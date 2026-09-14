---
name: coding-patterns
description: Development standards, design patterns, and architectural consistency rules for the project.
---

# Coding Patterns — Architecture & Engineering Standards

This runbook defines quality and design guidelines to maintain clean, consistent code across any project adopting the SSD workflow.

---

## 1. Strict Runtime Version Compliance
- Always check the **Tech Stack** section in the project's `CLAUDE.md`.
- Never use language features, operators, or syntax introduced in versions newer than the project runtime.
- Respect static analysis and linting rules defined by the team.

---

## 2. Separation of Concerns (SoC)
- **Thin Controllers / Handlers:** The transport layer (HTTP / CLI) should only validate input, call the domain service, and format the output response.
- **Business Logic in Services / Domain:** Calculations, workflows, and state transitions belong in services or domain models, never duplicated in controllers or UI views.
- **Early Authorization:** Any action modifying or accessing protected data must check user permissions at the start of the handler.

---

## 3. Transactions and Data Consistency
- **Atomic Operations:** Any process writing to multiple related tables or entities (e.g. order header and order items) must execute inside an explicit database transaction.
- **No Slow I/O in Transactions:** Never make third-party HTTP calls, send emails, or run heavy file generation inside a database transaction block. Keep transactions short to avoid deadlocks and lock contention.
- **Deletion Policies:** Follow project conventions for soft deletion (status fields, `deleted_at` timestamps, or audit tables).

---

## 4. Analogy and Consistency
- Before creating a new file or class, load skill `architecture-first`.
- Find an existing similar component and follow its patterns for:
  - Method and variable naming conventions.
  - Error handling and exception types.
  - Logging and audit trail format.
