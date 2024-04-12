---
title: Testing
layout: dev_guide
parent: Webhooks
nav_order: 3
---
Coming soon
{: .label .label-yellow }
# Webhook Testing Guide for Developers

This guide provides step-by-step instructions for developers on how to authenticate and test webhook integrations with our service. The process includes obtaining authentication tokens, sending a test webhook request, and refreshing your tokens as needed.

## Obtaining Authentication Tokens

Before you can test your webhook listener, you must obtain an access token for the pointservices platform.

### Step 1: Authenticate and Receive Tokens

See the [Authentication Guide](/authentication/access_token) for more information on how to authenticate and receive your token.

## Testing the Webhook Endpoint

With the access token, you're now ready to send a test request to our webhook endpoint testing endpoint.

### Step 2: Send a Test Webhook Request

Incorporate the access token in the `Authorization` header and send a test request as follows:

```bash
curl -X POST https://yourapi.com/test-webhook 
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json" 
-H "X-Pps-Topic: orders/placed" 
-H "X-Pps-Sub-Topic: product:adv/adv-120" 
-H "X-Pps-Tenant-Id: OU1243" 
-H "X-Pps-Webhook-Id: 279e4e55-dfa0-4e04-b717-148ae547ab7d" 
-H "X-Pps-Triggered-At: 2024-01-1T10:00:00.7777748Z" 
-d '{
  "address": "https://customerapi.com/receive-webhook",
  "testData": {
    "message": "This is a test message",
    "timestamp": "2024-02-28T12:34:56Z",
    "order_id": "12345"
  }
}'
```

Replace `your_access_token_here` with the actual token you received in the `access_token` field from the authentication process.

Replace header values with the actual values you want to test. The `X-Pps-Topic` and `X-Pps-Sub-Topic` headers are used to simulate different types of events. The `X-Pps-Webhook-Id` header is used to detect duplicate events. The `X-Pps-Triggered-At` header is the UTC timestamp when the event was triggered.

The `address` field in the json body is the URL where your system will receive the webhook events. The `address` must be a publicly accessible `https` URL. If you wish to test against your local development environment register a public URL using a service like [PageKite](https://pagekite.net/) or [ngrok](https://ngrok.com/).

The `testData` field in the json body is the data you want to test. The structure and content of the `testData` field can be any structure you would like to test and will be sent as the http body to the address you provided.

### Step 3: Receive the event
```javascript
const express = require('express');
const jwt = require('express-jwt');
const jwksRsa = require('jwks-rsa');

const app = express();
const port = 3000;

// Trusted issuer and audience
const trustedIssuer = 'https://securetoken.google.com/test-tenant';
const audience = 'test-tenant';

// Middleware to validate the JWT Bearer token
const checkJwt = jwt({
  secret: jwksRsa.expressJwtSecret({
    cache: true, // Enable caching of JWKs
    rateLimit: true,
    jwksRequestsPerMinute: 5, // Prevent attackers from requesting JWKs too frequently
    jwksUri: `${trustedIssuer}/.well-known/jwks.json`
  }),
  audience: audience,
  issuer: trustedIssuer,
  algorithms: ['RS256']
});

// Placeholder for business logic processing
function processBusinessLogic(req, res) {
  // Implement your business logic here
  console.log("Processing business logic for user:", req.user.sub);
  console.log("Webhook data order id:", req.body.order_id);
  res.send("Business logic processed successfully");
}

// Your API endpoint that requires JWT validation
app.post('/receive-webhook', checkJwt, (req, res) => {
  processBusinessLogic(req, res);
});

// Error handling for unauthorized access
app.use((err, req, res, next) => {
  if (err.name === 'UnauthorizedError') {
    res.status(403).send('Invalid token');
  }
});

app.listen(port, () => {
  console.log(`Server listening at http://localhost:${port}`);
});
```

## Refreshing Your Token

When your access token expires, use your refresh token to request a new one. In this example the `expires_in` field from the authentication response is 3600 seconds (1 hour). 1 minute before an access token expires, use the refresh token to obtain a new access token.

### Step 4: Obtain a New Access Token

Use the refresh token to obtain a new access token with the following command:

```bash
curl -X POST https://api.pointservices.com/user-management-services-ws/oauth2/002/refreshIdToken \
-H "Content-Type: application/json" \
-d '{
  "refresh_token": "your_refresh_token_here"
}'
```

Replace `your_refresh_token_here` with the actual refresh token in the `refresh_token` field you received initially. The response will provide a new `access_token`.

Read the response to obtain the new `access_token` and `expires_in` values. The `expires_in` value will be 3600 seconds (1 hour) from the time the new access token was issued. 

Example response:

```json
{
  "access_token": "eyJraWQiOiIxMjM0NSIsInR5cCI6IkpXVCIsImFsZyI6IlJTMjU2In0.eyJhdWQiOiJ0ZXN0LXRlbmFudCIsInN1YiI6InhwcHN8MTIyMyIsImVtYWlsX3ZlcmlmaWVkIjp0cnVlLCJ1c2VyX2lkIjoiMTIyMyIsImF1dGhfdGltZSI6MTcwODcxNTYwMiwiaXNzIjoiaHR0cHM6XC9cL3NlY3VyZXRva2VuLmdvb2dsZS5jb21cL3Rlc3QtdGVuYW50IiwiZXhwIjoxNzA4NzE5LCJpYXQiOjE3MDg3MTUsImVtYWlsIjoidGVzdHVzZXJAdGVuYW50LmNvbSJ9.dGVzdA==",
  "refresh_token": "AMf-vBxITbnfBBMJpPXMvop1qa1cMaUuqAKX_y1hYsmcgVtzhx7Al_9mWD",
  "expires_in": 3600
}
```
