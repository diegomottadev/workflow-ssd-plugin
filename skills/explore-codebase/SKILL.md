---
name: explore-codebase
description: Answers WHERE something is in the project and WHAT TO DO WITH IT — does not just return a file location, but provides context and an actionable recommendation. Use when asking "where is...?", "where does this value come from?", "who calls...?", "in which file is this defined?", "what breaks if I change this?", or when locating routes, variables, permissions, or entities.
---

# Explore Codebase — Locate and Decide

This skill does **two jobs, and the second one provides the real value**:

1. **Locate** the exact file and line containing what is being searched.
2. **Turn that finding into a decision**: how it works today, what it implies for the project, what options exist, and which one is recommended.

Giving a bare `file:line` without context is incomplete work. When someone asks "where is X?", they usually want to know if they can safely modify it, what risks exist, and the best path forward. Your answer is complete when the developer can make an informed decision without opening multiple files to rebuild context.

---

## 🔍 Search Order

Follow this sequence from cheapest to deepest:

1. **Living Specs and Circuits:** `openspec/specs/<domain>/circuit.md` and `spec.md`.
2. **Project Documentation:** Architecture and onboarding documents.
3. **Text & Pattern Search (Grep / Glob):** Search actual controllers, services, models, and UI components.
4. **Inspect Source File Directly:** Code is the final authority. Always provide clickable `path:line` references.

---

## ⚠️ Four Pitfalls that Ruin a Search

1. **Assuming that what is not explicitly declared does not exist:** Many frameworks resolve dependencies via autowiring, reflection, or naming conventions.
2. **Aliases and Multiple Names:** Frameworks or ORMs may refer to classes by alias, interface, or fully qualified name. Searching only one string misses half the usages.
3. **Outdated or Inactive Files:** Finding a file does not prove it runs in production. Check which entry point actually imports or calls it.
4. **Configured but Unused:** A library or package listed in configs may not be wired into the active runtime flow.

---

## 🧭 From Finding to Recommendation

Cross-reference your findings with project constraints:
- **Separate Evidence from Inference:** Verifiable facts must include `path:line`. Inferred deductions must be labeled explicitly as inferences.
- **Give a Clear Recommendation, Not Just a Menu:** Present options, select the best one, and explain why other alternatives were discarded.
- **A Negative Result is a Result:** "I searched X, Y, and Z and it does not exist" is far more valuable than a guess. Suggest where else it might live.
- **This Skill Does NOT Edit Code:** It locates, explains, and recommends. When the user agrees on the direction, proceed with `architecture-first` or `/change`.

---

## 📤 Output Format

```markdown
## What Was Found
<Key findings with `path:line`, ordered by relevance>

## How It Works Today
<End-to-end execution path, not fragmented snippets>

## Implications & Constraints
<Project constraints that apply and potential regression risks>

## Options
| Option | Technical Trade-off | Risk Level |
|---|---|---|
| Option 1 | ... | Low / Medium / High |

## Recommendation
<Single explicit recommendation with rationale>

## Confidence
Verified in code: <...> · Inferred: <...> · Could not verify: <...>
```
