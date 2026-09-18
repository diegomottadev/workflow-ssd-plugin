---
description: Spec-driven lifecycle (OpenSpec) — manages proposal, design, tasks, and delta specs in openspec/changes/ orchestrating specialized agents. Actions: explore | propose | ff | analyze | apply | verify | review | sync | archive | list.
argument-hint: explore <idea> | propose <idea> [--depth prototype|alpha|beta|ga] [--scope <slug>] | ff <idea> [--depth ...] [--scope <slug>] | analyze <name> | apply <name> | verify <name> | review <name> | sync <name> | archive <name> | list
---

# `/change` — Spec-Driven Change Management (OpenSpec)

Orchestrates the lifecycle of a **change** by persisting artifacts in `openspec/changes/<name>/` and updating living specifications in `openspec/specs/<domain>/`.

You are the central orchestrator; subagents do not invoke each other.

**Input:** `$ARGUMENTS` (action + arguments). If empty, display this guide and stop.
Before operating, read `openspec/project.md`.

---

## Workflow Depth

`propose`/`ff` accept an optional `--depth <level>` (default `alpha`). Depth controls which later stages are **required** before `archive` — it never skips writing tests, since strict TDD (skill `tdd-workflow`) is non-negotiable for anything that isn't thrown away.

| Depth | Adds | Use For |
|---|---|---|
| `prototype` | Nothing beyond `apply`. `analyze` and `verify` are optional. | Throwaway spikes you don't intend to merge as-is. |
| `alpha` *(default)* | Requires `analyze` before `apply`, and `verify` before `archive`. | The normal path for real features — matches this plugin's existing default behavior. |
| `beta` | + `verify` must include a test-coverage read on touched files (via `{{COVERAGE_COMMAND}}` if declared in `CLAUDE.md`, else a manual read of what's asserted vs. what's new). | Anything shipping to real users. |
| `ga` | + `review` (independent `code-reviewer` pass) must be **APPROVED**, and a `/document` draft (PR description at minimum) must exist. | Anything going out under a stability/support guarantee. |

Store the chosen depth as `- **Depth:** <level>` in `proposal.md` (added by `propose`/`ff`). `archive` reads it back to decide what it must find on disk before closing the change.

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

### `propose <idea> [--depth <level>] [--scope <slug>]` — Draft Change Package

1. Choose a kebab-case `<name>` and create `openspec/changes/<name>/` using templates from `openspec/templates/`.
2. Run the agent pipeline to generate artifacts:
   - `product-owner` → `proposal.md` — include `- **Depth:** <level>` (default `alpha`) and, if `--scope` was given, `- **Scope:** <slug>`.
   - `ticket-translator` → technical approach in `design.md`
   - `functional-analyst` → enriches `design.md` + delta specs (Given-When-Then + SBE example tables)
   - **User Stories** → `stories.md` with INVEST evaluation and Given-When-Then criteria
   - `spec-reviewer` → audits the spec against Gojko Adzic's SBE quality dimensions; loops until APPROVED (max 3 cycles)
3. **Register in the unified index** (see `docs/stories/README.md` and `docs/tickets/README.md`): append one row per file, pointing at `openspec/changes/<name>/stories.md` and `openspec/changes/<name>/tasks.md`, with `Status: proposed` and `Track: change propose`. Do not copy content — the index only links to it.
4. If `--scope <slug>` was given, run `/scope update <slug>` to link `<name>` to its phase and set that phase `Status: in-progress`.
5. ⛔ **GATE:** Present `proposal.md`, delta specs, `design.md`, `stories.md`, and `tasks.md` to the user and **wait for explicit approval** before `apply`.

#### Quality Checklist for the Gate:
- [ ] Proposal intent matches what was requested
- [ ] No scope creep (no unrequested extras)
- [ ] Every requirement is specific, atomic, and testable
- [ ] Every requirement has a concrete example table (SBE)
- [ ] Passes "The Tester Test" (QA can verify PASS/FAIL without inspecting internal code)
- [ ] Handles edge cases, error states, and permissions
- [ ] Tasks map directly to stories and requirements

---

### `ff <idea> [--depth <level>] [--scope <slug>]` — Fast-Forward Track (Single-Pass Feature)

For changes where **the full scope is clear from the start**: small bugfixes, minor tweaks, direct additions.
Creates `proposal.md`, `design.md`, delta specs, `stories.md`, and `tasks.md` **in a single pass**.

🔴 **`ff` DOES NOT skip the approval gate.** It skips the ceremony of drafting artifacts one by one, but still presents all artifacts to the user for explicit approval.

#### Lean Guardrail:
- `proposal.md`: Max 3 lines (problem and solution), plus `- **Depth:** <level>` (default `alpha`) and `- **Scope:** <slug>` if `--scope` was given.
- Delta spec: Single requirement with compact SBE table (2-3 rows).
- `design.md`: Exact `file:line` touched and code snippet.
- `tasks.md`: Short list of 3 tasks (🔴 failing test, 🟢 minimal code, 🟣 mutation check).

Register in the unified index exactly as in `propose` (Step 3 above), with `Track: change ff`. If `--scope <slug>` was given, run `/scope update <slug>` exactly as in `propose` (Step 4 above).

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

⛔ **Precondition:** Run `analyze` first, unless `proposal.md` declares `Depth: prototype`. Block on `[CRITICAL]` or `[HIGH]`.

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
- Dispatch in parallel: `spec-reviewer` ∥ `dba-optimizer` ∥ `runtime-verifier`.
  - `runtime-verifier` proves the delta spec's SBE examples hold true by actually running the app/CLI/API — not just reading test output. If an example can't be runtime-exercised, it reports **SKIPPED** with a reason rather than assuming PASS.
  - If `runtime-verifier` reports a FAIL, route it to `/debug` for root-cause investigation before re-running `verify` — do not patch it ad hoc inside this step.
- Run the full test suite (`{{TEST_COMMAND}}`).
- **Living Documentation & Completeness:** Match test output against delta spec requirements. 100% of requirements must have passing tests.
- If `Depth: beta` or `ga`, also read coverage on touched files (`{{COVERAGE_COMMAND}}` from `CLAUDE.md` if declared) and report any new logic left unasserted.

---

### `review <name>` — Independent Code Review (Second Pair of Eyes)

Required when `proposal.md` declares `Depth: ga`. Optional but recommended otherwise. Distinct from `verify`: `verify` proves the feature *works at runtime*; `review` proves the *code* is correct, safe, and consistent on a cold read — the way a teammate seeing the diff for the first time would.

- ⛔ **Precondition:** `verify` must have run, with no unresolved FAIL rows.
- Dispatch `code-reviewer` against the diff accumulated since `apply` started. It has no access to the implementer's reasoning — only the diff, the spec, `CLAUDE.md`/`AGENTS.md`, and the codebase itself.
- Verdict:
  - **APPROVED** → proceed to `sync`.
  - **CHANGES_REQUESTED** → route findings back to `apply` (bugs → `/debug` for root cause first); re-run `review` after the fix.

---

### `sync <name>` — Persist Living Truth

- Merge delta specs into living specifications: `openspec/specs/<domain>/spec.md`.
- Update the technical circuit: `openspec/specs/<domain>/circuit.md`.
- Record any database migrations or schema adjustments.
- If `proposal.md` declares `- **Scope:** <slug>`, run `/scope update <slug>` to check whether the linked phase's change is now archivable (its `Status` moves to `done` once `archive` completes).

---

### `archive <name>` — Close and Clean Up

- ⛔ **Precondition:** Delta spec must be synchronized (`sync`). In addition, gated by `Depth` in `proposal.md`:
  - `prototype`: no further gate.
  - `alpha` *(default)*: `verify` must have run with no unresolved FAIL rows.
  - `beta`: + coverage read from `verify` must show no unasserted new logic.
  - `ga`: + `review` verdict must be **APPROVED**, and a `/document` draft (at least a PR description) must exist for this change.
  - Missing a required gate blocks `archive` — report exactly what's missing and which command produces it.
- Dispatch `commit-message-writer` to prepare a Conventional Commit message (do not commit automatically).
- Move `openspec/changes/<name>/` to `openspec/archive/YYYY-MM-DD-<name>/` and set `Status: done` in `proposal.md`.
- **Update the unified index:** find `<name>`'s rows in `docs/stories/README.md` and `docs/tickets/README.md` (added during `propose`/`ff`), set `Status: done`, and rewrite their `Path` to the new `openspec/archive/YYYY-MM-DD-<name>/` location so the links don't rot.
- If `proposal.md` declares `- **Scope:** <slug>`, run `/scope update <slug>` to mark the linked phase `Status: done` and append a Change Log entry.

---

### `list` — List Changes

- Lists all directories in `openspec/changes/` with status, and recent entries in `openspec/archive/`.
- Cross-check against `docs/stories/README.md` and `docs/tickets/README.md`: flag any change on disk with no matching index row (registration was skipped) and any index row whose `Path` no longer exists on disk (stale after a manual move).
