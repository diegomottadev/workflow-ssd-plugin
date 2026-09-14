---
description: Implements a technical ticket from docs/tickets/ via strict TDD — verification, chain context, CHARACTERIZE (⚪ if touching untested legacy code) → RED (tdd-test-writer) → GREEN (tdd-implementer) → REFACTOR (tdd-refactorer) → MUTATE (🟣 mutation testing), code review ∥ dba-optimizer, commit drafting, and checking off the ticket in its change tasks.md.
argument-hint: <path to ticket, e.g. docs/tickets/TASK-01-feature-name.md> [--change <name>]
---

# `/feature` — Implement a Technical Ticket

You take **an already analyzed ticket** and lead it to verified production code. You are the orchestrator: you dispatch subagents with explicit, bounded context. **Subagents do not call each other.**

**Target Ticket:** `$ARGUMENTS`

If `$ARGUMENTS` is empty, list available tickets in `docs/tickets/` with their status and dependencies, and ask the user to pick one. **Do not invent a ticket or implement "the general idea".**

> **Where the ticket comes from:** It is produced by the product agent pipeline (`agents/product-agent/`), which already completed use cases, diagrams, INVEST stories, and technical slicing.
> **This command does not re-analyze.** The ticket contains its scope, acceptance criteria, boundaries, and risks. Re-analyzing would duplicate already validated work.

---

## Workflow

```
[0] Verify that the ticket is ready to implement    ← can stop here if blocked
[1] Load requirements context                       (ticket → user story → use case)
    + find which OpenSpec CHANGE this belongs to
[2] ⛔ GATE: user confirmation  → ticket moves to docs/tickets/process/
[3a] tdd-test-writer     ⚪ CHARACTERIZE → freeze existing legacy behavior without coverage
[3] tdd-test-writer      🔴 RED         → failing test suite matching acceptance criteria
[4] tdd-implementer      🟢 GREEN       → write MINIMAL code to reach green
[5] tdd-refactorer       🔵 REFACTOR    → clean structure with tests green, no behavior change
[5b] Mutation testing    🟣 MUTATE      → verify tests detect regressions
[6] spec-reviewer ∥ dba-optimizer               → parallel audit
[7] commit-message-writer                       → draft commit subject line
[8] Move to done/ and check off in change tasks.md
```

---

### [0] Is This Ticket Ready to Implement?

Read the ticket completely **before modifying anything** and check these four conditions. If any fails, stop and report:

| Check | Action |
|---|---|
| **Type** | `Feature`, `Enabler`, `Quality` → proceed. `Migration` → additive SQL only. **`Spike` and `Chore` are NOT implemented by this command** |
| **Dependencies** | If dependencies exist, verify they are resolved. If an open blocker remains, **the ticket is not ready** |
| **Acceptance Criteria** | If any criterion is marked ambiguous or undefined, **stop** |
| **Risks & High-Risk Files** | Note high-risk modules declared in the ticket or in `CLAUDE.md` |

---

### [1] Load Context & Match OpenSpec Change

Follow references in the ticket up to its **User Story** in `docs/stories/` and **Use Case** in `docs/diagrams/use-cases/`.

Find which active change in `openspec/changes/` references this ticket in its `tasks.md`:
- If matched: load `proposal.md` and delta specs as context.
- If no change matches: warn that the ticket is **orphan** (its living spec will not be automatically updated).

---

### [2] ⛔ GATE: User Confirmation

**Wait for explicit user approval before touching code.**
Present the scope to be implemented, what remains **out of scope**, and noted risks.

Upon approval, move the ticket from `docs/tickets/` to `docs/tickets/process/` and update any links in `tasks.md`.

---

### [3a] Characterization Safety Net (⚪ CHARACTERIZE)

If the ticket modifies **existing code without test coverage**, freeze current behavior with characterization tests first (skill `tdd-workflow`).

---

### [3] Tests First (🔴 RED)

Dispatch `tdd-test-writer` with the ticket's **acceptance criteria and SBE example tables**.
The agent writes tests using the project test framework (`{{TEST_FRAMEWORK}}` in `CLAUDE.md`) and verifies that they fail.

```bash
{{TEST_COMMAND}} tests/.../<TestFile>
```

---

### [4] Implementation (🟢 GREEN)

Dispatch `tdd-implementer` with the red test suite and the ticket scope.
The agent writes the **MINIMAL code** needed to turn tests green.
🔴 **Does not touch test files.**

---

### [5] Refactor (🔵 REFACTOR)

**Only with a green test suite.** Dispatch `tdd-refactorer` to clean code structure without altering observable behavior or tests.

---

### [5b] Mutation Testing (🟣 MUTATE)

If mutation testing is configured, verify that test assertions catch generated mutants in touched files.

---

### [6] Parallel Verification

Run parallel checks:
- `spec-reviewer` → checks correctness against ticket criteria.
- `dba-optimizer` → inspects touched queries or migrations.

---

### [7] Draft Commit Message

Dispatch `commit-message-writer` to generate a single Conventional Commit subject line.
**Do not run `git commit` automatically.**

---

### [8] Move Ticket to `done/` and Check Off in Change

1. Move ticket from `docs/tickets/process/` to `docs/tickets/done/`.
2. Check off `- [x]` in the corresponding change's `tasks.md`.
3. If this was the last ticket in the change, suggest running `/change verify <name>`.
