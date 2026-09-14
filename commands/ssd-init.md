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

### 3. Create Requirements and Ticket Folders
Create the following directories:
- `docs/stories/` (for INVEST user stories)
- `docs/tickets/` (for technical tickets ready for implementation)

---

## 🎯 Summary Message to the User

Upon completion, present a summary report containing:
1. Created files and directories.
2. Next steps to complete `CLAUDE.md` and `openspec/project.md`.
3. Ready-to-use commands:
   - `/change explore <idea>`: To research an idea without creating files.
   - `/change propose <idea>`: To draft a change proposal and spec delta.
   - `/feature <ticket>`: To implement a ticket using strict TDD (🔴 Red → 🟢 Green → 🔵 Refactor).
   - `/commit`: To generate a structured Conventional Commit message.
