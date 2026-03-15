---
sidebar_position: 2
---
# Authentication

Payrail uses JSON Web Tokens (JWT) to authenticate requests. Every request to a protected endpoint must include a valid token in the `Authorization` header.

> **Note:** Password fields are never returned in any API response, including registration and login.

---

## How it works

1. Register or log in via `/api/auth/register` or `/api/auth/login`
2. Receive a JWT token in the response
3. Include the token in the `Authorization` header of every subsequent request
4. The token expires after 7 days — log in again to get a new one

---

## Sending the token

Include the token in the request header using the `Bearer` scheme:
```
Authorization: Bearer <token>
```

**Example**
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

---

## Obtaining a token

### Register

Send a `POST` request to `/api/auth/register` with your name, email, phone, and password. A token is returned immediately on successful registration.
```json
{
    "name": "John Doe",
    "email": "john@example.com",
    "phone": "555-1234",
    "password": "password123"
}
```

### Log in

Send a `POST` request to `/api/auth/login` with your email and password.
```json
{
    "email": "john@example.com",
    "password": "password123"
}
```

Both endpoints return the same response format:
```json
{
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "customer": {
        "_id": "69b5330a4314418540e8676e",
        "name": "John Doe",
        "email": "john@example.com",
        "createdAt": "2026-03-14T10:06:02.079Z"
    }
}
```

---

## Token expiry

Tokens expire after **7 days**. After expiry, requests return a `401 Unauthorized` response:
```json
{
    "message": "Authentication required"
}
```

When this happens, log in again to obtain a new token.

---

## Protected vs public endpoints

| Endpoint | Protected |
|----------|-----------|
| `POST /api/auth/register` | No |
| `POST /api/auth/login` | No |
| `POST /api/customers` | Yes |
| `GET /api/customers` | Yes |
| `GET /api/customers/:id` | Yes |
| `PUT /api/customers/:id` | Yes |
| `DELETE /api/customers/:id` | Yes |
| `POST /api/payment-methods` | Yes |
| `GET /api/payment-methods` | Yes |
| `GET /api/payment-methods/:id` | Yes |
| `PUT /api/payment-methods/:id` | Yes |
| `DELETE /api/payment-methods/:id` | Yes |
| `POST /api/transactions` | Yes |
| `GET /api/transactions` | Yes |
| `GET /api/transactions/:id` | Yes |
| `PUT /api/transactions/:id` | Yes |
| `POST /api/refunds` | Yes |
| `GET /api/refunds` | Yes |
| `GET /api/refunds/:id` | Yes |
| `PUT /api/refunds/:id` | Yes |