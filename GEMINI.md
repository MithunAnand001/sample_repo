# D3 Project Mandates

All implementations and reviews in this project MUST strictly adhere to the following standards and checklists:

@checklists/code-review.md
@checklists/design-and-db.md
@standards/go-engineering-steering.md

## Key Architectural Rules
- **Layered Architecture:** Strictly follow the Controller -> Service -> Repository (N-Tier) pattern.
- **Context Propagation:** `context.Context` MUST be the first parameter of all IO-bound or business logic functions.
- **Error Handling:** All cross-layer errors MUST be of type `*dto.AppError`.
- **Identity Safety:** API/DTO layers use `uuid.UUID`; internal layers use `uint` PKs. Resolution MUST happen in the Service layer.
- **Database Safety:** All multi-table operations MUST be wrapped in a GORM Transaction at the Repository level.
