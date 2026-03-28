# AI-Assisted Development in D3 Project

This project uses **Gemini CLI** with custom guardrails to ensure all code adheres to our strict Go engineering standards, N-Tier architecture, and database integrity rules.

## 1. How the AI Knows Our Rules
We have a `GEMINI.md` file in the root that acts as a **Foundational Mandate**. Every time you chat with an AI agent in this workspace, it automatically loads:
- `@standards/go-engineering-steering.md` (Naming, API, Observability)
- `@checklists/code-review.md` (Implementation details)
- `@checklists/design-and-db.md` (SOLID, DB Performance, Stock Logic)

**You do not need to remind the AI of these rules; it "innately" knows them.**

---

## 2. Custom Commands
We have implemented two specialized commands to automate project-specific tasks.

### `/review [path/to/file or code]`
Use this to perform an automated audit of your implementation.
- **What it does:** Runs the code against all project checklists and standards.
- **Example:** `/review internal/service/order_service.go`
- **Pro Tip:** You can ask the AI to **fix** the violations it finds:
  > `/review internal/service/order_service.go`
  > 
  > **Instruction:** Fix all identified violations and rewrite the file to be fully compliant.

### `/design [schema/plan]`
Use this before writing code to validate architectural decisions.
- **What it does:** Audits a design or DB schema against our SOLID principles and transaction safety rules.
- **Example:**
  > `/design "I want to add a 'Gift' table that deducts stock from the 'Items' table."`

---

## 3. Best Practices for AI Contributors

### Architecture-First Development
When asking the AI to generate new features, follow this workflow:
1. **Design Check:** Use `/design` to validate your plan.
2. **Generate Code:** Ask the AI to "Implement [Feature] following D3 project standards."
3. **Automated Audit:** Run `/review` on the generated file to ensure no "lazy" code (like `time.Now()` or missing `ctx`) slipped through.

### Handling Standard Violations
If the AI suggests code that violates our standards (e.g., direct string concatenation in SQL), you can point it back to the mandates:
> "This violates our SQL Injection and GORM transaction rules in `@checklists/design-and-db.md`. Please fix it."

---

## 4. Maintenance
If you update any checklist in the `checklists/` or `standards/` folders, the AI context updates automatically. If you add a **new** checklist, ensure you add the `@path/to/new-file.md` reference to the root `GEMINI.md`.

To refresh the AI's memory after a change, run:
```bash
/memory reload
/commands reload
```
