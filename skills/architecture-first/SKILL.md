---
name: architecture-first
description: Mandatory entry point before writing code. Forces finding an existing analogous file in the codebase instead of inventing new patterns. Use whenever the task is to write, edit, fix, or refactor code (controllers, services, entities, components, views, or queries).
---

# Architecture First

## 🎯 Core Principle

**Do not invent new architecture: follow and reuse existing patterns.**
Before writing or changing any line of code, review project standards and find a real, working example in the codebase that solves an analogous problem.

"It is a quick fix" or "it looks obvious" does not excuse skipping established project conventions.

---

## 🚦 Trigger Rule (Mandatory)

Whenever you need to implement, fix, refactor, or add code, check these questions BEFORE editing:

| Question You Have | Reference to Check | When |
|---|---|---|
| Where does this file belong and how should it be named? | Existing directory structure | Whenever creating or moving a file |
| How should this service or endpoint be structured? | Skill `coding-patterns` | Whenever writing business logic |
| Which language version and syntax can I use? | Project `CLAUDE.md` | Always check runtime limits |
| How should this entity relate to others? | Skill `data-model` | Models, queries, migrations |
| How do I protect this action and check permissions? | Auth & permissions docs | Handlers, routes, views |
| What known pitfalls apply here? | Project docs or previous issues | Before modifying coupled code |

After checking these: **find an analogous implemented case** in the source code and mirror its structure.

---

## 🚩 Red Flags — STOP and Check Patterns

- *"It is only a one-line fix, I do not need to check patterns."*
- *"I will write this controller/service from memory."*
- *"Using hard physical DELETE when the project uses soft deletes."*
- *"Using syntax from newer language versions not supported in production."*
- *"A listing query that fails to filter by tenant, organization, or active status."*
- *"Writing ad-hoc database queries without following repository/model patterns."*

---

## ⚖️ Common Excuses vs Reality

| Excuse | Reality |
|---|---|
| *"It is a trivial change"* | Trivial changes still break conventions. Checking takes seconds. |
| *"I already know the patterns"* | Knowing them is not the same as applying them. Verify against code. |
| *"There is no time to check"* | Fixing code that broke conventions takes longer than checking first. |
| *"This case is completely unique"* | Look for an analogous component first; one almost always exists. |

---

## ✅ Checklist Before Editing Code

- [ ] Did I review `CLAUDE.md` and `openspec/project.md`?
- [ ] Did I check `coding-patterns` and runtime constraints?
- [ ] Did I find an analogous existing file to mirror?
- [ ] Does the code respect the target runtime version?
- [ ] Are soft deletion and data isolation rules respected?
- [ ] Does every mutating operation validate permissions and handle transactions safely?
- [ ] If database changes are needed, are they planned and documented?
