---
sidebar_position: 8
---

# Errors

Payrail uses standard HTTP status codes to indicate the success or failure of a request. All error responses return a JSON object with a `message` field describing the error.

---

## Error response format
```json
{
    "message": "Description of the error"
}
```

For validation errors, the response includes an `errors` array:
```json
{
    "errors": [
        {
            "type": "field",
            "msg": "Valid email is required",
            "path": "email",
            "location": "body"
        }
    ]
}
```

---

## HTTP status codes

| Status Code | Meaning |
|-------------|---------|
| `200 OK` | Request succeeded |
| `201 Created` | Resource created successfully |
| `400 Bad Request` | Invalid request — missing or malformed parameters |
| `401 Unauthorized` | Authentication required or token invalid |
| `404 Not Found` | Resource does not exist |
| `429 Too Many Requests` | Rate limit exceeded |
| `500 Internal Server Error` | Unexpected server error |

---

## Error reference

### 400 Bad Request

Returned when the request is missing required fields or contains invalid values.

**Example — missing required fields:**
```json
{
    "errors": [
        {
            "type": "field",
            "msg": "Valid email is required",
            "path": "email",
            "location": "body"
        },
        {
            "type": "field",
            "msg": "Password must be at least 6 characters",
            "path": "password",
            "location": "body"
        }
    ]
}
```

**Example — email already registered:**
```json
{
    "message": "Email already registered"
}
```

**Troubleshooting:**
- Check all required fields are included in the request body
- Ensure `email` is a valid email address
- Ensure `password` is at least 6 characters
- Ensure `amount` is a number greater than 0
- Ensure `currency` is a 3-letter ISO 4217 code such as `USD`, `EUR`, or `GBP`

---

### 401 Unauthorized

Returned when no token is provided, the token is invalid, or the token has expired.

**Example:**
```json
{
    "message": "Authentication required"
}
```

**Troubleshooting:**
- Ensure the `Authorization` header is included in the request
- Ensure the token is formatted correctly: `Bearer <token>`
- Tokens expire after 7 days — log in again to get a new token
- Ensure you are using the token returned from `/api/auth/login` or `/api/auth/register`

---

### 404 Not Found

Returned when the requested resource does not exist or has been deleted.

**Example:**
```json
{
    "message": "Customer not found"
}
```

**Troubleshooting:**
- Check the `id` in the request URL is correct
- The resource may have been soft-deleted — deleted customers are no longer returned by the API

---

### 429 Too Many Requests

Returned when a client exceeds the rate limit of 100 requests per 15 minutes.

**Example:**
```json
{
    "message": "Too many requests, please try again later."
}
```

**Troubleshooting:**
- Slow down your request rate
- Wait 15 minutes before retrying
- Implement exponential backoff in your integration

---

### 500 Internal Server Error

Returned when an unexpected error occurs on the server.

**Example:**
```json
{
    "message": "Internal Server Error"
}
```

**Troubleshooting:**
- Retry the request after a short delay
- If the error persists, check the [API status](#) or contact support

---

## Related guides

- [Authentication](/docs/authentication)
- [API Reference](/docs/api-reference)
- [FAQ](/docs/faq)