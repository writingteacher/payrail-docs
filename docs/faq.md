---
sidebar_position: 9
---
# Frequently Asked Questions

---

## General

### What is Payrail?

Payrail is a fintech payments REST API that enables developers to manage customers, payment methods, transactions, and refunds. It is built with Node.js, Express, and MongoDB.

### Is Payrail production-ready?

Payrail is a portfolio project built to demonstrate fintech API design and documentation best practices. It is not intended for production use.

### What is the base URL?
```
https://payrail-api.onrender.com
```

---

## Authentication

### How do I get a token?

Send a `POST` request to `/api/auth/register` or `/api/auth/login` with your email and password. A JWT token is returned in the response.

### How long does a token last?

Tokens expire after 7 days. After expiry, log in again to get a new token.

### What happens if I send a request without a token?

The API returns a `401 Unauthorized` response:
```json
{
    "error": {
        "type": "authentication_error",
        "code": "authentication_required",
        "message": "Authentication required"
    }
}
```

### Can I use the same token for all requests?

Yes — a single token authenticates all protected endpoints.

---

## Payments

### Why is the amount in cents?

Payrail stores amounts in minor units (cents) to avoid floating point rounding errors. For example, `15000` = $150.00. This is standard practice in fintech APIs including Stripe and PayPal.

### What currencies does Payrail support?

Payrail accepts any 3-letter ISO 4217 currency code such as `USD`, `EUR`, or `GBP`. The default currency is `USD`.

### What does a `pending` transaction status mean?

A `pending` status means the transaction has been created and is awaiting processing by the payment processor. The status will update to `completed` or `failed` asynchronously.

### Can I manually set a transaction status?

No — transaction statuses are system-driven and follow a strict lifecycle. Statuses reflect the actual state of the payment and cannot be set arbitrarily.

### What is an idempotency key and why do I need one?

An idempotency key is a unique string you include in the `Idempotency-Key` header of every `POST` request. If a request fails due to a network error and you retry it with the same key, Payrail returns the original response rather than creating a duplicate transaction.

Always use a UUID or other unique string as your idempotency key.

### What happens if I retry a failed transaction with the same idempotency key?

If you retry a failed transaction with the same idempotency key, Payrail returns the original failed response. To retry a failed payment, create a new transaction with a new idempotency key.

---

## Refunds

### How long do refunds take?

Refunds typically take 3 to 5 business days to process.

### Can I refund a partial amount?

Yes — set the `amount` field in the refund request to the partial amount in minor units.

### Can I refund a failed transaction?

No — only `completed` transactions can be refunded.

---

## Errors

### What does a `409 Conflict` error mean?

A `409 Conflict` error means you submitted a request with an idempotency key that was already used with different parameters. Use a new idempotency key.

### What does a `429 Too Many Requests` error mean?

You have exceeded the rate limit of 100 requests per 15 minutes. Slow down your requests and try again after 15 minutes.

### Where can I find a full list of error codes?

See the [API Reference](api-reference.md#error-responses) for a complete list of error types and codes.

---

## Webhooks

### Do I need to use webhooks?

Webhooks are optional but recommended. Without webhooks you need to poll `GET /api/transactions/:id` to check for status updates. With webhooks, Payrail notifies your server automatically when an event occurs.

### What happens if my webhook endpoint is down?

Payrail retries failed webhook deliveries up to 3 times with exponential backoff. If all retries fail the event is marked as undelivered.

---

## Related guides

- [Getting Started](quickstart)
- [Authentication](authentication)
- [Payment Workflow](payment-workflow)
- [Webhooks](webhooks)
- [API Reference](api-reference)