---
sidebar_position: 7
---
# Code Examples

Examples for integrating with the Payrail API in curl, JavaScript, and Python.

---

## Authentication

### Register

**curl**
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

**JavaScript**
```javascript
const response = await fetch('https://payrail-api.onrender.com/api/auth/register', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    name: 'John Doe',
    email: 'john@example.com',
    phone: '555-1234',
    password: 'password123'
  })
});

const data = await response.json();
const token = data.token;
```

**Python**
```python
import requests

response = requests.post(
    'https://payrail-api.onrender.com/api/auth/register',
    json={
        'name': 'John Doe',
        'email': 'john@example.com',
        'phone': '555-1234',
        'password': 'password123'
    }
)

data = response.json()
token = data['token']
```

---

## Customers

### Create a customer

**curl**
```bash
curl -X POST https://payrail-api.onrender.com/api/customers \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{
    "name": "Jane Doe",
    "email": "jane@example.com",
    "phone": "555-5678",
    "password": "password123"
  }'
```

**JavaScript**
```javascript
const response = await fetch('https://payrail-api.onrender.com/api/customers', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': `Bearer ${token}`
  },
  body: JSON.stringify({
    name: 'Jane Doe',
    email: 'jane@example.com',
    phone: '555-5678',
    password: 'password123'
  })
});

const customer = await response.json();
```

**Python**
```python
response = requests.post(
    'https://payrail-api.onrender.com/api/customers',
    headers={
        'Authorization': f'Bearer {token}'
    },
    json={
        'name': 'Jane Doe',
        'email': 'jane@example.com',
        'phone': '555-5678',
        'password': 'password123'
    }
)

customer = response.json()
```

---

## Payment Methods

### Add a payment method

**curl**
```bash
curl -X POST https://payrail-api.onrender.com/api/payment-methods \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{
    "customer": "CUSTOMER_ID",
    "type": "card",
    "last4": "4242",
    "expiryDate": "12/28",
    "isDefault": true
  }'
```

**JavaScript**
```javascript
const response = await fetch('https://payrail-api.onrender.com/api/payment-methods', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': `Bearer ${token}`
  },
  body: JSON.stringify({
    customer: customerId,
    type: 'card',
    last4: '4242',
    expiryDate: '12/28',
    isDefault: true
  })
});

const paymentMethod = await response.json();
```

**Python**
```python
response = requests.post(
    'https://payrail-api.onrender.com/api/payment-methods',
    headers={
        'Authorization': f'Bearer {token}'
    },
    json={
        'customer': customer_id,
        'type': 'card',
        'last4': '4242',
        'expiryDate': '12/28',
        'isDefault': True
    }
)

payment_method = response.json()
```

---

## Transactions

### Create a transaction

**curl**
```bash
curl -X POST https://payrail-api.onrender.com/api/transactions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Idempotency-Key: a1b2c3d4-e5f6-7890-abcd-ef1234567890" \
  -d '{
    "customer": "CUSTOMER_ID",
    "paymentMethod": "PAYMENT_METHOD_ID",
    "amount": 15000,
    "currency": "USD",
    "description": "Payment for services"
  }'
```

**JavaScript**
```javascript
const response = await fetch('https://payrail-api.onrender.com/api/transactions', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': `Bearer ${token}`,
    'Idempotency-Key': 'a1b2c3d4-e5f6-7890-abcd-ef1234567890'
  },
  body: JSON.stringify({
    customer: customerId,
    paymentMethod: paymentMethodId,
    amount: 15000,
    currency: 'USD',
    description: 'Payment for services'
  })
});

const transaction = await response.json();
```

**Python**
```python
response = requests.post(
    'https://payrail-api.onrender.com/api/transactions',
    headers={
        'Authorization': f'Bearer {token}',
        'Idempotency-Key': 'a1b2c3d4-e5f6-7890-abcd-ef1234567890'
    },
    json={
        'customer': customer_id,
        'paymentMethod': payment_method_id,
        'amount': 15000,
        'currency': 'USD',
        'description': 'Payment for services'
    }
)

transaction = response.json()
```

---

## Refunds

### Create a refund

**curl**
```bash
curl -X POST https://payrail-api.onrender.com/api/refunds \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Idempotency-Key: b2c3d4e5-f6a7-8901-bcde-f12345678901" \
  -d '{
    "customer": "CUSTOMER_ID",
    "transaction": "TRANSACTION_ID",
    "amount": 15000,
    "reason": "Customer requested refund"
  }'
```

**JavaScript**
```javascript
const response = await fetch('https://payrail-api.onrender.com/api/refunds', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': `Bearer ${token}`,
    'Idempotency-Key': 'b2c3d4e5-f6a7-8901-bcde-f12345678901'
  },
  body: JSON.stringify({
    customer: customerId,
    transaction: transactionId,
    amount: 15000,
    reason: 'Customer requested refund'
  })
});

const refund = await response.json();
```

**Python**
```python
response = requests.post(
    'https://payrail-api.onrender.com/api/refunds',
    headers={
        'Authorization': f'Bearer {token}',
        'Idempotency-Key': 'b2c3d4e5-f6a7-8901-bcde-f12345678901'
    },
    json={
        'customer': customer_id,
        'transaction': transaction_id,
        'amount': 15000,
        'reason': 'Customer requested refund'
    }
)

refund = response.json()
```

---

## Related guides

- [Getting Started](quickstart.md)
- [API Reference](api-reference.md)
- [Payment Workflow](payment-workflow.md)