---
sidebar_position: 2
---
# Tutorial: Handle a Failed Transaction and Retry

This tutorial walks you through what to do when a payment fails. You will learn how to detect a failed transaction, understand why it failed, and safely retry the payment with a new idempotency key.

**Time to complete:** 10 minutes

---

## What you will learn

- How to detect a failed transaction
- Common reasons payments fail
- How to safely retry a payment without duplicate charges
- How to notify the customer

---

## Prerequisites

- A registered customer with a JWT token
- A payment method attached to the customer
- Familiarity with [Process Your First Payment](process-first-payment.md)

---

## Step 1 — Create a transaction

Send a payment request as normal. Always include an `Idempotency-Key`.

**Request**
```bash
curl -X POST https://payrail-api.onrender.com/api/transactions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Idempotency-Key: a1b2c3d4-e5f6-7890-abcd-ef1234567890" \
  -d '{
    "customer": "69b5330a4314418540e8676e",
    "paymentMethod": "69b53559c707c55a4e351409",
    "amount": 15000,
    "currency": "USD",
    "description": "Payment for services"
  }'
```

**Response**
```json
{
    "_id": "69b536d0bcfe7db4d09c1403",
    "customer": "69b5330a4314418540e8676e",
    "paymentMethod": "69b53559c707c55a4e351409",
    "amount": 15000,
    "currency": "USD",
    "status": "pending",
    "description": "Payment for services",
    "createdAt": "2026-03-14T10:22:08.804Z"
}
```

Save the transaction `_id` — you will need it to check the status.

---

## Step 2 — Detect the failure

Poll the transaction endpoint to check the status.

**Request**
```bash
curl https://payrail-api.onrender.com/api/transactions/69b536d0bcfe7db4d09c1403 \
  -H "Authorization: Bearer YOUR_TOKEN"
```

**Response — failed**
```json
{
    "_id": "69b536d0bcfe7db4d09c1403",
    "customer": "69b5330a4314418540e8676e",
    "paymentMethod": "69b53559c707c55a4e351409",
    "amount": 15000,
    "currency": "USD",
    "status": "failed",
    "description": "Payment for services",
    "createdAt": "2026-03-14T10:22:08.804Z"
}
```

A `failed` status means the bank declined the payment.

---

## Step 3 — Understand why payments fail

Common reasons a transaction moves to `failed`:

| Reason | Description | Action |
|--------|-------------|--------|
| Insufficient funds | Customer's account balance is too low | Ask customer to use a different payment method |
| Expired card | Card expiry date has passed | Ask customer to update their card |
| Fraud detection | Bank flagged the transaction as suspicious | Ask customer to contact their bank |
| Invalid card details | Card number or details are incorrect | Ask customer to re-enter card details |
| Bank decline | Bank declined without specific reason | Ask customer to contact their bank |

---

## Step 4 — Add a new payment method (if needed)

If the customer needs to use a different card, add a new payment method.

**Request**
```bash
curl -X POST https://payrail-api.onrender.com/api/payment-methods \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{
    "customer": "69b5330a4314418540e8676e",
    "type": "card",
    "last4": "1234",
    "expiryDate": "06/29",
    "isDefault": true
  }'
```

**Response**
```json
{
    "_id": "69b53559c707c55a4e351500",
    "customer": "69b5330a4314418540e8676e",
    "type": "card",
    "last4": "1234",
    "expiryDate": "06/29",
    "isDefault": true,
    "createdAt": "2026-03-14T10:30:00.000Z"
}
```

---

## Step 5 — Retry the payment

Create a new transaction with a **new idempotency key**. Never reuse the idempotency key from the failed transaction.

**Request**
```bash
curl -X POST https://payrail-api.onrender.com/api/transactions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Idempotency-Key: b2c3d4e5-f6a7-8901-bcde-f12345678901" \
  -d '{
    "customer": "69b5330a4314418540e8676e",
    "paymentMethod": "69b53559c707c55a4e351500",
    "amount": 15000,
    "currency": "USD",
    "description": "Payment for services - retry"
  }'
```

> **Important:** Always use a new `Idempotency-Key` when retrying a failed transaction. Reusing the same key returns the original failed response.

**Response**
```json
{
    "_id": "69b536d0bcfe7db4d09c1500",
    "customer": "69b5330a4314418540e8676e",
    "paymentMethod": "69b53559c707c55a4e351500",
    "amount": 15000,
    "currency": "USD",
    "status": "pending",
    "description": "Payment for services - retry",
    "createdAt": "2026-03-14T10:35:00.000Z"
}
```

---

## Step 6 — Confirm the retry succeeded

Poll the new transaction until the status changes from `pending`.

**Request**
```bash
curl https://payrail-api.onrender.com/api/transactions/69b536d0bcfe7db4d09c1500 \
  -H "Authorization: Bearer YOUR_TOKEN"
```

**Response — completed**
```json
{
    "_id": "69b536d0bcfe7db4d09c1500",
    "customer": "69b5330a4314418540e8676e",
    "paymentMethod": "69b53559c707c55a4e351500",
    "amount": 15000,
    "currency": "USD",
    "status": "completed",
    "description": "Payment for services - retry",
    "createdAt": "2026-03-14T10:35:00.000Z"
}
```

---

## Summary

When a payment fails:

1. Check the transaction status with `GET /api/transactions/:id`
2. Identify the likely cause and communicate it to the customer
3. Add a new payment method if needed
4. Retry with a **new** idempotency key
5. Confirm the retry succeeded

---

## Next steps

- [Set up webhooks](../webhooks.md) to receive failure notifications automatically
- [Request a refund](../api-reference.md#refunds) if a completed payment needs to be reversed
- [API Reference](../api-reference.md) for full endpoint details