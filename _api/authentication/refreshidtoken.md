---
title: Refresh Access Token
layout: default
nav_order: 3
parent: Authentication
datatable: true
---
Stable
{: .label .label-green }

## Refresh Id Token

Refresh your access token.  Pitchpoint suggests that you use this call rather than signing-in again as this call returns marginally faster than the sign-in process.    

```shell
curl -X POST https://api.pointservices.com/user-management-services-ws/oauth2/002/refreshIdToken \
-H "Content-Type: application/json" \
-d '{
  "refresh_token": "AMf-vBxITbnfBBMJpPXMvop1qa1cMaUuqAKX_y1hYsmcgVtzhx7Al_9mWD"
}'
```

#### Header Properties
<div class="datatable-begin"></div>

| Property | Value            | Required? |
|----------|------------------|-----------|
| Content-Type | application/json | true      |

<div class="datatable-end"></div>


#### Request Data Properties
<div class="datatable-begin"></div>

| Property | Description                                | Type | Default |
|----------|--------------------------------------------|------|---------|
| refresh_token | The refresh token receieved during sign-in | string | |

<div class="datatable-end"></div>



### Responses

200
{: .label .label-green }

<div class="datatable-begin"></div>

| Property      | Description                                                | Type   | Default |
|---------------|------------------------------------------------------------|--------|---------|
| access_token  | Used for authenticated requests to the webhook testing endpoint. | string |         |
| refresh_token | Used to obtain a new access token once the current one expires. | string |         |
| scope         | The scope of the token                                     | string |         |
| token_type    | The token type                           | string | Bearer  |
| expires_in    | The time in seconds until the access token expires.        | number | 3600    |

<div class="datatable-end"></div>

Example:
```json
{
  "access_token": "eyJraWQiOiIxMjM0NSIsInR5cCI6IkpXVCIsImFsZyI6IlJTMjU2In0.eyJhdWQiOiJ0ZXN0LXRlbmFudCIsInN1YiI6InhwcHN8MTIyMyIsImVtYWlsX3ZlcmlmaWVkIjp0cnVlLCJ1c2VyX2lkIjoiMTIyMyIsImF1dGhfdGltZSI6MTcwODcxNTYwMiwiaXNzIjoiaHR0cHM6XC9cL3NlY3VyZXRva2VuLmdvb2dsZS5jb21cL3Rlc3QtdGVuYW50IiwiZXhwIjoxNzA4NzE5LCJpYXQiOjE3MDg3MTUsImVtYWlsIjoidGVzdHVzZXJAdGVuYW50LmNvbSJ9.dGVzdA==",
  "refresh_token": "AMf-vBxITbnfBBMJpPXMvop1qa1cMaUuqAKX_y1hYsmcgVtzhx7Al_9mWD",
  "scope": "policy",
  "token_type": "Bearer",
  "expires_in": 3600
}
```


400
{: .label .label-red }

403
{: .label .label-red }

<div class="datatable-begin"></div>

| Property | Description                           | Type | Default |
|----------|---------------------------------------|------|---------|
| error    | Error code                            | string | |
| error_description  | String description of what went wrong | string | |

<div class="datatable-end"></div>

Example:
```json
{
    "error_description": "Invalid username / password combination",
    "error": "access_denied"
}
```
