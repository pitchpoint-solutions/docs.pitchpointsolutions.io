---
layout: default
title: Webhook Endpoint Implementation Guide
---

# Webhook Endpoint Implementation Guide

## Overview
Your system needs to implement a webhook endpoint to receive real-time notifications from our service. Each webhook message includes several headers providing additional context about the event.

## Webhook Message Headers
When your endpoint receives a webhook, it will contain the following headers:

- **X-Pps-Topic**: The name of the webhook event (e.g., `orders/placed`).
- **X-Pps-Hmac-Sha256**: A HMAC hash of the message body, generated using your client secret. Use this to verify the message's integrity and authenticity.
- **X-Pps-Tenant-Id**: The unique identifier for the organizational unit that generated the event (e.g., `OU1243`).
- **X-Pps-Webhook-Id**: A unique identifier for the webhook event (e.g., `279e4e55-dfa0-4e04-b717-148ae547ab7d`). Your endpoint might receive the same webhook event more than once; use this ID to detect duplicates.
- **X-Pps-Triggered-At**: The UTC timestamp when the event was triggered (e.g., `2024-01-1T10:00:00.7777748Z`).

## Handling Webhooks
### Verifying Webhook Authenticity
1. Compute the HMAC SHA-256 hash of the incoming message body using your client secret.
2. Compare this hash with the value in the `X-Pps-Hmac-Sha256` header. If they match, the webhook is authentic.

### Processing Webhook Events
- Parse the webhook body to retrieve the event data.
- Use the `X-Pps-Topic` header to determine the type of event and process accordingly.
- Check the `X-Pps-Webhook-Id` to avoid processing duplicate events.

### Responding to Webhooks
- Your endpoint should respond with a `200 OK` status code after successfully processing the webhook.
- If your endpoint is unable to handle the webhook, respond with an appropriate error code (e.g., `500 Internal Server Error`).

## Example Code
Here's a simple example in Python to demonstrate how to handle and verify a webhook:

```python
import hashlib
import hmac

def verify_webhook(data, secret, hash_header):
    """Verify the webhook signature."""
    hash = hmac.new(secret.encode(), data.encode(), hashlib.sha256).hexdigest()
    return hash == hash_header

# Example usage
# Assume 'request' is your incoming webhook request
secret = 'your_client_secret'
data = request.data  # Webhook data
hash_header = request.headers['X-Pps-Hmac-Sha256']

if verify_webhook(data, secret, hash_header):
    print("Webhook verified")
else:
    print("Webhook verification failed")
```
