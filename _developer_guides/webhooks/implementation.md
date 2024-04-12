---
title: Implementation
layout: dev_guide
parent: Webhooks
nav_order: 1
---
Coming soon
{: .label .label-yellow }
# Webhook Endpoint Implementation Guide

## Overview
Your system needs to implement a webhook endpoint to receive real-time notifications from our services. Each webhook message includes several headers providing additional context about the event.

## Registering an Endpoint
Contact your account representative to register your webhook endpoint. You will need to provide the following information:
- **Endpoint URL**: The URL where your system will receive webhook events.
- **Webhook Topics**: The types of events you want to receive (e.g., `orders/placed`, `orders/updated`).
- **Webhook Sub Topics**: The subtypes of events you want to receive (e.g., `product:adv/adv-120`, `product:cdv/cdv-120`).
- **Tenant ID**: The unique identifier for the organizational unit that will generate the events.

## Webhook Message Headers
When your endpoint receives a webhook, it will contain the following headers:

- **X-Pps-Topic**: The name of the webhook event (e.g., `orders/placed`).
- **X-Pps-Sub-Topic**: The subtype of the webhook event (e.g., `product:adv/adv-120`).
- **X-Pps-Tenant-Id**: The unique identifier for the organizational unit that generated the event (e.g., `OU1243`).
- **X-Pps-Webhook-Id**: A unique identifier for the webhook event (e.g., `279e4e55-dfa0-4e04-b717-148ae547ab7d`). Your endpoint might receive the same webhook event more than once; use this ID to detect duplicates.
- **X-Pps-Triggered-At**: The UTC timestamp when the event was triggered (e.g., `2024-01-1T10:00:00.7777748Z`).

## Handling Webhooks
### Verifying Webhook Authenticity
See the [Verification Guide](/webhooks/verification)

### Processing Webhook Events
- Use the `X-Pps-Topic` header to determine the type of event and process accordingly.
- Check the `X-Pps-Webhook-Id` to avoid processing duplicate events.
- Parse the webhook body to retrieve the event data.

### Responding to Webhooks
- Your endpoint should respond with a `200 OK` status code after successfully processing the webhook.
- If your endpoint is unable to authorize the webhook, respond with a `403 Forbidden` status code.
- If your endpoint is unable to handle the webhook, respond with an appropriate error code (e.g., `500 Internal Server Error`).
- If your endpoint returns a status code >= `500`, the data will be resent using an exponential backoff algorithm.

## Example Code
Here's a simple example that demonstrates how to verify and process a webhook request:

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
