---
title: Authentication
layout: default
nav_order: 4
---

# Webhook Authentication Guide


### Verifying Webhook Authenticity
A JWT in the Authorization header of the request allows you to validate that the request originated from the pointservies platform. The JWT includes claims and a signature.

The JWT is an [OpenIDConnect JWT](https://openid.net/specs/draft-jones-json-web-token-07.html) that consists of a header, claim set, and signature. The webhook service encodes the JWT as a base64 string with period delimiters.

For example, the following authorization header includes an encoded JWT:

```
"Authorization" : "Bearer eyJhbGciOiJSUzI1NiIsImtpZCI6ImExODE4ZjQ0ODk0MjI1ZjQ2MWQyMmI1NjA4NDcyMDM3MTc2MGY1OWIiLCJ0eXAiOiJKV1QifQ.eyJlbWFpbF92ZXJpZmllZCI6dHJ1ZSwiZW1haWwiOiJ1c2VyQHN5c3RlbS5jb20iLCJpc3MiOiJodHRwczovL3NlY3VyZXRva2VuLmdvb2dsZS5jb20vcHBzLWxvY2FsLWRldi0zNzYwMjIiLCJhdWQiOiJwcHMtbG9jYWwtZGV2LTM3NjAyMiIsImF1dGhfdGltZSI6MTcwODcxNTYwMiwidXNlcl9pZCI6InhwcHN8VWEiLCJzdWIiOiJ4cHBzfFVhIiwiaWF0IjoxNzA4NzE1NjAyLCJleHAiOjE3MDg3MTkyMDIsImZpcmViYXNlIjp7ImlkZW50aXRpZXMiOnt9LCJzaWduX2luX3Byb3ZpZGVyIjoiY3VzdG9tIn19.VGRsiUyxjld3B2KfOsvl8wT6LdTqnG0Z5nMR4sL8qmZUrTotRbTWnFTBp6l-RW_g4teKE2_QKuDQEpu3GYBpDtZw_AmdwjvkLVlqg_2vFoSRPnLDKCkBxsnoDp9nkG8q1opkToE8SbxsBprhiHWT4GZX8_YbPLjmb7JaxvGJJoO35J5354OjiSF6yQez0uL3F5yvbkb8MJl25hvD1GKe6lSurojXQFWHsMO7OmFem8qVPO_LSLOjXWn7LyjGfeCuXwOrFN-flkUoALt6ZV3zOYvXMjQ3OO3uSZffdq8eZkmbIdZE8tJ2yLpGUGrJICOqnVgY5fdmUGUc3C7j78oW7w","refresh_token":"AMf-vBzjizyIKhWV2b0Aa2geUrXCpvzHDCw9QQUKuZalC-IUGYtfxo6YPH-RD-l9Lkqe82Thn34r2Lz-quE0pUBcheVUBkVieMjsyMUBeTUceTfRVKnf_K_ziN_HzkP-tqsbFCu-iUT1oBBNwCyCBbCtaJXQXji5gVXGwIOYIAScGnEklqYeB3KF1i45dqynyOCnBFwe2Y_PYbTI4vX6e_OlYjclvquiCilBgTaS8GFTbcvoP_L3-vs" 
```

The header and claim set are JSON strings. Once decoded, they take the following form:

```json
{
  "alg": "RS256",
  "kid": "a1818f44894225f461d22b56084720371760f59b",
  "typ": "JWT"
}

{
  "email_verified": true,
  "email": "user@system.com",
  "iss": "https://securetoken.google.com/pps-local-dev-376022",
  "aud": "pps-local-dev-376022",
  "auth_time": 1708715602,
  "user_id": "xpps|Ua",
  "sub": "xpps|Ua",
  "iat": 1708715602,
  "exp": 1708719202,
  "firebase": {
    "identities": {},
    "sign_in_provider": "custom"
  }
}
```  

When validating a JWT from an OpenID Connect (OIDC) compliant issuer, it's efficient to cache the JWKS (JSON Web Key Set) and refresh it at a set interval, such as hourly, to reduce network overhead and improve performance.

## Prerequisites
- JWT token to validate.
- `Authlib` and `cachetools` libraries installed in your Python environment. Install them via `pip install Authlib cachetools`.

## Caching JWKS
To avoid fetching the JWKS from the issuer every time a token needs to be validated, implement caching with a timed expiration.

## Validating the JWT
If the token is expired, the issuer is not in the trusted list or the audience is not valid for the trusted issuer, the `jwt.decode` method will raise an exception. Once this exception is raised, your endpoint should return a `403` status code without processing the body of the webhook.

### Responding to Webhooks
- Your endpoint should respond with a `200 OK` status code after successfully processing the webhook.
- If your endpoint is unable to authorize the webhook, respond with a `403 Forbidden` status code.
- If your endpoint is unable to handle the webhook, respond with an appropriate error code (e.g., `500 Internal Server Error`).

## Example Code
Here's a simple example in Python to demonstrate how to verify and handle a webhook:

```python
import hashlib
from authlib.jose import jwt
from authlib.oidc.core import CodeIDToken
import requests
from cachetools import TTLCache
from flask import Flask
from flask import request

app = Flask(__name__)

# Trusted OIDC issuers
trusted_issuers = {
    'https://securetoken.google.com/pps-local-dev-376022': ['pps-local-dev-376022'],
    'https://issuer2.example.com': None,
    # Add more trusted issuers as needed
}

# JWKS Cache setup - cache will expire after 3600 seconds (1 hour)
jwks_cache = TTLCache(maxsize=10, ttl=3600)

def fetch_jwks(issuer):
    if issuer not in trusted_issuers:
        raise ValueError("Issuer not trusted")

    if issuer not in jwks_cache:
        # Fetch OpenID Configuration
        config_url = f'{issuer}/.well-known/openid-configuration'
        oidc_config = requests.get(config_url).json()

        # Fetch JWKS
        jwks_url = oidc_config['jwks_uri']
        jwks_cache[issuer] = requests.get(jwks_url).json()

    return jwks_cache[issuer]

def verify_id_token(data, secret, id_token):
    # JWT Token to validate
    
    # Decode token without verification to extract issuer
    unverified_claims = jwt.decode(id_token, options={"verify_signature": False})
    issuer = unverified_claims.get('iss')
    
    if not issuer:
        raise ValueError("Issuer claim not found in JWT")
    
    # Fetch JWKS for the issuer
    jwks = fetch_jwks(issuer)
    
    # Decode and validate the token
    claims_options = {'iss': {'essential': True, 'value': issuer}, 'aud': {'essential': True, 'value': trusted_issuers[issuer]}, }
    decoded = jwt.decode(id_token, jwks, claims_options=claims_options)
    
    return decoded

@app.route('/', methods=['POST'])
def hello_world():
    authorization_header = request.headers['Authorization']
    a, id_token = authorization_header.split(maxsplit=1)
    
    try{
        jwt_claims = verify_id_token(id_token)
        print("Webhook verified")
        return 200
    } catch (ValueError){
        return 403
    }
```
