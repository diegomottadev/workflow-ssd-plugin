---
name: data-model
description: Conventions and standards for data models, persistence layers, relational integrity, and database safety rules.
---

# Data Model — Persistence Layer & Database Standards

This runbook provides guidelines for interacting with the database, maintaining schema consistency, and avoiding destructive operations.

---

## 1. Data Model Conventions
- Check the project `CLAUDE.md` to see the ORM, Query Builder, or persistence library used (e.g. TypeORM, Prisma, Eloquent, SQLAlchemy, Hibernate).
- **Model Structure:** Follow the existing directory layout and naming conventions for entities and repositories.
- **Timestamps:** Check whether entities use automatic timestamp columns (`createdAt`, `updatedAt`) before assuming they exist in queries or inserts.
- **Soft Deletes:** Respect project policy for logical vs physical deletion (e.g. status flags vs timestamp columns).

---

## 2. Replicated / Read-Only Sources
- If the project synchronizes read-only data from external ERPs or third-party APIs into local cache tables, **never manually write to those replicated tables**, as periodic synchronization will overwrite your changes.

---

## 3. 🚫 Absolute Prohibition of Destructive Commands
- **FORBIDDEN:** Executing manual `DELETE`, `TRUNCATE`, or `DROP` commands against live or shared development databases.
- **NO DESTRUCTIVE MIGRATIONS:** Never run database reset or wipe commands (e.g., `migrate:fresh`, `db:wipe`) on shared environments.
- **Additive Schema Changes:** Schema updates must be backward-compatible and additive (`ADD COLUMN`, `ADD INDEX`, `CREATE TABLE`). Write migration scripts in `design.md` for review before applying.

---

## 4. Query Safety and Performance
- **N+1 Query Prevention:** Always use eager loading (joins or includes) when querying related entities in loops.
- **Strict SQL Compliance:** Be mindful of strict SQL modes (e.g. `ONLY_FULL_GROUP_BY` or strict column length limits) between local and production database engines.
- **Transaction Safety:** Wrap multi-entity writes in database transactions and keep transactions as brief as possible.
