---
description: Creates or gap-fills nested AGENTS.md context files for distinct areas/modules of a monorepo or large codebase, complementing the root CLAUDE.md instead of replacing it.
argument-hint: [area-path, e.g. apps/api or packages/ui]
allowed-tools: Read, Grep, Glob, Write, Bash(git log:*), Bash(find:*)
---

# `/audit` — Nested Context Bootstrapper (AGENTS.md)

Root project context lives in `CLAUDE.md` (created by `/ssd-init`). `/audit` extends that with **nested, area-specific** `AGENTS.md` files for codebases split into distinct modules/apps/packages where a single root file can't capture per-area conventions, stacks, or gotchas.

**Input:** `$ARGUMENTS` — an optional area path (e.g. `apps/api`, `packages/ui`). If omitted, scan the whole repo for candidate areas.

---

## Golden Rule

**Never overwrite curated content.** If a nested `AGENTS.md` already exists, gap-fill only what's missing and ask before touching anything that looks hand-written and current.

---

## Step 1 — Decide Scope

- **Area given (`$ARGUMENTS` is a path):** Audit only that directory.
- **No area given:** Look for signals of a multi-area layout — multiple `package.json`/`composer.json`/`go.mod` manifests, a monorepo tool config (`turbo.json`, `pnpm-workspace.yaml`, `lerna.json`, `nx.json`), or top-level folders like `apps/`, `packages/`, `services/`. If the project is a single cohesive app, tell the user `CLAUDE.md` already covers it and stop — do not create nested files with no distinct content to hold.

## Step 2 — Propose the Area List

List candidate areas found (path + one-line guess at its purpose from its manifest/README). Present the list and **wait for confirmation** before writing anything — the user may want to exclude generated, vendored, or archived directories.

## Step 3 — Audit Each Confirmed Area

For each area:
1. Check whether `<area>/AGENTS.md` already exists.
   - **Exists and looks current:** Skip; report it as already covered.
   - **Exists but stale/incomplete:** List the specific gaps found and ask before editing.
   - **Missing:** Generate from `templates/AGENTS.md.template`.
2. Populate it by reading the area's manifest, entry points, test setup, and any obviously repeated patterns (naming conventions, folder layout) distinct from the root `CLAUDE.md`. Do not repeat root-level rules verbatim — only what's specific to this area.
3. Keep it short: stack/conventions, gotchas, key files, and a pointer back to root `CLAUDE.md`.

## Step 4 — Link Back from Root

Add or update a **Nested Context** row in the root `CLAUDE.md`'s "Where to Find What You Need" table pointing to each nested `AGENTS.md`, so other agents and commands discover them.

## Step 5 — Summary

Report which areas got a new `AGENTS.md`, which were gap-filled, which were skipped as already current, and which were excluded by the user in Step 2.
