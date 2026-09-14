---
description: Spec-driven lifecycle (OpenSpec) — manages proposal, design, tasks, and delta specs in openspec/changes/ orchestrating specialized agents. Actions: explore | propose | ff | analyze | apply | verify | sync | archive | list.
argument-hint: explore <idea> | propose <idea> | ff <idea> | analyze <name> | apply <name> | verify <name> | sync <name> | archive <name> | list
---

# `/change` — Spec-Driven Change Management (OpenSpec)

Orchestrates the lifecycle of a **change** by persisting artifacts in `openspec/changes/<name>/` and updating living specifications in `openspec/specs/<domain>/`.

You are the central orchestrator; subagents do not invoke each other.

**Input:** `$ARGUMENTS` (action + arguments). If empty, display this guide and stop.
Before operating, read `openspec/project.md`.

---

## Actions

### `explore <idea>` — Preliminary Exploration (Creates NO files)

Optional but recommended when an idea is vague or when checking if a feature already exists.
**Does not create change folders or files on disk.** It is throwaway research.

1. Load skill `explore-codebase` and follow its search sequence: project docs (`CLAUDE.md`, `openspec/project.md`) → Grep across code → confirm with real source files (`file:line`).
2. Answer these four questions:
   - **Does it already exist?** Completely, partially, or not at all.
   - **Which files does it touch?** Specify `file:line`. Flag any high-risk files listed in `CLAUDE.md`.
   - **Is it one or multiple functional requirements?** One change = one requirement. Split if needed.
   - **Are there phantom requirements or contradictions?** Contradictions with living specs.
3. Conclude with an explicit recommendation: proceed with `ff` (clear scope), with `propose` (requires definition), or do not implement.

---

### `propose <idea>` — Draft Change Package

1. Choose a kebab-case `<name>` and create `openspec/changes/<name>/` using templates from `openspec/templates/`.
2. Run the agent pipeline to generate artifacts:
   - `product-owner` → `proposal.md`
   - `ticket-translator` → technical approach in `design.md`
   - `functional-analyst` → enriches `design.md` + delta specs (Given-When-Then + SBE example tables)
   - **User Stories** → `stories.md` with INVEST evaluation and Given-When-Then criteria
   - `spec-reviewer` → audits the spec against Gojko Adzic's SBE quality dimensions; loops until APPROVED (max 3 cycles)
3. ⛔ **GATE:** Present `proposal.md`, delta specs, `design.md`, `stories.md`, and `tasks.md` to the user and **wait for explicit approval** before `apply`.

#### Quality Checklist for the Gate:
- [ ] Proposal intent matches what was requested
- [ ] No scope creep (no unrequested extras)
- [ ] Every requirement is specific, atomic, and testable
- [ ] Every requirement has a concrete example table (SBE)
- [ ] Passes "The Tester Test" (QA can verify PASS/FAIL without inspecting internal code)
- [ ] Handles edge cases, error states, and permissions
- [ ] Tasks map directly to stories and requirements

---

### `ff <idea>` — Fast-Forward Track (Single-Pass Feature)

For changes where **the full scope is clear from the start**: small bugfixes, minor tweaks, direct additions.
Creates `proposal.md`, `design.md`, delta specs, `stories.md`, and `tasks.md` **in a single pass**.

🔴 **`ff` DOES NOT skip the approval gate.** It skips the ceremony of drafting artifacts one by one, but still presents all artifacts to the user for explicit approval.

#### Lean Guardrail:
- `proposal.md`: Max 3 lines (problem and solution).
- Delta spec: Single requirement with compact SBE table (2-3 rows).
- `design.md`: Exact `file:line` touched and code snippet.
- `tasks.md`: Short list of 3 tasks (🔴 failing test, 🟢 minimal code, 🟣 mutation check).

---

### `analyze <name>` — Read-Only Simulation Before Writing Code

**Mandatory step before `apply`.** Simulates task execution in sequence to verify that every task is executable with what exists at that point.

🔴 **Do not write or modify code or specs.** Output a diagnostic report only.

| Severity | Category | Trigger |
|---|---|---|
| **[CRITICAL]** | Coverage Gap | A task consumes an artifact that no prior task or existing code produces |
| **[HIGH]** | Inconsistency | `tasks.md` contradicts `spec.md` or `design.md` |
| **[HIGH]** | Missing SBE Table | A requirement lacks concrete example data for TDD |
| **[MEDIUM]** | Underspecified Task | Task violates the 5 task criteria (non-atomic, no target file) |
| **[LOW-MEDIUM]**| Invalid Order | Two tasks duplicate work, or depend on a step listed later |

`CRITICAL` and `HIGH` findings block execution until resolved.

---

### `apply <name>` — Implementation via Strict TDD

⛔ **Precondition:** Run `analyze` first. Block on `[CRITICAL]` or `[HIGH]`.

The TDD cycle runs in this strict order (skill `tdd-workflow`):
0. ⚪ **CHARACTERIZE** — `tdd-test-writer` captures existing legacy behavior with characterization tests before modifying untested code.
1. 🔴 **RED** — `tdd-test-writer` derives test cases from spec example tables, structures parameterized tests, and verifies legitimate failure.
2. 🟢 **GREEN** — `tdd-implementer` writes the MINIMAL code needed to make failing tests turn green without modifying tests.
3. 🔵 **REFACTOR** — `tdd-refactorer` cleans code structure while keeping tests green.
4. 🟣 **MUTATE** — Verifies test suite effectiveness using mutation testing on touched files.

`fullstack-developer` is reserved for scoped direct fixes via `/change ff`.
Check off tasks in `tasks.md` as they are completed.

---

### `verify <name>` — Verification & Evidence

- ⛔ **Precondition: All tasks under Apply in `tasks.md` must be checked.**
- Run project linter (`{{LINT_COMMAND}}`) and clear cache if needed.
- Dispatch in parallel: `spec-reviewer` ∥ `dba-optimizer`.
- Run the full test suite (`{{TEST_COMMAND}}`).
- **Living Documentation & Completeness:** Match test output against delta spec requirements. 100% of requirements must have passing tests.

---

### `sync <name>` — Persist Living Truth

- Merge delta specs into living specifications: `openspec/specs/<domain>/spec.md`.
- Update the technical circuit: `openspec/specs/<domain>/circuit.md`.
- Record any database migrations or schema adjustments.

---

### `archive <name>` — Close and Clean Up

- ⛔ **Precondition: Delta spec must be synchronized (`sync`).**
- Dispatch `commit-message-writer` to prepare a Conventional Commit message (do not commit automatically).
- Move `openspec/changes/<name>/` to `openspec/archive/YYYY-MM-DD-<name>/` and set `Status: done` in `proposal.md`.

---

### `list` — List Changes

- Lists all directories in `openspec/changes/` with status, and recent entries in `openspec/archive/`.
