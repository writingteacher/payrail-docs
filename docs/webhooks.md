---
sidebar_position: 4
---
# Webhooks

Payrail uses webhooks to notify your application when asynchronous events occur. Instead of polling the API for status updates, you can register a webhook endpoint to receive real-time event notifications.

---

## How webhooks work
```
Payrail API
     ↓
Event occurs (e.g. payment completed)
     ↓
Payrail sends POST request to your webhook URL
     ↓
Your server receives and processes the event
```

When an event occurs, Payrail sends an HTTP `POST` request to your registered webhook URL with a JSON payload describing the event.

---

## Event types

| Event | Description |
|-------|-------------|
| `payment.succeeded` | A transaction was successfully completed |
| `payment.failed` | A transaction was declined or failed |
| `payment.pending` | A transaction has been created and is awaiting processing |
| `refund.processed` | A refund was successfully processed |
| `refund.rejected` | A refund request was rejected |
| `customer.created` | A new customer was registered |
| `payment_method.added` | A new payment method was added to a customer |

---

## Webhook payload

All webhook events share the same payload structure.

| Field | Type | Description |
|-------|------|-------------|
| `event` | string | The event type (e.g. `payment.succeeded`) |
| `created_at` | string | ISO 8601 timestamp of when the event occurred |
| `data` | object | The resource object associated with the event |

**Example payload — payment.succeeded**
```json
{
    "event": "payment.succeeded",
    "created_at": "2026-03-14T10:22:08.804Z",
    "data": {
        "_id": "69b536d0bcfe7db4d09c1403",
        "customer": "69b5330a4314418540e8676e",
        "paymentMethod": "69b53559c707c55a4e351409",
        "amount": 15000,
        "currency": "USD",
        "status": "completed",
        "description": "Payment for services",
        "createdAt": "2026-03-14T10:22:08.804Z"
    }
}
```

**Example payload — payment.failed**
```json
{
    "event": "payment.failed",
    "created_at": "2026-03-14T10:22:08.804Z",
    "data": {
        "_id": "69b536d0bcfe7db4d09c1403",
        "customer": "69b5330a4314418540e8676e",
        "paymentMethod": "69b53559c707c55a4e351409",
        "amount": 15000,
        "currency": "USD",
        "status": "failed",
        "description": "Payment for services",
        "createdAt": "2026-03-14T10:22:08.804Z"
    }
}
```

**Example payload — refund.processed**
```json
{
    "event": "refund.processed",
    "created_at": "2026-03-14T10:24:20.638Z",
    "data": {
        "_id": "69b537548386681aea83bfd2",
        "customer": "69b5330a4314418540e8676e",
        "transaction": "69b536d0bcfe7db4d09c1403",
        "amount": 15000,
        "reason": "Customer requested refund",
        "status": "processed",
        "createdAt": "2026-03-14T10:24:20.638Z"
    }
}
```

---

## Responding to webhooks

Your webhook endpoint must return a `200 OK` response within 5 seconds to acknowledge receipt. If Payrail does not receive a `200` response, it will retry the webhook up to 3 times with exponential backoff.

**Example response**
```json
{
    "received": true
}
```

---

## Retry policy

| Attempt | Delay |
|---------|-------|
| 1st retry | 5 minutes |
| 2nd retry | 30 minutes |
| 3rd retry | 2 hours |

After 3 failed attempts the event is marked as undelivered.

---

## Security

Payrail signs all webhook payloads with a secret key. Verify the signature on your server to confirm the request came from Payrail and has not been tampered with.

Include your webhook secret in the `X-Payrail-Signature` header verification:
```
X-Payrail-Signature: sha256=abc123...
```

Always verify this signature before processing the event.

---

## Related guides

- [Payment Workflow](payment-workflow.md)
- [API Reference](api-reference.md)
- [Getting Started](quickstart.md)