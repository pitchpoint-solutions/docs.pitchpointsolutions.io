---
title: Authentication
layout: default
nav_order: 4
---

# Webhook Authentication Guide

For security purposes, Ptichpoint highly recommends that you validate the authenticity of the webhook event before processing.

If you are unfamiliar with JWTs, view [JWT Primer](/webhooks/jwt) for more details. 


### Verifying Webhook Authenticity
In Pitchpoint, the JWT to be validated will come in the HTTP Request as a "Authorization" header.   

For example:

```
"Authorization" : "Bearer eyJhbGciOiJSUzI1NiIsImtpZCI6ImExODE4ZjQ0ODk0MjI1ZjQ2MWQyMmI1NjA4NDcyMDM3MTc2MGY1OWIiLCJ0eXAiOiJKV1QifQ.eyJlbWFpbF92ZXJpZmllZCI6dHJ1ZSwiZW1haWwiOiJ1c2VyQHN5c3RlbS5jb20iLCJpc3MiOiJodHRwczovL3NlY3VyZXRva2VuLmdvb2dsZS5jb20vcHBzLWxvY2FsLWRldi0zNzYwMjIiLCJhdWQiOiJwcHMtbG9jYWwtZGV2LTM3NjAyMiIsImF1dGhfdGltZSI6MTcwODcxNTYwMiwidXNlcl9pZCI6InhwcHN8VWEiLCJzdWIiOiJ4cHBzfFVhIiwiaWF0IjoxNzA4NzE1NjAyLCJleHAiOjE3MDg3MTkyMDIsImZpcmViYXNlIjp7ImlkZW50aXRpZXMiOnt9LCJzaWduX2luX3Byb3ZpZGVyIjoiY3VzdG9tIn19.VGRsiUyxjld3B2KfOsvl8wT6LdTqnG0Z5nMR4sL8qmZUrTotRbTWnFTBp6l-RW_g4teKE2_QKuDQEpu3GYBpDtZw_AmdwjvkLVlqg_2vFoSRPnLDKCkBxsnoDp9nkG8q1opkToE8SbxsBprhiHWT4GZX8_YbPLjmb7JaxvGJJoO35J5354OjiSF6yQez0uL3F5yvbkb8MJl25hvD1GKe6lSurojXQFWHsMO7OmFem8qVPO_LSLOjXWn7LyjGfeCuXwOrFN-flkUoALt6ZV3zOYvXMjQ3OO3uSZffdq8eZkmbIdZE8tJ2yLpGUGrJICOqnVgY5fdmUGUc3C7j78oW7w","refresh_token":"AMf-vBzjizyIKhWV2b0Aa2geUrXCpvzHDCw9QQUKuZalC-IUGYtfxo6YPH-RD-l9Lkqe82Thn34r2Lz-quE0pUBcheVUBkVieMjsyMUBeTUceTfRVKnf_K_ziN_HzkP-tqsbFCu-iUT1oBBNwCyCBbCtaJXQXji5gVXGwIOYIAScGnEklqYeB3KF1i45dqynyOCnBFwe2Y_PYbTI4vX6e_OlYjclvquiCilBgTaS8GFTbcvoP_L3-vs" 
```

The first part `Bearer` can be discarded.  The send part (`eyJhbGc....voP_L3-vs`) is the JWT token to be validated. 

### Validation 
When validating the JWT, key points to validate are:  
* the issuer is a issuer that you expect 
* the audience is an audience that you expect for that issuer 
* the token is not expired (`exp`) 
* the signature is valid

If any of the above points are invalid, your service should return a `403 Forbidden` status code.


### Also, keep in mind: 
A JWKS (JSON Web Key Set) is not stagnant and is periodically updated.  Therefore, to reduce network overhead and improve performance, you should cache the results and refresh it periodically. (Suggested: hourly)  

## Sample Python Implementation
### Prerequisites
- JWT token to validate.
- `Authlib` and `cachetools` libraries installed in your Python environment. Install them via `pip install Authlib cachetools`.

### Caching JWKS
To avoid fetching the JWKS from the issuer every time a token needs to be validated, implement caching with a timed expiration.

### Validating the JWT
If (the token is expired), (the issuer is not in the trusted list) or (the audience is not valid for the trusted issuer), the `jwt.decode` method will raise an exception. Once this exception is raised, your endpoint should return a `403` status code without processing the body of the webhook.


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
        # do more processing 
        return 200
    } catch (ValueError){
        return 403
    }
```
