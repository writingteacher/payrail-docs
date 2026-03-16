---
sidebar_position: 5
---
# Data Models

Every resource in the Payrail API returns a consistent object structure. Use these definitions as a reference when building your integration.

---

## Customer object
```json
{
    "_id": "69b5330a4314418540e8676e",
    "name": "John Doe",
    "email": "john@example.com",
    "phone": "555-1234",
    "createdAt": "2026-03-14T10:06:02.079Z"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `_id` | string | Unique identifier for the customer |
| `name` | string | Customer's full name |
| `email` | string | Customer's email address. Must be unique. |
| `phone` | string | Customer's phone number |
| `createdAt` | string | ISO 8601 timestamp of when the customer was created |

> **Note:** Password fields are never returned in any customer response.

---

## Payment Method object
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

| Field | Type | Description |
|-------|------|-------------|
| `_id` | string | Unique identifier for the payment method |
| `customer` | string | ID of the customer this payment method belongs to |
| `type` | string | Payment type — `card` or `bank_account` |
| `last4` | string | Last 4 digits of the card or account. Display purposes only — full card numbers are never stored. |
| `expiryDate` | string | Card expiry date in MM/YY format |
| `isDefault` | boolean | Whether this is the customer's default payment method |
| `createdAt` | string | ISO 8601 timestamp of when the payment method was added |

---

## Transaction object
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

| Field | Type | Description |
|-------|------|-------------|
| `_id` | string | Unique identifier for the transaction |
| `customer` | string | ID of the customer being charged |
| `paymentMethod` | string | ID of the payment method used |
| `amount` | integer | Amount in minor units (e.g. 15000 = $150.00) |
| `currency` | string | 3-letter ISO 4217 currency code |
| `status` | string | Transaction status — `pending`, `completed`, `failed`, or `refunded` |
| `description` | string | Description of the transaction |
| `createdAt` | string | ISO 8601 timestamp of when the transaction was created |

---

## Refund object
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

| Field | Type | Description |
|-------|------|-------------|
| `_id` | string | Unique identifier for the refund |
| `customer` | string | ID of the customer receiving the refund |
| `transaction` | string | ID of the transaction being refunded |
| `amount` | integer | Refund amount in minor units (e.g. 15000 = $150.00) |
| `reason` | string | Reason for the refund |
| `status` | string | Refund status — `pending`, `processed`, or `rejected` |
| `createdAt` | string | ISO 8601 timestamp of when the refund was created |

---

## Related guides

- [API Reference](api-reference)
- [Payment Workflow](payment-workflow)