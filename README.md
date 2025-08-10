# Sahal Service Discount API Documentation

> REST API for partner companies to verify employees and save SomGas loan adjustments.

**Base URL (Production)**
```
https://api.yourcompany.com
```

**Authentication**
Send your API key with every request:
```
X-API-Key: <YOUR_API_KEY>
```
All requests must use HTTPS and `Content-Type: application/json`.

---

## Endpoints

### 1) Check Employee Status
**POST** `/v1/employees/check-status`

Checks whether an employee exists and returns their current status.

**Headers**
```
Content-Type: application/json
X-API-Key: <YOUR_API_KEY>
```

**Request (choose ONE identifier)**
```jsonc
// By employeeId
{ "employeeId": "SO2342" }
```
```jsonc
// By mobile (local number without '+' and country code)
{ "mobile": "617953152" }
```

**Response — 200**
```json
{
  "data": {
    "status": "Active",
    "employeeId": "SO2342",
    "name": "Mohamed Mahad Farah"
  }
}
```
Where `status` ∈ `["Active","Inactive","NotFound"]`.

**Errors**
- `400 Bad Request` – invalid input (must provide one identifier)
- `401 Unauthorized` – missing/invalid API key
- `404 Not Found` – no employee matched
- `429 Too Many Requests` – rate limited

**Example**
```bash
curl -X POST https://api.yourcompany.com/v1/employees/check-status   -H "Content-Type: application/json"   -H "X-API-Key: $API_KEY"   -d '{"mobile":"617953152"}'
```

---

### 2) Save SomGas Loan / Adjustment
**POST** `/v1/employees/somgas-loan`

Creates an adjustment record (e.g., loan deduction/payment) for an employee.

**Production alias (direct host):**
`POST https://ccxapi.hormuud.com/api/v1/SaveEmployeeAdjustmentSomGas`

**Headers**
```
Content-Type: application/json
X-API-Key: <YOUR_API_KEY>
```

**Request (choose ONE identifier)**
```json
{
  "employeeId": "SO2342",
  "amount": 13,
  "actionDate": "2025-08-10",
  "orderId": "SO23322",
  "isConfirmed": 1
}
```
_or_
```json
{
  "mobile": "617953152",
  "amount": 13,
  "actionDate": "2025-08-10",
  "orderId": "SO23322",
  "isConfirmed": 1
}
```

**Field notes**
- `amount` — number (positive).  
- `actionDate` — `YYYY-MM-DD`.  
- `orderId` — unique request reference.  
- `isConfirmed` — `1` (confirmed) or `0` (pending).

**Response — 200**
```json
{
  "data": {
    "status": "Success",
    "orderId": "SO23322",
    "employeeId": "SO2342",
    "processedAt": "2025-08-10T14:22:00Z"
  }
}
```

**Errors**
- `400 Bad Request` – invalid input (e.g., non-positive amount)
- `401 Unauthorized` – missing/invalid API key
- `404 Not Found` – employee not found
- `409 Conflict` – duplicate order or business rule violation (e.g., inactive employee)
- `429 Too Many Requests` – rate limited

**Example**
```bash
curl -X POST https://api.yourcompany.com/v1/employees/somgas-loan   -H "Content-Type: application/json"   -H "X-API-Key: $API_KEY"   -d '{
        "mobile": "617953152",
        "amount": 13,
        "actionDate": "2025-08-10",
        "orderId": "SO23322",
        "isConfirmed": 1
      }'
```

---

## Common Models

### Error object
```json
{
  "error": {
    "code": "INVALID_INPUT",
    "message": "Provide either employeeId or mobile.",
    "details": { "field": "mobile" }
  }
}
```

### Status enum
```
Active | Inactive | NotFound
```

---

## HTTP Status Codes

- `200 OK` — success
- `400 Bad Request` — malformed or missing fields
- `401 Unauthorized` — bad or missing API key
- `404 Not Found` — resource not found
- `409 Conflict` — business rule violation (e.g., inactive employee or duplicate order)
- `429 Too Many Requests` — throttled

---

## Rate Limiting

Default: **60 requests/minute** per API key.
When rate limited, the API returns `429` and headers:
```
X-RateLimit-Limit, X-RateLimit-Remaining, Retry-After
```

---

## Changelog

- **v1.0.1** — Added SomGas loan/adjustment fields, success example, and production alias path.
- **v1.0.0** — Initial release with check-status.