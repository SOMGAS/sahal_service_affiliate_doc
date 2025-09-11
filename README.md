# Sahal Service Staff Offer API Documentation

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
**POST** `/api/v1/employees/status`

Checks whether an employee exists and returns their current status.

**Headers**
```
Content-Type: application/json
X-API-Key: <YOUR_API_KEY>
```

**Request (choose ONE identifier)**

```jsonc
// By mobile (local number without '+' and country code)
{ "mobile": "617953152" }
```

**Response — 200**
```json
{
    "message": "Success",
    "data": [
        {
            "Status": "Active",
            "EmpId": "S18289",
            "Name": "Mohamed Mahad Farah"
        }
    ]
}
```
Where `status` ∈ `["Active","Inactive", "Temporary", "NotFound"]`.

**Errors**
- `400 Bad Request` – invalid input (must provide one identifier)
- `401 Unauthorized` – missing/invalid API key
- `404 Not Found` – no employee matched

**Example**
```bash
curl -X POST https://api.yourcompany.com/api/v1/employees/status   -H "Content-Type: application/json"   -H "X-API-Key: $API_KEY"   -d '{"mobile":"617953152"}'
```

---

### 2) Save SomGas Loan / Adjustment
**POST** `/api/v1/employees/debts/confirm`

Creates an adjustment record (e.g., loan deduction/payment) for an employee.

**Production alias (direct host):**
`POST https://api.yourcompany.com/api/v1/employees/debts/confirm`

**Headers**
```
Content-Type: application/json
X-API-Key: <YOUR_API_KEY>
```

**Request**
```json
{
    "Amount": 10.35,
    "Tell": "615585400",
    "ActionDate": "2025-09-06",
    "OrderId": "S174825",
    "Isconfirmed": "1"
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
    "message": "Success"
}
```

**Errors**
- `400 Bad Request` – invalid input (e.g., non-positive amount)
- `401 Unauthorized` – missing/invalid API key
- `404 Not Found` – employee not found
- `409 Conflict` – duplicate order or business rule violation (e.g., inactive employee)

**Example**
```bash
curl -X POST https://api.yourcompany.com/api/v1/employees/debts/confirm   -H "Content-Type: application/json"   -H "X-API-Key: $API_KEY"   -d '{
    "Amount": 10.35,
    "Tell": "615585400",
    "ActionDate": "2025-09-06",
    "OrderId": "S174825",
    "Isconfirmed": "1"
}'
```

---