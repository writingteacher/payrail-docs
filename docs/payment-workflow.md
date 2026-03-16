---
sidebar_position: 3
---
# Payment Workflow

Payrail processes payments through a structured workflow. Understanding this workflow helps you integrate correctly and handle edge cases like failures and refunds.

---

## System architecture
```
Client Application
        ↓
Payrail API
        ↓
Payment Processor
        ↓
Bank / ACH Network
```

Payments are asynchronous. When you create a transaction, Payrail returns a `pending` status immediately. The payment processor then communicates with the bank and updates the status to `completed` or `failed`.

---

## Standard payment flow

Follow these steps in order to process a payment successfully.
```
1. POST /api/auth/register     → Create a customer account
2. POST /api/payment-methods   → Add a payment method
3. POST /api/transactions      → Create a transaction
4. GET  /api/transactions/:id  → Poll for status update
5. POST /api/refunds           → Request a refund (if needed)
```

### Step-by-step

**1. Create a customer**

Every transaction requires a customer. Register the customer first and save their `_id`.

**2. Add a payment method**

Attach a card or bank account to the customer. Save the payment method `_id`.

**3. Create a transaction**

Submit the transaction with the customer ID, payment method ID, amount in minor units, and an idempotency key.

**4. Poll for status**

Transaction processing is asynchronous. Poll `GET /api/transactions/:id` until the status changes from `pending` to `completed` or `failed`.

**5. Handle the result**

- If `completed` — payment was successful
- If `failed` — notify the customer and retry with a new idempotency key
- If refund needed — submit `POST /api/refunds`

---

## Transaction status lifecycle
```
pending → completed   (successful payment)
pending → failed      (bank decline)
completed → refunded  (full refund processed)
```

Statuses are system-driven. They cannot be set manually by the client.

---

## Refund flow
```
1. GET  /api/transactions/:id  → Confirm transaction is completed
2. POST /api/refunds           → Submit refund request
3. GET  /api/refunds/:id       → Poll for refund status
```

Refunds follow the same asynchronous pattern as transactions. A refund starts as `pending` and moves to `processed` or `rejected`.

---

## Handling failures

### Network failures
Always include an `Idempotency-Key` on every `POST` request. If a request fails due to a network error, retry with the same key — Payrail will return the original response rather than creating a duplicate.

### Payment declines
If a transaction status is `failed`, the payment was declined by the bank. Common causes include insufficient funds, expired cards, or fraud detection. Create a new transaction with a new idempotency key to retry.

### Idempotency conflicts
If you submit a request with an `Idempotency-Key` that was already used with different parameters, the API returns a `409 Conflict` error.

---

## Settlement and timing

| Event | Timing |
|-------|--------|
| Transaction created | Immediate |
| Status updated to completed or failed | Varies by processor |
| Refund processed | 3–5 business days |
| Funds settled | 1–2 business days after completion |

---

## Related guides

- [Getting Started](quickstart)
- [Authentication](authentication)
- [API Reference](api-reference)