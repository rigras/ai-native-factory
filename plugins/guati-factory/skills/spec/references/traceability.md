# Traceability: states, manifest, changelog

Traceability makes the system auditable and regenerations safe (knowing which ticket
depends on which source).

## Ticket state cycle

```
pending-approval → approved
       ↑              |
       └──────────────┘
   (a Change Request / regeneration
    returns the ticket to pending-approval)
```

- **pending-approval**: generated (or regenerated) and awaiting the human gate.
- **approved**: validated by the human. Only a Change Request can reopen it.

Nothing is ever marked `approved` without explicit human confirmation — the gate is the
system's quality and business control. Publishing to Jira/Confluence happens only for
approved tickets. Every approval is also logged as a dated changelog entry (who/when),
so the manifest's State column always has an audit trail behind it.

## Manifest

Maintain `docs/specs/<epic-slug>/_manifest.md`. Its header records the **epic slug**,
the **source docs** (PRD/TRD paths or Confluence page id, with their version or date if
available), and the Jira epic key if one was used. Then one row per ticket:

| ID | Title | State | Version | PRD objectives | TRD sections | Depends on | Jira | Last CR |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| T-001 | … | approved | v1 | O1, O2 | §2, §5 | none | PROJ-101 | — |
| T-003 | … | pending-approval | v2 | O1 | §3 | T-001 | PROJ-103 | CR-002 |

The Jira column is `—` until (and unless) the ticket is published. It is what makes
"update the corresponding issue after a CR" possible weeks later.

## Changelog

Maintain `docs/specs/<epic-slug>/_changelog.md` in reverse chronological order:

```markdown
## 2026-08-18 — CR-002 (Type B)
- TRD §3 updated: queue framework changed.
- Regenerated: T-003 (v1→v2), T-007 (v1→v2). Back to pending-approval.

## 2026-08-16 — Approval
- Approved by the human gate: T-001…T-010.

## 2026-08-15 — Initial generation
- Created T-001…T-010 from docs/prds/invoicing-prd.md + docs/trds/invoicing-trd.md
  (as read on 2026-08-15). All pending-approval.
```

With this, any person or agent can reconstruct why a ticket is in its current state and
what affected it.
