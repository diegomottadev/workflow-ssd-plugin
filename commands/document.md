---
description: Generates PR descriptions, changelog entries, release notes, or postmortems from real git history and diffs — never invented content. Never pushes, opens, or edits anything outward-facing without explicit confirmation.
argument-hint: pr | changelog | release-note | postmortem [extra context]
allowed-tools: Bash(git status:*), Bash(git log:*), Bash(git diff:*), Bash(git tag:*), Bash(gh pr view:*), Read, Grep, Glob, Write
---

# `/document` — Generate Documentation from Real History

Produces human-facing documentation grounded strictly in the actual diff, commit history, and OpenSpec/ticket artifacts.

**Input:** `$ARGUMENTS` — the document type (`pr`, `changelog`, `release-note`, `postmortem`) plus optional context (version, ticket ID, incident description).

---

## Golden Rule

**Never invent timeline entries, causes, or changes.** Every claim traces back to a `git log`/`git diff` line, an OpenSpec artifact, or something the user stated explicitly. **Never run `git push`, `gh pr create`, or `gh pr edit` without the user confirming the drafted text first.**

---

## Step 1 — Determine the Document Type

If `$ARGUMENTS` doesn't name a type, infer from context and confirm with one question:
- On a feature branch with unmerged commits → likely `pr`.
- A new tag exists / release is being cut → likely `release-note`.
- Otherwise ask directly.

## Step 2 — Gather Evidence

```bash
git status --porcelain
git log <base>..HEAD --oneline     # for pr / changelog
git diff <base>...HEAD             # for pr / changelog
git log <last-tag>..HEAD           # for release-note
```
Also read any related `openspec/changes/<name>/` (`proposal.md`, `stories.md`) or `docs/tickets/` entry, and — for `postmortem` — any `/debug` root-cause report already produced.

## Step 3 — Dispatch `doc-writer`

Pass the gathered evidence and the requested type. `doc-writer` drafts the document per its type-specific format (see `agents/doc-writer.md`) and never fabricates missing facts — it asks instead.

## Step 4 — Present and Confirm

- **`pr`**: Show title + body in chat. Only run `gh pr create`/`gh pr edit` after explicit user confirmation.
- **`changelog`**: Show the exact diff to `CHANGELOG.md` before writing it.
- **`release-note`**: Show the drafted `docs/releases/<version>.md` before writing it.
- **`postmortem`**: Show the drafted `docs/postmortems/<date>-<slug>.md` before writing it; flag any section left incomplete due to missing evidence rather than guessing.

## Step 5 — Write (Only What Was Confirmed)

Write only the file(s) explicitly confirmed in Step 4. Do not perform any git-mutating or outward-facing action (`push`, `gh pr create/edit`) unless the user explicitly asked for it in this step.
