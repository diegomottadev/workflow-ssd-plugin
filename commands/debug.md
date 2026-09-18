---
description: Root-cause investigation for a bug or unexpected behavior, then a targeted fix through the strict TDD cycle (regression test first). Distinct from /ticket-translator, which triages without root-causing.
argument-hint: <bug description, error log, failing test name, or ISSUE-ID>
---

# `/debug` — Root Cause, Then Fix

Investigates a bug through evidence, not guesswork, then closes the loop with a regression test and a minimal fix.

**Input:** `$ARGUMENTS` — a bug description, error message/stack trace, failing test name, or issue identifier. If empty, ask the user for the observed behavior, the expected behavior, and reproduction steps.

Load skill `debug-workflow` before starting.

---

## Golden Rule

**No production code changes happen before the root cause is proven.** Investigation and fix are separate phases with a gate in between.

---

## Step 1 — Investigate (Read-Only)

Dispatch `bug-investigator` to run the reproduce → localize → hypothesize → test loop. It must not write or edit any file.

## Step 2 — ⛔ GATE: Present the Root Cause Report

Show the investigator's report (reproduction, root cause with `file:line`, evidence, blast radius, proposed minimal fix) to the user and **wait for confirmation** before touching code. If the bug could not be reproduced, stop here and report that explicitly — do not proceed to a fix.

## Step 3 — 🔴 RED: Regression Test

Dispatch `tdd-test-writer` with the investigator's **Regression Test Spec**. It writes a failing test capturing the bug's observable symptom. Confirm it fails for the expected reason.

## Step 4 — 🟢 GREEN: Minimal Fix

Dispatch `tdd-implementer` with the investigator's **Proposed Minimal Fix** and root cause. It writes only the change needed to turn the regression test green — no unrelated refactors.

## Step 5 — Verify No Regressions

Run the full project test suite (`{{TEST_COMMAND}}`) and re-run the original reproduction from Step 1. Both must now pass.

## Step 6 — Blast Radius Check

If `bug-investigator` flagged other locations with the same faulty pattern, report them to the user as follow-up candidates — do not silently fix them in the same pass unless the user asks.

---

## Next Steps

- `/commit` to draft the fix's commit message.
- If the same bug reveals a missing spec/requirement, escalate to `/change ff` (small) or `/change propose` (larger) instead of patching further inside `/debug`.
