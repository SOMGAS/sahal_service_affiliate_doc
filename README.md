# Partner Discount API

> Simple REST API for partner companies to verify an employee and check discount eligibility.

**Base URL (Production)**  
```
https://api.yourcompany.com
```

**Authentication**  
Provide your API key in a header:
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
```json
// By employeeId
{ "employeeId": "SO2342" }
```
```json
// By mobile (E.164)
{ "mobile": "252617953152" }
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
curl -X POST https://api.yourcompany.com/v1/employees/check-status   -H "Content-Type: application/json"   -H "X-API-Key: $API_KEY"   -d '{"mobile":"252617953152"}'
```

---

### 2) Verify Discount Eligibility
**POST** `/v1/discounts/verify`

Validates whether a given employee is eligible for a partner discount.

**Headers**
```
Content-Type: application/json
X-API-Key: <YOUR_API_KEY>
```

**Request (choose ONE identifier)**
```json
// By employeeId
{ "employeeId": "SO2342" }
```
```json
// By mobile (E.164)
{ "mobile": "252617953152" }
```

**Response — 200**
```json
{
  "data": {
    "eligible": true,
    "level": "Gold",
    "discountPercent": 20,
    "employeeId": "SO2342",
    "status": "Active"
  }
}
```
Notes:
- `level` can be `"Standard" | "Silver" | "Gold" | "Platinum"`.
- `status` mirrors the employee status from **Check Employee Status**.

**Errors**
- `400 Bad Request` – invalid input (must provide one identifier)
- `401 Unauthorized` – missing/invalid API key
- `404 Not Found` – no employee matched
- `409 Conflict` – employee is inactive or suspended (not eligible)
- `429 Too Many Requests` – rate limited

**Example**
```bash
curl -X POST https://api.yourcompany.com/v1/discounts/verify   -H "Content-Type: application/json"   -H "X-API-Key: $API_KEY"   -d '{"employeeId":"SO2342"}'
```

---

## Common Models

### Error object
```json
{
  "error": {
    "code": "INVALID_INPUT",
    "message": "Provide either employeeId or mobile (E.164).",
    "details": {
      "field": "mobile"
    }
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
- `409 Conflict` — business rule violation (e.g., inactive employee for discount)
- `429 Too Many Requests` — throttled

---

## Rate Limiting

Default: **60 requests/minute** per API key. Contact support to raise limits.  
When rate limited, the API returns `429` with headers:
```
X-RateLimit-Limit, X-RateLimit-Remaining, Retry-After
```

---

## Changelog

- **v1.0.0** — Initial release with two endpoints.

---

## OpenAPI

See the bundled [`openapi.yaml`](./openapi.yaml). You can preview it via Redoc at `/docs/` when hosted on GitHub Pages.
