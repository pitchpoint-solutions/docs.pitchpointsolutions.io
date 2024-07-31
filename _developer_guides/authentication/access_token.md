---
title: Obtaining An Access Token
layout: dev_guide
parent: Authentication
nav_order: 1
---

Stable
{: .label .label-green }
# Obtaining Authentication Tokens
{:.no_toc}

* TOC 
{: toc}

## Overview
Before you can access the system, you must obtain an access token for the pointservices platform.


### Step 1: Authenticate and Receive Tokens

Use the following command to authenticate with your credentials and receive your token. Replace `customerUsername` and `customerPassword` with your actual username and password.

```bash
curl -X POST https://api.pointservices.com/user-management-services-ws/oauth2/002/signInWithPassword \
-H "Content-Type: application/json" \
-d '{
  "username": "customerUsername",
  "password": "customerPassword"
}'
```

The response will be a json structure that includes:
- `access_token`: Used for authenticated requests to the webhook testing endpoint.
- `refresh_token`: Used to obtain a new access token once the current one expires.
- `expires_in`: The time in seconds until the access token expires.

Read the response to obtain the new `access_token`, `expires_in` and `refresh_token` values. The `expires_in` value will in most cases be 3600 seconds (1 hour) from the time the new access token was issued.


{: .api_ref }
[Sign In With Password API Reference](/api/authentication/signinwithpassword)

### Example response:
{:.no_toc}

```json
{
  "access_token": "eyJraWQiOiIxMjM0NSIsInR5cCI6IkpXVCIsImFsZyI6IlJTMjU2In0.eyJhdWQiOiJ0ZXN0LXRlbmFudCIsInN1YiI6InhwcHN8MTIyMyIsImVtYWlsX3ZlcmlmaWVkIjp0cnVlLCJ1c2VyX2lkIjoiMTIyMyIsImF1dGhfdGltZSI6MTcwODcxNTYwMiwiaXNzIjoiaHR0cHM6XC9cL3NlY3VyZXRva2VuLmdvb2dsZS5jb21cL3Rlc3QtdGVuYW50IiwiZXhwIjoxNzA4NzE5LCJpYXQiOjE3MDg3MTUsImVtYWlsIjoidGVzdHVzZXJAdGVuYW50LmNvbSJ9.dGVzdA==",
  "refresh_token": "AMf-vBxITbnfBBMJpPXMvop1qa1cMaUuqAKX_y1hYsmcgVtzhx7Al_9mWD",
  "expires_in": 3600
}
```
### Step 2: Send a request using your access token

Incorporate the access token in the `Authorization` header and send a test request as follows:

```bash
curl -X POST https://api.pointservices.com/...
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json" 
-d '...'
```

### Step 3: Refresh your access token

The access token is valid for 3600 seconds (1 hour) from the time it was issued. If you need to refresh the token, use the following command:

```bash
curl -X POST https://api.pointservices.com/user-management-services-ws/oauth2/002/refreshIdToken \
-H "Content-Type: application/json" \
-d '{"refresh_token":"AMf-vBxITbnfBBMJpPXMvop1qa1cMaUuqAKX_y1hYsmcgVtzhx7Al_9mWD""}'
```

Refresh tokens are valid for 24 hours rounded up to midnight eastern time. The refresh token should be reused to fetch to access tokens until the refresh token expires. Once the refresh token expires, you will need to authenticate again using `/user-management-services-ws/oauth2/002/signInWithPassword` api again.

{: .api_ref }
[Refresh Access Token API Reference](/api/authentication/refreshidtoken)

