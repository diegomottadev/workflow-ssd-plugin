# 📦 Product Requirements Pipeline (7 Stages)

> The **seven specialized agents** of the requirements pipeline turn a raw, informal user request into **actionable technical tickets** with full end-to-end traceability:
> `Requirement ID → Use Case → Sequence Diagram → INVEST Story → Technical Ticket`.
>
> The pipeline finishes with verified tickets ready for `/feature` to implement using strict TDD.

---

## 👥 The Seven Agents

| Stage | Agent (`name`) | Primary File | Output Artifact |
|---|---|---|---|
| **0** | `requirement-enumerator` | [`00-requirement-enumerator.md`](00-requirement-enumerator.md) | `docs/diagrams/use-cases/<ID>-requirement-inventory.md` |
| **1** | `use-case-generator` | [`01-use-case-generator.md`](01-use-case-generator.md) | `docs/diagrams/use-cases/<ID>-use-cases.md` |
| **2** | `cockburn-validator` | [`02-cockburn-validator.md`](02-cockburn-validator.md) | `docs/diagrams/use-cases/<ID>-cockburn-validation-report.md` |
| **3** | `diagram-generator` | [`03-diagram-generator.md`](03-diagram-generator.md) | `docs/diagrams/sequence/<ID>/<ID>-sequence-diagrams.md` |
| **4** | `diagram-validator` | [`04-diagram-validator.md`](04-diagram-validator.md) | `docs/diagrams/sequence/<ID>/<ID>-diagram-validation-report.md` |
| **5** | `invest-stories` | [`05-invest-stories.md`](05-invest-stories.md) | `docs/stories/<ID>-<slug>.md` |
| **6** | `ticket-generator` | [`06-ticket-generator.md`](06-ticket-generator.md) | `docs/tickets/<PREFIX>-<NN>-<slug>.md` + `openspec/changes/<slug>/` |

---

## 🔄 Pipeline Workflow & Approval Gates

```
0: Enumerator ──► 1: Use Cases ──► 2: Validator ──┐
                         ▲                         │
                         └────── corrections ──────┘
                                                   │
                                          ⛔ GATE 1 (User)
                                                   │
                                                   ▼
                  3: Diagrams ──► 4: Validator ────┤
                         ▲                         │
                         └────── corrections ──────┘
                                                   │
                                          ⛔ GATE 2 (User)
                                                   │
                                                   ▼
                  5: Stories ──► ⛔ GATE 3 (User) ─► 6: Tickets
```

### Core Pipeline Principles

1. **Validators Do Not Rewrite Directly:** Validators in Stages 2 and 4 evaluate rules and emit an audit report. If defects are found, the corresponding generator re-runs in correction mode (up to 3 loops before requiring human intervention).
2. **Coverage Against Origin:** Every artifact at Stages 1, 3, 5, and 6 must trace back to the `FR-XX` IDs defined in Stage 0. This prevents silent omission of business requirements.
3. **User Gates:** Execution stops at key milestones so the human user approves functional scope and architectural direction before proceeding to technical implementation tickets.
