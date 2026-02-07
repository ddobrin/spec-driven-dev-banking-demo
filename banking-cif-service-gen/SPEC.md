# Banking CIF Application Specification v0.1.0

## Overview

The Banking CIF (Customer Information Facility) application is a microservice designed to manage core banking entities: Persons (Customers), Financial Applications (Accounts), and Transactions. It provides a RESTful API to perform CRUD operations, adhering to modern banking standards including UUIDs for keys and audit trails.

## Technology Stack

- **Language:** Java 25
- **Framework:** Spring Boot 4.0.2
- **Spring Boot profiles:** dev, test, prod
- **Database:** PostgreSQL (Schema defined in `ddl.md`) for prod, H2 for dev, Postgres Testcontainer for running tests in test profile
- **TestContainers:** For database testing - use a Postgres container
- **JSON Library:** Use Jackson 3 for JSON processing
- **Architecture:** Layered (Controller, Service, Repository)

## Design Principles

1.  **Stateless REST:** All API interactions are stateless.
2.  **DTO Pattern:** Expose Data Transfer Objects (DTOs), not JPA Entities directly.
3.  **Global Error Handling:** Consistent error responses using `@ControllerAdvice`.
4.  **Immutable Data:** Use Java Records for DTOs where possible.
5.  **Validation:** Strict input validation using Jakarta Validation.

---

## Data Model (Summary)

The application maps to the provided PostgreSQL DDL:

-   **Person (Customer):** Represents the identity (`customers` table).
    -   Key attributes: `customer_id` (UUID), `cif_number` (String), `first_name`, `last_name`, `email`, `kyc_status`.
-   **Financial Application (Account):** Represents a product instance held by a customer (`accounts` table).
    -   Key attributes: `account_id` (UUID), `account_number`, `product_code`, `balance`, `status`.
-   **Transaction:** Represents a financial movement (`transactions` table).
    -   Key attributes: `transaction_id` (UUID), `amount`, `transaction_type`, `balance_after`.

---

## API Specification

All endpoints are prefixed with `/api/v1`.

### 1. Person (Customers)

**Base URL:** `/api/v1/customers`

#### Create Person
-   **Method:** `POST /`
-   **Body:**
    ```json
    {
      "firstName": "John",
      "lastName": "Doe",
      "email": "john.doe@example.com",
      "dateOfBirth": "1985-05-20",
      "cifNumber": "CIF-1001"
    }
    ```
-   **Response:** `201 Created` with created Customer DTO.

#### Get Person
-   **Method:** `GET /{id}`
-   **Response:** `200 OK` with Customer DTO.
-   **Error:** `404 Not Found` if ID does not exist.

#### Update Person
-   **Method:** `PUT /{id}`
-   **Body:** (Fields to update, e.g., address, email)
-   **Response:** `200 OK` with updated Customer DTO.

#### Delete Person
-   **Method:** `DELETE /{id}`
-   **Response:** `204 No Content`.

---

### 2. Financial Application (Accounts)

**Base URL:** `/api/v1/accounts`

#### Create Financial Application (Open Account)
-   **Method:** `POST /`
-   **Body:**
    ```json
    {
      "customerId": "uuid-...",
      "productCode": "SAV-GOLD",
      "currencyCode": "USD"
    }
    ```
-   **Response:** `201 Created` with Account DTO (including generated `accountNumber`).

#### Get Account Details
-   **Method:** `GET /{id}`
-   **Response:** `200 OK` with Account DTO.

#### Update Account Status
-   **Method:** `PATCH /{id}/status`
-   **Body:** `{ "status": "FROZEN" }`
-   **Response:** `200 OK` with updated Account DTO.

---

### 3. Transactions

**Base URL:** `/api/v1/transactions`

#### Create Transaction (Deposit/Withdrawal)
-   **Method:** `POST /`
-   **Body:**
    ```json
    {
      "accountId": "uuid-...",
      "type": "DEPOSIT",
      "amount": 100.00,
      "currency": "USD",
      "description": "ATM Deposit"
    }
    ```
-   **Response:** `201 Created` with Transaction DTO (including `balanceAfter`).

#### Get Transaction History
-   **Method:** `GET /account/{accountId}`
-   **Response:** `200 OK` with List of Transaction DTOs.

---

## Error Handling

Errors return a standard JSON structure:

```json
{
  "timestamp": "2026-02-06T10:00:00Z",
  "status": 400,
  "error": "Bad Request",
  "message": "Invalid email format",
  "path": "/api/v1/customers"
}
```

| HTTP Status | Condition |
|-------------|-----------|
| 400 | Validation failure (e.g., missing field, negative amount) |
| 404 | Resource not found (Customer/Account ID) |
| 409 | Conflict (e.g., Duplicate CIF or Email) |
| 500 | Internal Server Error |

---

## Testing

Implementations must pass the scenarios defined in `test.yaml`.
