---
description: Initializes the Spec-Driven Development framework (OpenSpec, directory structure, templates, and CLAUDE.md) in the current project
globs: *
---

# `/ssd-init`: Spec-Driven Development (SSD) Project Initializer

This command bootstraps the current project to operate under the SSD and OpenSpec framework using the agents, commands, and skills provided by this plugin.

---

## 📋 Execution Steps

### 1. Verify and Initialize `CLAUDE.md`
- Check whether a `CLAUDE.md` file already exists in the project root.
- If it **does not exist**:
  - Create it in the root directory using `templates/CLAUDE.md.template`.
  - Inspect project manifests (`package.json`, `composer.json`, `requirements.txt`, `go.mod`, `pom.xml`, `Dockerfile`, etc.) to auto-populate placeholders:
    - Project name and core description.
    - Tech stack, runtime version, and framework.
    - Detected test runner and linting commands.
  - Inform the user which fields were pre-filled and which require review.
- If it **already exists**:
  - Respect existing content and do not overwrite it. Verify that it declares test commands, linter commands, and database rules.

### 2. Create the `openspec/` Structure
Create the following directory structure and files in the project root:
```text
openspec/
├── project.md                 # Business context and domain definitions
├── templates/                 # Reusable change templates
│   ├── proposal.md
│   ├── design.md
│   ├── tasks.md
│   ├── spec-delta.md
│   ├── spec-living.md
│   ├── circuit.md
│   └── stories.md
├── specs/                     # Living domain specifications
├── changes/                   # Active in-progress change proposals
└── archive/                   # Immutable history of completed changes
```

- Copy the template files from the plugin's `templates/openspec/` into the project's `openspec/templates/`.
- If `openspec/project.md` does not exist, generate it from `templates/openspec/project.md.template` and prompt the user to list their initial domain names.

### 3. Create Requirements and Ticket Folders, With a Unified Index
Create the following directories:
- `docs/stories/` (for INVEST user stories)
- `docs/tickets/` (for technical tickets ready for implementation)
- `docs/scope/` (for living scope documents — one per initiative, created via `/scope new`)

Seed each with a master index file, if it does not already exist:
- `docs/stories/README.md` ← from `templates/docs/stories-index.md.template`
- `docs/tickets/README.md` ← from `templates/docs/tickets-index.md.template`
- `docs/scope/README.md` ← from `templates/docs/scope-index.md.template`

🔑 **Why this matters:** `/change propose|ff` and standard-mode `/refine` write `stories.md`/`tasks.md` **inside** `openspec/changes/<name>/`, not under `docs/`. Only `/refine --deep` writes standalone files directly into `docs/stories/`/`docs/tickets/`. Without these index files, anything produced by the fast track is invisible from `docs/`. `/change propose|ff` and the deep pipeline (stages 5–6) both register a row here — see `commands/change.md` and `agents/product-agent/05-invest-stories.md` / `06-ticket-generator.md`. `docs/scope/README.md` is separate: it indexes coarse, long-lived initiatives (`/scope`), not individual stories or tickets.

### 4. Suggest `/audit` for Monorepos (Conditional)
If the project shows multi-area signals (a monorepo tool config — `turbo.json`, `pnpm-workspace.yaml`, `lerna.json`, `nx.json` — or multiple manifests under `apps/`, `packages/`, `services/`), tell the user they can run `/audit` afterward to generate nested `AGENTS.md` files per area. Do not run it automatically — `CLAUDE.md` alone is enough for a single cohesive app.

---

## 🎯 Summary Message to the User

Upon completion, present a summary report containing:
1. Created files and directories.
2. Next steps to complete `CLAUDE.md` and `openspec/project.md`.
3. Ready-to-use commands:
   - `/scope new <idea>`: To turn a raw idea into a living, coarse scope document before slicing it up.
   - `/change explore <idea>`: To research an idea without creating files.
   - `/change propose <idea>`: To draft a change proposal and spec delta.
   - `/feature <ticket>`: To implement a ticket using strict TDD (🔴 Red → 🟢 Green → 🔵 Refactor).
   - `/change review <name>`: To get an independent code review before merging (required at `Depth: ga`).
   - `/debug <bug description>`: To root-cause a bug before fixing it.
   - `/commit`: To generate a structured Conventional Commit message.
   - `/document pr`: To draft a PR description, changelog entry, release note, or postmortem.
   - `/audit`: (monorepos only) To generate nested `AGENTS.md` context files per area.
