---
description: Rewrites a raw prompt using prompt engineering principles (role, objective, expected observable outcome, context, constraints, output format) and persists it once approved.
argument-hint: <original prompt to improve>
---

# `/meta-prompt` — Prompt Engineering & Refinement

You are an expert in prompt engineering.
Given an input prompt, structure it using best practices (role, objective, expected result, constraints) to achieve high precision. Stick strictly to the original objective.

---

## Original Prompt:

$ARGUMENTS

---

## Execution Rules

- If `$ARGUMENTS` is empty: prompt the user to provide the original prompt and stop.
- **Do not broaden or shrink the scope.** Clarify what the prompt already requests without inventing extra requirements.
- Retain the **target language** of the prompt.
- If ambiguities exist, resolve them with the most reasonable assumption and document them under **Assumptions**.
- **Every improved prompt must include an `# Expected Result` section before instructions.** Define the observable outcome first (what the final state looks like when complete) and how it is verified.
- **Do not execute the prompt** — your deliverable is the refined prompt text.

---

## Output

Return the improved prompt inside a fenced markdown block (ready to copy), followed by **Assumptions** if applicable.

---

## Persisting Approved Prompts

Only archive prompts after the user approves them ("run it", "looks good", or "save it").
Save approved prompts to:
```
docs/requests/YYYY-MM-DD-<slug>.md
```

### Persisted File Format:
```markdown
---
date: YYYY-MM-DD
objective: <one-line summary>
status: approved
---

## Original Request
<raw input text>

## Improved Prompt
<final prompt text>
```
