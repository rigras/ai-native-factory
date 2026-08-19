# Ticket template (user-story format — "HU" in Spanish-speaking teams)

Each ticket file `T-<NNN>-<kebab-slug>.md` contains **exactly** these sections, in this
order. *Technical Notes* is the only conditional section (include only if it applies);
all others are mandatory. Guide comments (`<!-- -->`) and bracketed placeholders are
for the author — never copy them into the output. Write content in the language the
project's docs use; keep IDs (`T-NNN`) and states in the canonical form.

```markdown
# [Short, descriptive title]

## Metadata
- **ID**: T-NNN · **Version**: v1 · **State**: pending-approval
- **Traces to**: PRD objective(s) [O1, O2] · TRD section(s) [§2, §5]
- **Depends on**: [none | T-00X, T-00Y] · **Wave**: [N]
- **Files likely touched**: [best-effort estimate — paths or areas]

## Goal
[1-3 sentences: what problem this ticket solves and what value it delivers.]

## User Story
As a [concrete actor of the product — a person or an AI agent]
I want [action]
So that [genuine benefit, not a paraphrase of the action]

## Functional Description
[Step by step, without ambiguity: what the user does, what the platform does, how the
system responds, what validations exist, what states the entity/feature can have, what
errors are handled. Any decision made to fill a spec gap is stated explicitly here.]

## Main Flow
1. [Actor] [action].
2. [System] [response].
N. [Successful outcome.]

## Alternative Flows
- **[Alternative / error case]**: Given [triggering condition] → the system
  [response: message, state, recovery].
<!-- Cover failed validations, invalid data, missing permissions, integration
failures, and empty states where they apply. -->

## Acceptance Criteria
<!-- Maximum FIVE. Given/When/Then, verifiable, unambiguous. More than five means the
ticket is too big — split it. Tie criteria to the PRD's metrics where possible: not
"works well" but "responds 200 and persists the record with state `active`". -->
1. **[Criterion name]**
   - Given [initial context/state]
   - When [action/event]
   - Then [observable expected result]

## Technical Notes *(only if applicable)*
<!-- Constraints only (permissions, security, performance, integrations, audit,
traceability) citing the TRD section they come from. NO implementation design — no
framework choices, no DB schemas. Delete the section entirely if nothing applies. -->
- [Relevant constraint — TRD §X]

## Agent Validation
Checklist for a reviewer agent to determine the ticket was implemented correctly:

- [ ] Every acceptance criterion passes.
- [ ] No contradictions between expected behavior and the implementation.
- [ ] No open functional decisions remain.
- [ ] Main and alternative flows fully covered.
- [ ] No semantic ambiguity.
- [ ] The functionality is automatically testable.
- [ ] The implementation stays consistent with the other tickets.
- [ ] Documentation remains in sync with implemented behavior.
- [ ] No hidden functional dependencies.
- [ ] Behavior can be validated by another agent without human intervention.
- [ ] [Ticket-specific, actionable check — e.g. "the endpoint rejects payloads missing
      field X with 422".]
```

The **Agent Validation** section is what makes the ticket a contract: `/code-review`
and `/validate` check against it, so every line must be objectively verifiable. Always
add at least one ticket-specific check — a checklist that is only generic is not
actionable.
