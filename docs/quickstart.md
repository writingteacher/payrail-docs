---
sidebar_position: 1
---
# Getting Started

Payrail is a fintech payments API that lets you manage customers, payment methods, transactions, and refunds. This guide gets you from zero to your first transaction in under 10 minutes.

## Base URL
```
https://payrail-api.onrender.com
```

## Who is this for?

This guide is written for backend developers integrating payments into their application. You will need basic familiarity with REST APIs and HTTP requests. No prior payments experience is required.

---

## Before you begin

You will need:
- A REST client such as [Postman](https://www.postman.com) or curl
- A Payrail account (created via the Register endpoint)
- A JWT token (returned on registration and login)
- The [Swagger Interactive Docs](https://editor.swagger.io/?url=https://raw.githubusercontent.com/writingteacher/payrail-api/master/openapi.yaml) to test endpoints in your browser without Postman

---

## Step 1 — Register an account

Create a Payrail account. This returns a JWT token you will use to authenticate all subsequent requests.

**Request**
```bash
curl -X POST https://payrail-api.onrender.com/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "name": "John Doe",
    "email": "john@example.com",
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
        "name": "John Doe",
        "email": "john@example.com",
        "phone": "555-1234",
        "createdAt": "2026-03-14T10:06:02.079Z"
    }
}
```

Copy the `token` value — you will need it for every authenticated request.

---

## Step 2 — Add a payment method

Add a card or bank account to the customer profile.

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

---

## Step 3 — Create a transaction

Process a payment using the customer and payment method from the previous steps.

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

> **Note:** `amount` is in minor units. `15000` = $150.00.

> **Note:** Always include an `Idempotency-Key` header to prevent duplicate charges if the request is retried.

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

---

## Step 4 — Request a refund

Refund a transaction by referencing its ID.

**Request**
```bash
curl -X POST https://payrail-api.onrender.com/api/refunds \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Idempotency-Key: b2c3d4e5-f6a7-8901-bcde-f12345678901" \
  -d '{
    "customer": "69b5330a4314418540e8676e",
    "transaction": "69b536d0bcfe7db4d09c1403",
    "amount": 15000,
    "reason": "Customer requested refund"
  }'
```

**Response**
```json
{
    "_id": "69b537548386681aea83bfd2",
    "customer": "69b5330a4314418540e8676e",
    "transaction": "69b536d0bcfe7db4d09c1403",
    "amount": 15000,
    "reason": "Customer requested refund",
    "status": "pending",
    "createdAt": "2026-03-14T10:24:20.638Z"
}
```

---

## Next steps

- Read the full [API Reference](api-reference.md) for all available endpoints
- Learn about [Authentication](authentication.md) and JWT tokens
- Download the [Postman Collection](https://www.postman.com/techcomteacher99-7835706/workspace/payrail-api/collection/53069530-700ccabf-377e-47c2-9513-f36683e4777f?action=share&source=copy-link&creator=53069530) — open directly in Postman Web  