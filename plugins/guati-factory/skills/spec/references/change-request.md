# Change Request (CR) and selective regeneration

All feedback on already-generated tickets is handled as a **Change Request**. The first
step is always to **classify**, because it determines what gets touched and what gets
regenerated.

## Classifying the feedback

**Type A — Ticket ambiguity / defect.**
The ticket is badly expressed, ambiguous, incomplete, or wrong, but the PRD and TRD are
correct. The problem lives in the ticket.
→ Action: **rewrite that ticket** (same ID, version +1). PRD and TRD untouched. May
touch neighboring tickets only when shared terminology must be realigned.

**Type B — Implementation learning.**
Building revealed that reality differs from what is documented: a technical decision
changed, a business assumption was false, a new constraint appeared. The problem lives
in the **sources**.
→ Action: the CR **modifies the PRD and/or TRD first** (source of truth — a reversed
TRD decision gets a new ADR), and **then regenerates only the tickets affected** by
that change.

When in doubt, ask: *is the source document still true?* Yes → Type A. No → Type B.

## Change Request format

Save each CR under `docs/specs/<epic-slug>/change-requests/CR-<NNN>.md`. Numbering is
sequential **per epic** (create the `change-requests/` folder on the first CR):

```markdown
# CR-<NNN> — [short title]

- **Type**: A (ticket defect) | B (implementation learning)
- **Origin**: [who/what triggered it]
- **Description**: [what is wrong / what was learned]
- **Affected sources**: [PRD §x, TRD §y]  (empty for Type A)
- **Affected tickets**: [T-003, T-007]
- **New tickets**: [T-011]  (Type B only, when the learning demands a new slice; omit otherwise)
- **Action**: [ticket rewrite | source edit + selective regeneration]
- **Result**: [what changed, new ticket versions]
- **State**: open | applied
```

## Regeneration policy (so the system doesn't break)

- Regenerate **only** the affected tickets; the rest are not touched. A Type B CR may
  additionally **add** new tickets when the learning demands a new slice (e.g. a
  migration) — assign the next sequential ID and list them under **New tickets**.
- **Stable IDs**: never renumber an existing ticket; bump its version.
- **Keep `spec.md` true**: if the change alters dependencies, refresh the ticket
  index, dependency graph, and wave order in `spec.md`.
- **Consistency**: a regenerated ticket must stay aligned with the untouched tickets
  and the TRD (same entity names, states, and contracts; no contradictions).
- **Back through the gate**: every regenerated ticket returns to `pending-approval`
  and passes the human gate again.
- **Record everything**: update the manifest and changelog (see `traceability.md`)
  with the CR and the new versions. After re-approval, update the published copies
  where they exist: the Jira issues (keys are in the manifest's Jira column) and the
  Confluence breakdown page.
