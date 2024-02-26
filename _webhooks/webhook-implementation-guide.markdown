---
title: Implementation
layout: default
nav_order: 4
---

# Webhook Endpoint Implementation Guide

## Overview
Your system needs to implement a webhook endpoint to receive real-time notifications from our service. Each webhook message includes several headers providing additional context about the event.

## Webhook Message Headers
When your endpoint receives a webhook, it will contain the following headers:

- **X-Pps-Topic**: The name of the webhook event (e.g., `orders/placed`).
- **X-Pps-Tenant-Id**: The unique identifier for the organizational unit that generated the event (e.g., `OU1243`).
- **X-Pps-Webhook-Id**: A unique identifier for the webhook event (e.g., `279e4e55-dfa0-4e04-b717-148ae547ab7d`). Your endpoint might receive the same webhook event more than once; use this ID to detect duplicates.
- **X-Pps-Triggered-At**: The UTC timestamp when the event was triggered (e.g., `2024-01-1T10:00:00.7777748Z`).

## Handling Webhooks
### Verifying Webhook Authenticity
See the [Authentication Guide](/webhooks/authentication)

### Processing Webhook Events
- Parse the webhook body to retrieve the event data.
- Use the `X-Pps-Topic` header to determine the type of event and process accordingly.
- Check the `X-Pps-Webhook-Id` to avoid processing duplicate events.

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
