# 🤖 Project Specialized Agents Catalog

> This directory defines the specialized subagents that form the AI-assisted engineering team.
> Each agent has a focused role, dedicated tools, and strict boundaries to guarantee high code quality without overlapping responsibilities.

---

## 👥 Role & Agent Matrix

| Agent | File | Primary Responsibility | Assigned Tools |
|---|---|---|---|
| **Product Owner** | [`product-owner.md`](product-owner.md) | Clarifies business value, user needs, scope, and functional split. | Read, Grep, Glob |
| **Functional Analyst** | [`functional-analyst.md`](functional-analyst.md) | Enriches technical specs with edge cases, DoD, and Example Mapping tables. | Read, Grep, Glob |
| **Spec Reviewer** | [`spec-reviewer.md`](spec-reviewer.md) | Audits specs for completeness, consistency, clarity, and testability. | Read, Grep, Glob |
| **TDD Test Writer** | [`tdd-test-writer.md`](tdd-test-writer.md) | 🔴 RED Phase: Writes failing tests (unit, integration, or E2E). Never writes implementation code. | Read, Write, Edit, Grep, Glob, Bash |
| **TDD Implementer** | [`tdd-implementer.md`](tdd-implementer.md) | 🟢 GREEN Phase: Writes minimal code needed to make tests pass. Never modifies test files. | Read, Write, Edit, Grep, Glob, Bash |
| **TDD Refactorer** | [`tdd-refactorer.md`](tdd-refactorer.md) | 🔵 REFACTOR Phase: Cleans up and removes duplication while keeping tests green. | Read, Write, Edit, Grep, Glob, Bash |
| **Fullstack Developer** | [`fullstack-developer.md`](fullstack-developer.md) | Direct implementer for small fixes or fast-forward tracks (`/change ff`). | Read, Write, Edit, Grep, Glob, Bash |
| **DBA & Optimizer** | [`dba-optimizer.md`](dba-optimizer.md) | Audits queries, schema design, indexes, and migrations to ensure database health. | Read, Grep, Glob |
| **Commit Message Writer** | [`commit-message-writer.md`](commit-message-writer.md) | Drafts clear Conventional Commit messages traceable to tickets and changes. | Read, Grep, Glob |
| **Product Pipeline** | [`product-agent/`](product-agent/) | 7-agent pipeline turning raw user requests into actionable technical tickets. | Read, Grep, Glob |

---

## 🔄 The Strict TDD Cycle

```
   [Requirement / Ticket]
            │
            ▼
┌───────────────────────┐
│ 🔴 tdd-test-writer    │ ◄── Writes failing test confirming the missing behavior
└───────────┬───────────┘
            │
            ▼
┌───────────────────────┐
│ 🟢 tdd-implementer    │ ◄── Writes minimal production code to reach green
└───────────┬───────────┘
            │
            ▼
┌───────────────────────┐
│ 🔵 tdd-refactorer     │ ◄── Improves code structure while keeping tests green
└───────────────────────┘
```

> ⚠️ **Golden Rule:** No agent enters another agent's domain. The Test Writer never writes production code, the Implementer never modifies tests, and the Refactorer never changes observable behavior.
