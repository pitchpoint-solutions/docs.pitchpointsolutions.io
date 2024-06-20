---
title: Verification
layout: dev_guide
parent: Webhooks
nav_order: 2
---
Coming soon
{: .label .label-yellow }
# Webhook Verification Guide


### Verifying Webhook Authenticity
A JWT in the Authorization header of the request allows you to validate that the request originated from the pointservies platform. The JWT includes claims and a signature.

The JWT is an [OpenIDConnect JWT](https://openid.net/specs/draft-jones-json-web-token-07.html) that consists of a header, claim set, and signature. The webhook service encodes the JWT as a base64 string with period delimiters.

For example, the following authorization header includes an encoded JWT:

```
"Authorization" : "Bearer eyJraWQiOiIxMjM0NSIsInR5cCI6IkpXVCIsImFsZyI6IlJTMjU2In0.eyJhdWQiOiJ0ZXN0LXRlbmFudCIsInN1YiI6InhwcHN8MTIyMyIsImVtYWlsX3ZlcmlmaWVkIjp0cnVlLCJ1c2VyX2lkIjoiMTIyMyIsImF1dGhfdGltZSI6MTcwODcxNTYwMiwiaXNzIjoiaHR0cHM6XC9cL3NlY3VyZXRva2VuLmdvb2dsZS5jb21cL3Rlc3QtdGVuYW50IiwiZXhwIjoxNzA4NzE5LCJpYXQiOjE3MDg3MTUsImVtYWlsIjoidGVzdHVzZXJAdGVuYW50LmNvbSJ9.dGVzdA==" 
```

The header and claim set are JSON strings. Once decoded, they take the following form:

```json
{
  "kid": "12345",
  "typ": "JWT",
  "alg": "RS256"
}

{
  "aud": "test-tenant",
  "sub": "xpps|1223",
  "email_verified": true,
  "user_id": "1223",
  "auth_time": 1708715602,
  "iss": "https://securetoken.google.com/test-tenant",
  "exp": 1708719,
  "iat": 1708715,
  "email": "testuser@tenant.com"
}
```

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
When validating a JWT from an OpenID Connect (OIDC) compliant issuer, it's efficient to cache the JWKS (JSON Web Key Set) and refresh it at a set interval, such as hourly, to reduce network overhead and improve performance.

## Caching JWKS
To avoid fetching the JWKS from the issuer every time a token needs to be validated, implement caching with a timed expiration.

## Validating the JWT
If the token is expired, the issuer is not in the trusted list or the audience is not valid for the trusted issuer, the `express-jwt` middleware will raise an exception that will be handled by the express error handler. Once this exception is raised, your endpoint should return a `403` status code without processing the body of the webhook.

### Responding to Webhooks
- Your endpoint should respond with a `200 OK` status code after successfully processing the webhook.
- If your endpoint is unable to authorize the webhook, respond with a `403 Forbidden` status code.
- If your endpoint is unable to handle the webhook, respond with an appropriate error code (e.g., `500 Internal Server Error`).
