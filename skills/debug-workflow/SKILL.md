---
name: debug-workflow
description: Root-cause investigation protocol for bugs and unexpected behavior — reproduce, localize, hypothesize, test, then hand off to the TDD cycle for the fix. Use before touching any production code in response to a bug report.
---

# Debug Workflow — Root Cause Before Fix

```
🔍 REPRODUCE ──► 🔬 LOCALIZE ──► 💡 HYPOTHESIZE ──► 🧪 TEST ──► 🔴 RED ──► 🟢 GREEN ──► ✅ VERIFY
    (bug-investigator)                                          (tdd-test-writer) (tdd-implementer)
```

🔑 **What this is and what it is not.** This is **not** a shortcut to patch a symptom. The first four steps belong to `bug-investigator` and are strictly read-only. The fix itself re-enters the standard TDD cycle (skill `tdd-workflow`): the regression test is written RED first, then the minimal fix turns it GREEN.

---

## Non-Negotiable Rule

> Never skip to a fix on a hunch. An unverified fix is how a symptom gets patched while the bug survives.

If reproduction fails, stop and report — do not guess at a cause you have not observed.

---

## 🔍 1. REPRODUCE

Establish a **deterministic** trigger: a failing test, a script, a curl call, a CLI invocation. Record the exact command. A bug that cannot be reproduced on demand cannot later be proven fixed.

## 🔬 2. LOCALIZE

Narrow the failure to its smallest surface using `git log`/`git blame`, code bisection, or temporary instrumentation. Read actual runtime values — never assume.

## 💡 3. HYPOTHESIZE

State **one** falsifiable root cause at a time, with `file:line`. Do not enumerate multiple suspects and fix them all "just in case" — that hides which change actually mattered.

## 🧪 4. TEST THE HYPOTHESIS

Design the smallest experiment that confirms or refutes the claim. Only once the hypothesis is proven does the loop continue.

## 🔴 5. RED — Regression Test

Hand the proven root cause to `tdd-test-writer`, which writes a failing test that captures the bug's exact symptom (not the internal mechanism). Confirm it fails for the right reason.

## 🟢 6. GREEN — Minimal Fix

Hand the root cause and failing test to `tdd-implementer`, which writes the smallest change that addresses the **root cause**, not the symptom. Resist unrelated refactors or "while I'm in here" changes.

## ✅ 7. VERIFY

Re-run the full reproduction and the full test suite to rule out regressions. Grep the codebase for the same faulty pattern elsewhere (`bug-investigator`'s **Blast Radius** section) and decide whether it also needs a fix.

---

## When to Use `/debug` vs. Other Entry Points

| Situation | Use |
|---|---|
| Something is broken and the cause is unknown | `/debug` |
| A ticket/issue needs triage before deciding scope | `/ticket-translator` (produces a spec, does not root-cause) |
| The cause is already known and it's a small, clear fix | `/change ff` |
| A new capability is needed, nothing is broken | `/refine` or `/change propose` |
