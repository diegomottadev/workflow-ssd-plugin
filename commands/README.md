# 🎮 Workflow Orchestration Commands

> This directory contains the orchestration guides for Claude Code **Slash Commands**.
> Each command defines the step-by-step procedure, validation checks, security gates, and agent handoffs required to execute complex workflows reliably.

---

## 📋 Commands Catalog

| Command | File | Purpose |
|---|---|---|
| `/ssd-init` | [`ssd-init.md`](ssd-init.md) | Bootstraps OpenSpec folders, starter templates, and `CLAUDE.md` in any project. |
| `/scope` | [`scope.md`](scope.md) | Living, coarse scope document per initiative (`docs/scope/`) that evolves as `/change` phases start and finish. |
| `/change` | [`change.md`](change.md) | Full Spec-Driven lifecycle: `explore`, `propose`, `ff`, `analyze`, `apply`, `verify`, `review`, `sync`, `archive`, `list`. |
| `/feature` | [`feature.md`](feature.md) | Implements a technical ticket from `docs/tickets/` using strict TDD (🔴 Red → 🟢 Green → 🔵 Refactor). |
| `/refine` | [`refine.md`](refine.md) | Refines user requests and requirements into Given-When-Then stories, example tables, and tickets. |
| `/ticket-translator` | [`ticket-translator.md`](ticket-translator.md) | Performs rapid technical triage on bug reports or support issues, mapping them to codebase components. |
| `/debug` | [`debug.md`](debug.md) | Root-causes a bug (reproduce → localize → hypothesize → test) before fixing it via the strict TDD cycle. |
| `/commit` | [`commit.md`](commit.md) | Generates structured Conventional Commit messages matching git diffs and ticket references. |
| `/document` | [`document.md`](document.md) | Drafts PR descriptions, changelog entries, release notes, or postmortems from real git history. |
| `/audit` | [`audit.md`](audit.md) | Generates or gap-fills nested `AGENTS.md` context files per area in a monorepo. |
| `/compress` | [`compress.md`](compress.md) | Compresses conversation context while preserving decisions and pending tasks. |
| `/preserve` | [`preserve.md`](preserve.md) | Freezes current session state into a log artifact for future resumption. |
| `/resume` | [`resume.md`](resume.md) | Restores context from a previous preserved session file. |
| `/meta-prompt` | [`meta-prompt.md`](meta-prompt.md) | Rewrites and refines user prompts using prompt engineering techniques. |
