---
name: tdd-workflow
description: Quality and testing protocol — Kent Beck's TDD cycle (Red-Green-Refactor) surrounded by two safety nets (characterization testing before, mutation testing after).
---

# TDD Workflow — Development Cycle & Safety Nets

```
⚪ CHARACTERIZE ──►┌─ 🔴 RED ──► 🟢 GREEN ──► 🔵 REFACTOR ─┐──► 🟣 MUTATE
   (Feathers)      └────────── TDD · Kent Beck ───────────┘   (DeMillo et al.)
   pre-safety                                                  do tests catch bugs?
```

🔑 **What is TDD here and what is not.** The **TDD cycle consists of the three middle steps**, as defined by Kent Beck in *Test-Driven Development: By Example* (2002). Phases ⚪ and 🟣 are **safety nets** designed to protect existing production code.

| Phase | Methodology | Goal |
|---|---|---|
| ⚪ CHARACTERIZE | **Michael Feathers**, *Working Effectively with Legacy Code* (2004) | Freeze existing behavior before touching code |
| 🔴🟢🔵 | **Kent Beck**, *TDD by Example* (2002) | The core TDD cycle |
| 🟣 MUTATE | **DeMillo, Lipton & Sayward** (1978) | Verify if the test suite detects regressions |

---

## The Two Core Rules (Kent Beck)

> 1. **Do not write new production code unless an automated test has failed first.**
> 2. **Eliminate duplication.**

---

## 📏 Non-Negotiable Rules

1. **Always follow the cycle in order.** Never skip or invert steps.
2. **Start with the simplest failing test.** The smallest case that proves the feature is missing.
3. **Write only the minimum code needed to pass the test.** You can hardcode values initially (see *How to Reach Green*).
4. **Never edit existing tests without explicit approval.** If a test must change to reach green, stop and report it.
5. **Never skip a step silently.** If any step is omitted, declare the reason explicitly.

---

## ⚪ 0. CHARACTERIZE — Freeze Existing Behavior

**When required:** Whenever touching existing legacy code lacking test coverage.

A *characterization test* **does not assert what the system should do: it captures what it does today**, including quirky behavior. It acts as an alarm if a refactoring inadvertently alters observable behavior.

- Strange behavior or existing bugs **are not fixed here**: assert the actual observed output and comment:
  `// CHARACTERIZATION: documents current behavior, not necessarily desired behavior`.
- End the suite with a **signature test** confirming the overall state.
- Document any surprising findings in `design.md`.

---

## 🔴 1. RED — The Failing Test (SBE & Data Tables)

> *"Write a little test that doesn't work, and perhaps doesn't even compile at first."*

Build your test cases directly from the **Example Mapping (SBE — Gojko Adzic)** table in `spec-delta.md`:
- Structure tests using parameterized data tables (`@dataProvider`, `test.each`, or table-driven tests) matching rows in the spec (`EX-01`, `EX-02`, etc.).
- Assert observable business outcomes (status codes, state transitions, event emissions).
- Run the test suite **to confirm it fails as expected**. A test passing on the first run indicates a testing error or already existing code.

---

## 🟢 2. GREEN — The Minimum Code

> *"Make the test work quickly, committing whatever sins necessary in process."*

Write the simplest code that turns the failing tests green. **Do not touch the tests.**

### The Three Strategies (Kent Beck)

| Strategy | When to Use | What It Means |
|---|---|---|
| **1 · Obvious Implementation** | You know what to type **and** can do it quickly | Write the real implementation directly |
| **2 · Fake It** | You are **not** sure how to implement | Return a constant value, then replace constants with variables |
| **3 · Triangulation** | The design is still unclear after a fake | Generalize **only** when two or more tests demand it |

**Example of "Fake It":**
```ts
// 🔴 Test 1 expects add(2, 3) to return 5.
// 🟢 Fake It: Return constant 5 to turn green quickly.
function add(a: number, b: number): number { return 5; }

// 🔴 Test 2 expects add(10, 1) to return 11. Constant 5 no longer works...
// 🟢 Now generalization is demanded by the test:
function add(a: number, b: number): number { return a + b; }
```

A hardcoded value **must not survive the cycle**. If a value remains hardcoded when entering 🔵, a test case is missing.

---

## 🔵 3. REFACTOR — Remove Duplication

> *"Eliminate all of the duplication created in merely getting the test to work."*

Run this step only with a completely green test suite. Clean up design, rename variables for clarity, and extract small functions without altering observable behavior. The test suite must remain green throughout.

---

## 🟣 4. MUTATE — Do Tests Catch Regressions?

**When required:** On modified files after the suite is completely green.

Mutation testing introduces artificial defects (*mutants*) into production code to verify that test suites catch them. A surviving mutant exposes a coverage gap.

```bash
# Run mutation testing tool according to project stack:
# - JavaScript / TypeScript: npx stryker run
# - Python: mutmut run
# - PHP: php vendor/bin/infection --threads=4 --filter=<ModifiedFile>
```

- Target only files modified in this change, not the entire codebase.
- Address escaped mutants: either add a test that catches the mutant or explain why it is harmless.

---

## ✅ 5. Final Verification

Run the full project test suite to rule out any regressions, displaying the real output in the terminal.
