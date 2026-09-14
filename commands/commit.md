---
description: Analyzes pending changes and drafts a descriptive commit message following Conventional Commits. Generates text only — never executes git commit automatically.
argument-hint: [ticket/issue ID, OpenSpec change name, or target scope]
allowed-tools: Bash(git status:*), Bash(git diff:*), Bash(git log:*), Read, Grep, Glob
---

# `/commit` — Prepare Commit Message

Analyzes pending project changes and **drafts a descriptive Conventional Commit message**.
Your deliverable is **only the commit text**: the developer decides when and how to commit.

**Optional Scope:** `$ARGUMENTS` — hint to narrow analysis (a ticket/issue ID, an OpenSpec change, or a specific file/module). If omitted, analyze **all** detected changes.

---

## Golden Rule

**Never execute `git commit`, `git add`, `git push`, `git reset`, or any command that mutates git state.**
Use only read commands: `git status`, `git diff`, `git log`.
Present the drafted message and **stop**.

---

## Step 1 — Detect Changes

```bash
git status --porcelain
git diff            # unstaged changes
git diff --staged   # staged changes
```

If an active OpenSpec change exists, read `openspec/changes/<name>/` (`proposal.md` + `tasks.md`).
**If no changes exist:** declare that the working tree is clean and exit. Do not invent a commit.

---

## Step 2 — Understand What Changed and Why

Inspect the **real diff** of modified files (`git diff` or `Read`/`Grep`).
Describe the **functional change**, not just the list of file paths. If context is insufficient to describe the change accurately, **ask the user rather than guessing**.

---

## Step 3 — Classify the Commit Type

| Type | When to Use |
|---|---|
| `feat` | New user or API feature |
| `fix` | Bug fix |
| `refactor` | Structural cleanup without behavior changes |
| `perf` | Performance improvement (indexes, caching) |
| `docs` | Documentation only (`README.md`, comments, specs) |
| `test` | Adding or updating tests |
| `chore` | Build tasks, configs, dependency bumps |
| `style` | Formatting, whitespace, indentation |

---

## Step 4 — Determine Scope & Description

Format: `<type>(<scope>): <imperative description>`
- **Scope:** The affected module (e.g. `auth`, `billing`, `orders`, `api`).
- **Description:** Imperative mood, present tense, lowercase start, no ending period.
- **Length:** Under 100 characters total.

---

## Output Format

Present the command ready for the user to copy:

```bash
git add <files>
git commit -m "<type>(<scope>): <description>"
```
