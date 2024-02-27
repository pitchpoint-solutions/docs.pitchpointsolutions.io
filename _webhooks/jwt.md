---
title: Authentication
layout: default
nav_order: 4
---

# JWT - JSON Web Tokens Primer

For Pitchpoint and Authorization purposes, Pitchpoint follows the [OpenIDConnect JWT](https://openid.net/specs/draft-jones-json-web-token-07.html) standard for implementing a JSON Web Token.

## JWT Components 

An example JWT will look like the following: 

```
eyJhbGciOiJSUzI1NiIsImtpZCI6ImExODE4ZjQ0ODk0MjI1ZjQ2MWQyMmI1NjA4NDcyMDM3MTc2MGY1OWIiLCJ0eXAiOiJKV1QifQ.eyJlbWFpbF92ZXJpZmllZCI6dHJ1ZSwiZW1haWwiOiJ1c2VyQHN5c3RlbS5jb20iLCJpc3MiOiJodHRwczovL3NlY3VyZXRva2VuLmdvb2dsZS5jb20vcHBzLWxvY2FsLWRldi0zNzYwMjIiLCJhdWQiOiJwcHMtbG9jYWwtZGV2LTM3NjAyMiIsImF1dGhfdGltZSI6MTcwODcxNTYwMiwidXNlcl9pZCI6InhwcHN8VWEiLCJzdWIiOiJ4cHBzfFVhIiwiaWF0IjoxNzA4NzE1NjAyLCJleHAiOjE3MDg3MTkyMDIsImZpcmViYXNlIjp7ImlkZW50aXRpZXMiOnt9LCJzaWduX2luX3Byb3ZpZGVyIjoiY3VzdG9tIn19.VGRsiUyxjld3B2KfOsvl8wT6LdTqnG0Z5nMR4sL8qmZUrTotRbTWnFTBp6l-RW_g4teKE2_QKuDQEpu3GYBpDtZw_AmdwjvkLVlqg_2vFoSRPnLDKCkBxsnoDp9nkG8q1opkToE8SbxsBprhiHWT4GZX8_YbPLjmb7JaxvGJJoO35J5354OjiSF6yQez0uL3F5yvbkb8MJl25hvD1GKe6lSurojXQFWHsMO7OmFem8qVPO_LSLOjXWn7LyjGfeCuXwOrFN-flkUoALt6ZV3zOYvXMjQ3OO3uSZffdq8eZkmbIdZE8tJ2yLpGUGrJICOqnVgY5fdmUGUc3C7j78oW7w 
```

It has 3 parts delimited by (`.`) -- a header, a payload, and signature.

### Header 

The header portion (first section) is a base64URL encoded string.  

```
eyJhbGciOiJSUzI1NiIsImtpZCI6ImExODE4ZjQ0ODk0MjI1ZjQ2MWQyMmI1NjA4NDcyMDM3MTc2MGY1OWIiLCJ0eXAiOiJKV1QifQ
```

Decoded it will typically look like: 

```json
{
  "alg": "RS256",
  "kid": "a1818f44894225f461d22b56084720371760f59b",
  "typ": "JWT"
}
```

In an OIDC Compliant JWT, the header will have `alg` (algorithm used for the signature) and a `kid` (the id of the key used to sign the token, should there be more than one available key to decode).  

### Payload

The payload (second section) is a base64URL encoded string. 

```
eyJlbWFpbF92ZXJpZmllZCI6dHJ1ZSwiZW1haWwiOiJ1c2VyQHN5c3RlbS5jb20iLCJpc3MiOiJodHRwczovL3NlY3VyZXRva2VuLmdvb2dsZS5jb20vcHBzLWxvY2FsLWRldi0zNzYwMjIiLCJhdWQiOiJwcHMtbG9jYWwtZGV2LTM3NjAyMiIsImF1dGhfdGltZSI6MTcwODcxNTYwMiwidXNlcl9pZCI6InhwcHN8VWEiLCJzdWIiOiJ4cHBzfFVhIiwiaWF0IjoxNzA4NzE1NjAyLCJleHAiOjE3MDg3MTkyMDIsImZpcmViYXNlIjp7ImlkZW50aXRpZXMiOnt9LCJzaWduX2luX3Byb3ZpZGVyIjoiY3VzdG9tIn19
```

Decoded it will typically look like: 
```json
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

In an OIDC compliant JWT, the fields most pertinent to authentication are the `iss` (issuer), the `aud` (audience) and `exp` (expiration time of token -- defined as seconds since epoc).  

### Signature 

The signature (third section) which should be used to validate the authenticity of JWT.  It is created by: 

```
RSA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  private_key)
```

In an OIDC compliant JWT, with a OIDC compliant issuer, you should be able to go visit the URL: `${issuer}/.well-known/openid-configuration` which will return a json response. 

https://securetoken.google.com/pps-local-dev-376022/.well-known/openid-configuration 
```json 
{
  "issuer": "https://securetoken.google.com/pps-local-dev-376022",
  "jwks_uri": "https://www.googleapis.com/service_accounts/v1/jwk/securetoken@system.gserviceaccount.com",
  "response_types_supported": [
    "id_token"
  ],
  "subject_types_supported": [
    "public"
  ],
  "id_token_signing_alg_values_supported": [
    "RS256"
  ]
}
```

`jwks_uri` is the URL where to go to get the public key to validate the signature.  

In the previous example, visiting https://www.googleapis.com/service_accounts/v1/jwk/securetoken@system.gserviceaccount.com will return the following JWKS (JSON Web Key Set): 
```json
{
  "keys": [
    {
      "use": "sig",
      "e": "AQAB",
      "kty": "RSA",
      "kid": "a1818f44894225f461d22b56084720371760f59b",
      "n": "4bl2UaQY-xoIC0yXo0BKClpEq8LjdljLK817KtuEasnKRcC2-xlwPrY2jfYtEbAV2Fmg5P-Wu6F1o8YSL1VoGwGE4mG0opPO5_p5ZrrQ13cciqy1LiED0o5nveD9pfmZ8on_U8vtvkVVtX2aHPkG9YrGGuSZO_kMAw0E7WkDx9mqL6LKzva_48-8f4T3gfCGc4PRjRuqC9hcSKW_to2-mRmn3AcauQ-fVcFopfKHqVRHvqUqWsrh_u4QxR9oi-ormlz04tJOEyL4zKDpcOEHz-y37oopcVf6gh35CnEMJnunxICVoeyLR_abCmjLbdfDUgEKwKV7YiuO7t5G_blAuw",
      "alg": "RS256"
    },
    {
      "n": "t6O34UHBBc2PAN3gGFBjjSDLGWyylU4-f3c5E0CCwMax1BE_VC0D-VQhNg5xRPq_TAsiAePlYs5WEDncCCh2BYUupPClvdeA9WvVjUEA-l_i_l3n6VP3ecs7LcuLnhCUhegpTjJcDIKOPjyG80Mu0Wem7lNiUzmkLOXJ7MoG6RBnRtu4OxCC0cTDTph0Ga2o2izTK08NX3YLIib_tssyje311V90luaCND9PaRwhL-biAEMOQetpPncebhmP_lqdFmD_Wr5idIu9Qhl9DsFqNBsznFrHraoTFYTMJ73rXu6uetpzK1F3p0PdDNFov87ernW4cobP8BTkKkP3V8A1WQ",
      "alg": "RS256",
      "kty": "RSA",
      "e": "AQAB",
      "kid": "3bb87dca3bcb69d72b6cba1b59b33cc52297ca8d",
      "use": "sig"
    }
  ]
}
```

A JWKS is a set of (public) keys published by the Issuer.  This set is periodically refreshed as new keys are rotated in and old ones are rotated out.  One of the `kid` (key_ids) will match the `kid` specified in the header.  This is the key that should be used to validate the signature. 

## Windup 
It may seem like it's a big job to validate a JWT token, but in reality, because it is a well-defined, standard process, there are many reputable libraries available to make this process easier.  See [Authentication Guide](/webhooks/authentication) for more validation details and an example implementation.   