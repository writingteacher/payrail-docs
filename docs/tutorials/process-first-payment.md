---
sidebar_position: 1
---
# Tutorial: Process Your First Payment

This tutorial walks you through the complete flow of processing a customer's first payment using the Payrail API. You will register a customer, add a payment method, and create a transaction.

**Time to complete:** 10 minutes

---

## What you will build

By the end of this tutorial you will have:
- A registered customer
- A payment method attached to that customer
- A completed transaction for $150.00

---

## Prerequisites

- A REST client such as [Postman](https://www.postman.com) or curl
- The [Payrail Postman Collection](https://github.com/writingteacher/payrail-docs/raw/main/postman/payrail-api.postman_collection.json) imported and ready

---

## Step 1 — Register a customer

Every payment in Payrail starts with a customer. Send a `POST` request to create one.

**Request**
```bash
curl -X POST https://payrail-api.onrender.com/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Alice Smith",
    "email": "alice@example.com",
    "phone": "555-1234",
    "password": "password123"
  }'
```

**Response**
```json
{
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "customer": {
        "_id": "69b5330a4314418540e8676e",
        "name": "Alice Smith",
        "email": "alice@example.com",
        "phone": "555-1234",
        "createdAt": "2026-03-14T10:06:02.079Z"
    }
}
```

**What to save:**
- `token` — you will use this in every subsequent request
- `customer._id` — you will use this to attach a payment method and create a transaction

---

## Step 2 — Add a payment method

Now attach a card to Alice's account. Use the `customer._id` from Step 1.

**Request**
```bash
curl -X POST https://payrail-api.onrender.com/api/payment-methods \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{
    "customer": "69b5330a4314418540e8676e",
    "type": "card",
    "last4": "4242",
    "expiryDate": "12/28",
    "isDefault": true
  }'
```

**Response**
```json
{
    "_id": "69b53559c707c55a4e351409",
    "customer": "69b5330a4314418540e8676e",
    "type": "card",
    "last4": "4242",
    "expiryDate": "12/28",
    "isDefault": true,
    "createdAt": "2026-03-14T10:15:53.050Z"
}
```

**What to save:**
- `_id` — the payment method ID you will use in Step 3

---

## Step 3 — Create a transaction

Now process the payment. Use the customer ID and payment method ID from the previous steps.

A few things to note before you send:
- `amount` is in minor units — `15000` = $150.00
- Always include an `Idempotency-Key` to prevent duplicate charges if the request is retried

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

The transaction starts with a `pending` status. The payment processor updates it to `completed` or `failed` asynchronously.

---

## Step 4 — Check the transaction status

Poll the transaction endpoint until the status changes from `pending`.

**Request**
```bash
curl https://payrail-api.onrender.com/api/transactions/69b536d0bcfe7db4d09c1403 \
  -H "Authorization: Bearer YOUR_TOKEN"
```

**Response — completed**
```json
{
    "_id": "69b536d0bcfe7db4d09c1403",
    "customer": "69b5330a4314418540e8676e",
    "paymentMethod": "69b53559c707c55a4e351409",
    "amount": 15000,
    "currency": "USD",
    "status": "completed",
    "description": "Payment for services",
    "createdAt": "2026-03-14T10:22:08.804Z"
}
```

A `completed` status means the payment was successful.

---

## What you built

In this tutorial you:

1. Registered a customer and received a JWT token
2. Added a card as a payment method
3. Created a transaction with an idempotency key
4. Polled for the transaction status

---

## Next steps

- [Handle a failed transaction and retry](handle-failed-transaction)
- [Request a refund](../api-reference#refunds)
- [Set up webhooks](../webhooks) to receive status updates automatically instead of polling