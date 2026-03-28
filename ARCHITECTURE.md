# D3 Project Architecture

This document defines the high-level architecture and data flow for the Order Processing System.

## 1. N-Tier Layered Architecture
We strictly follow a three-tier decoupling pattern to ensure testability and maintainability.

### Layer 1: Controller (API)
- **Responsibility:** Handle HTTP/REST/Messaging entry points.
- **Rules:** 
    - No business logic.
    - Validates request payloads (DTOs).
    - Maps `dto.AppError` to HTTP status codes.
    - **Identity:** Uses `uuid.UUID` for all external IDs.

### Layer 2: Service (Business Logic)
- **Responsibility:** Orchestrate business rules and domain logic.
- **Rules:**
    - Always defined by an **Interface** for mocking.
    - **Identity Resolution:** This is the ONLY layer that resolves `uuid.UUID` into internal `uint` PKs.
    - Manages transactions if they cross multiple repositories.
    - Returns `*dto.AppError` for all failures.

### Layer 3: Repository (Data Access)
- **Responsibility:** Direct interaction with GORM/Postgres.
- **Rules:**
    - Always defined by an **Interface**.
    - No business logic; only CRUD and raw query optimization.
    - **Identity:** Uses internal `uint` primary keys.
    - Handles database-specific error mapping (e.g., Unique Constraint -> `ErrCodeDuplicate`).

---

## 2. Dependency Rule
Dependencies must always point **inwards** and **downwards**:
`Controller` -> `Service` -> `Repository`

- Layers must only communicate via **Interfaces**.
- Concrete implementations are injected via constructors (`New...` functions).
- Circular dependencies are strictly forbidden.

---

## 3. Data Flow Example (Order Retrieval)
1. **Client** calls `GET /api/v1/orders/{uuid}`.
2. **Controller** parses UUID and calls `Service.GetOrder(ctx, uuid)`.
3. **Service** resolves UUID to `uint` PK using a cache or index lookup.
4. **Service** calls `Repository.FindByID(ctx, pk)`.
5. **Repository** returns the GORM model.
6. **Service** maps the GORM model to a **DTO** and returns it to the Controller.
7. **Controller** returns the DTO to the client.

---

## 4. Cross-Cutting Concerns
- **Observability:** `context.Context` is carried through all layers to propagate `request_id`.
- **Time:** All layers use `internal/utils/time.go` to ensure consistent timezones and mockability.
- **Transactions:** Atomic operations are managed at the Repository or Service level using GORM's `db.Transaction`.
