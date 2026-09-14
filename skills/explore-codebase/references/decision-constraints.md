# Project Constraints That Guide Technical Decisions

Reference for the `explore-codebase` skill. These criteria transform raw technical findings into solid architectural recommendations.

All recommendations must align with the project's `CLAUDE.md` and `openspec/project.md`.

---

## 1. Runtime and Language Version
- What exact language version is deployed to production?
- Any suggested solution must compile and execute cleanly on that runtime without requiring unplanned environment upgrades.

## 2. Persistence and Deletion Policies
- Does the project enforce soft deletes (status flag, `deletedAt` timestamp) or physical deletes?
- Maintain transactional integrity when operating across multiple tables.

## 3. Security and Permissions
- How are endpoints authenticated and authorized?
- New features must seamlessly integrate into the existing permission model.

## 4. 🚫 Prohibition of Destructive Actions
- Absolute ban on destructive DDL statements (`DROP`, `TRUNCATE`, database wipes) on live or shared development databases.
- Schema adjustments must be incremental, additive, and backward-compatible.

## 5. High-Risk Modules
- Identify core classes or "god objects" with heavy incoming dependencies and high transaction volume.
- Changes to these components must be minimal, surgical, and protected by tests to prevent regressions.
