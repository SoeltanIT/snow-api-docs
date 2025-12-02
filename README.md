# PAM / Wallet API Documentation
Version: 1.0  
Author: Felix  
Last Updated: 2025-XX-XX  
Status: Draft

---

## 1. Purpose
This document provides the API specifications for the PAM / Wallet Service. It describes how to fetch user balances, retrieve transaction history, and trigger balance updates within the Go microservice environment.

---

## 2. Scope
This documentation covers:
- Balance retrieval APIs  
- Transaction listing APIs  
- Balance update APIs  
- Authentication  
- Data models  
- Request and response examples  
- Error codes  
- Optional sequence diagrams  

---

## 3. Architecture Overview (Optional)
Describe how the Wallet Service interacts with other microservices such as Game Service, Promotion Service, and Transaction Service.

(Insert diagram here.)

---

## 4. Authentication

### Headers required:
```
Authorization: Bearer <jwt_token>
Content-Type: application/json
```

A valid JWT token is required for all endpoints.

---

## 5. Data Models

### UserBalance Model
| Field        | Type   | Description                |
|--------------|--------|----------------------------|
| user_id      | string | Unique user identifier     |
| balance      | number | Main wallet balance        |
| bonus_balance| number | Bonus balance              |
| currency     | string | Currency code (KRW, IDR)   |

### Transaction Model
| Field      | Type     | Description                             |
|------------|----------|-----------------------------------------|
| id         | string   | Transaction ID                          |
| type       | string   | deposit, withdraw, bet, rollback        |
| amount     | number   | Amount of the transaction               |
| created_at | timestamp| Timestamp of creation                   |
| meta       | object   | Additional metadata                     |

---

# 6. API Endpoints

## 6.1 GET Balance
**Endpoint:**  
```
GET /wallet/v1/balance/{userID}
```

**Description:**  
Fetch the user's current wallet balances.

**Response Example:**
```json
{
  "user_id": "U123",
  "balance": 100000,
  "bonus_balance": 50000
}
```

---

## 6.2 GET Transactions
**Endpoint:**  
```
GET /wallet/v1/transactions?user_id={id}&page=1&pageSize=20
```

**Description:**  
Retrieve paginated transaction history for a user.

### Query Parameters
| Name     | Type     | Required | Description |
|----------|----------|----------|-------------|
| user_id  | string   | Yes      | User ID     |
| page     | integer  | No       | Default=1   |
| pageSize | integer  | No       | Default=20  |

**Response Example:**
```json
{
  "total": 100,
  "transactions": [
    {
      "id": "tx001",
      "type": "deposit",
      "amount": 500000,
      "created_at": "2025-01-01T14:22:00Z"
    }
  ]
}
```

---

## 6.3 POST Balance Update
**Endpoint:**  
```
POST /wallet/v1/update
```

**Description:**  
Perform a balance update using a defined transaction type (plus, minus, bet, rollback).

**Request Example:**
```json
{
  "user_id": "U123",
  "type": "plus",
  "amount": 50000,
  "reference_id": "tx123",
  "meta": {
    "game": "poker",
    "round_id": "r0001"
  }
}
```

**Response Example:**
```json
{
  "new_balance": 150000
}
```

---

## 7. Error Codes
| Code  | Description                   |
|-------|-------------------------------|
| 30001 | Insufficient balance          |
| 30002 | Invalid transaction type      |
| 10107 | User not found                |
| 90000 | Internal processing error     |

---

## 8. Sequence Diagrams (Optional)

Example: Fetch Balance  
1. Client sends "Get Balance" request  
2. Wallet Service queries the database  
3. Wallet Service returns the balance response  

(Insert PlantUML diagram here.)

---

## 9. Go Integration Example
```go
req, _ := http.NewRequest("GET", baseURL+"/wallet/v1/balance/"+userID, nil)
req.Header.Set("Authorization", "Bearer "+token)

resp, err := http.DefaultClient.Do(req)
if err != nil {
    log.Println("failed to fetch balance:", err)
}
```

---

## 10. Changelog
| Version | Description |
|---------|-------------|
| 1.0     | Initial documentation |
