---
context: conversation
description: Smart conversation compression with session logging — produces structured preservation notes and saves session logs before compacting conversation context.
allowed-tools: Read, Write, Bash, AskUserQuestion
---

# `/compress` — Conversation Compression with Session Logging

Prepares structured preservation notes before compacting conversation context and saves a searchable log file to `docs/session-logs/`.

**Flow:** `/preserve` (optional) → `/compress` → preservation review → save log → `/compact`

---

## 📋 Step-by-Step Instructions

### Step 1: Identify Key Information to Preserve
Review the current session for:
1. **Key Learnings:** Technical findings or discovered domain rules.
2. **Solutions & Fixes:** Applied code changes, bugfixes, working commands.
3. **Decisions Made:** Architectural choices and trade-offs (why A was chosen over B).
4. **Files Modified:** List of created or updated files.
5. **Setup & Config:** Environment variables or settings adjusted.
6. **Pending Tasks:** Immediate next steps or open blockers.

### Step 2: Suggest Log Slug
Propose a clear kebab-case filename based on the session topic (e.g. `YYYY-MM-DD-refactor-auth.md`).

### Step 3: Save Session Log
Write the log to `docs/session-logs/YYYY-MM-DD-<slug>.md`:

```markdown
# Session Log: {{TOPIC_NAME}}

**Date:** YYYY-MM-DD
**Goal:** ...

## Executive Summary
...

## Technical Decisions
- ...

## Files Modified
- `path/to/file` — description of change

## Current State & Next Steps
- [ ] Task 1...
```

### Step 4: Summary for Compacting
Present a brief summary ready to resume work seamlessly after context compaction.
