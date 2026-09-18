---
description: Turns a raw product idea into a living, coarse scope document in docs/scope/ that evolves as development progresses — the entry point before /change explore or /refine. Actions: new | update | list.
argument-hint: new <idea> | update <slug> | list
---

# `/scope` — Living Scope Document

A scope document is not a spec. It captures **what to build and in what order**, coarsely, before anything is sliced into `/change` proposals or `/refine` tickets. Unlike `proposal.md` (one per change, closed once archived), a scope document **persists and evolves** across the whole lifetime of an initiative — it is the file that answers "where are we on this idea?" without reading every change.

**Input:** `$ARGUMENTS` (action + arguments). If empty, display this guide and stop.

---

## Actions

### `new <idea>` — Create a Scope Document

1. Derive a kebab-case `<slug>` from the idea. Check `docs/scope/<slug>.md` doesn't already exist — if it does, tell the user to use `update` instead.
2. Optionally load skill `explore-codebase` to sanity-check what already exists, so the Build Order isn't proposing something already built.
3. Create `docs/scope/<slug>.md` from `templates/docs/scope.md.template`:
   - Fill **Idea** from the user's input.
   - Draft a coarse **Build Order**: 2-6 phases, each a meaningful slice (not an atomic task). Do not write acceptance criteria here — that's `/refine`'s and `/change propose`'s job once a phase is ready.
   - Set `Status: draft`.
4. Register the row in `docs/scope/README.md` (create from `templates/docs/scope-index.md.template` if the index doesn't exist yet).
5. Present the draft to the user. This is not a hard approval gate like `/change propose` — the scope document is expected to keep changing — but confirm the Build Order looks right before moving on.

---

### `update <slug>` — Advance or Revise a Scope Document

Read `docs/scope/<slug>.md` first; refuse to proceed if it doesn't exist (suggest `new` instead).

Use this when:
- A phase starts: dispatch/received a linked change name — set that phase's `Linked Change(s)` and `Status: in-progress`, set the doc's own `Status: active` if it was `draft`.
- A phase finishes: cross-check `openspec/archive/` (or `docs/tickets/README.md` / `docs/stories/README.md`) for the linked change's current state; if archived/done, set the phase `Status: done`.
- The idea itself changes: append a dated entry to **Change Log**, and edit **Build Order** / **Out of Scope** / **Open Questions** directly — this file is meant to be edited in place, not versioned like a spec delta.
- Every phase is `done`: set the document's own `Status: done`.

Always update `Last Updated` and append one line to **Change Log** describing what moved.

`/change propose --scope <slug>`, `/change ff --scope <slug>`, and `/change archive` call this action automatically when a change was opened with `--scope` — see `commands/change.md`. Manual `/scope update <slug>` is for everything those hooks don't cover (revising the idea, reordering phases, answering open questions).

---

### `list` — List All Scope Documents

Read `docs/scope/README.md` and print the table. Cross-check against `docs/scope/*.md` on disk: flag any file with no matching index row, and any index row whose file no longer exists.
