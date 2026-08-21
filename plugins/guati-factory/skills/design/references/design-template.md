# Design document template

Every `/design` run outputs `docs/designs/T-NNN-<slug>-design.md` with **exactly**
these sections, in this order. Write content in the language the project's docs use;
keep ticket IDs (`T-NNN`) and the State field canonical. Guide comments (`<!-- -->`)
and bracketed placeholders are for the author — never copy them into the output.

A section that does not apply is **not deleted**: it states why in one sentence.
Absence must always be a decision, never an oversight.

```markdown
# Design — [T-NNN]: [short title]

## State
draft | approved ([who], [YYYY-MM-DD])
<!-- A draft survives sessions but unlocks nothing. Only "approved" allows
/plan-feature to consume this document. -->

## 1. Goal and scope
[3-6 lines: what this delivers, and explicitly what it does NOT do — pointing to the
ticket/component where each excluded thing lives.]

## 2. Global constraints
<!-- The ticket's Given/When/Then transcribed VERBATIM — non-negotiable — plus any
hard rules from the TRD/CONSTITUTION that bind this design, each with its citation.
This section is what /plan-feature copies into the plan's Global Constraints and what
/code-review validates against. Nothing here is paraphrased. -->
1. [GWT criterion, verbatim — ticket §Acceptance Criteria]
2. [Hard rule — TRD §X / CONSTITUTION §Y]

## 3. The frontier decision (the contract)
<!-- Decided BEFORE the internals: the interface this work presents to the rest of
the system — what crosses the boundary, in what form, and why that form won. Name the
alternatives and state the loser's cost. Internals can change later without ceremony;
the contract cannot. -->
- **Contract**: [what crosses the boundary and in what form]
- **Why this form**: [rationale; alternatives considered and why rejected]
- **Contract PR flag**: [none | "creates/changes `<path>` — own PR, approved before
  any dependent ticket runs"]

## 4. The shape (internal architecture)
<!-- Steps / modules / states; what runs parallel vs sequential; what is LLM vs
deterministic code vs human — with a RATIONALE PER BOUNDARY (evaluability,
blame-placement when something fails, reuse, latency). A diagram earns its place when
the flow is non-linear. -->

## 5. Closed decisions
<!-- One line each: what was open when the run started, what was chosen, what was
rejected, why. A decision that would reopen a TRD ADR does not belong here — it
becomes a TRD change-request in its own PR. -->
- [Decision]: chose [X] over [Y] because [reason].

## 6. Verification strategy
<!-- How we will KNOW it works — both layers. This states the strategy; writing the
tests/evals is implementation work. -->
- **Deterministic**: [what gets unit/integration tested — behaviors, not code]
- **Threshold-based** *(only if behavior is probabilistic or budgeted)*:
  [fixtures/golden cases · what is evaluated per step · metric + threshold ·
  no-regression-vs-baseline rule]
  <!-- When it doesn't apply, keep the bullet: "N/A — behavior is fully
  deterministic." -->

## 7. Out of scope
- [Non-goal] — lives in [T-XXX / component / future HU].

## 8. Risks and plan B
| Risk | Mitigation |
|---|---|
| [top risk] | [mitigation] |

**Plan B**: [the documented fallback if the chosen shape degrades — named now, while
the rejected alternatives are still fresh. Cite the §5 decision it would revisit.]
```

## Why these sections (for the author, not the output)

- **§2 is the compliance spine.** Verbatim constraints flow: ticket → design →
  plan's Global Constraints → review. No later session depends on memory.
- **§3 before §4.** The frontier is the decision that outlives the ticket and the one
  parallel tickets depend on; it gets the human's attention first. Changing §4 later
  is cheap; changing §3 is a contract PR.
- **§6 is the two-layer Definition of Done, stated early.** Deterministic always;
  threshold-based only when behavior warrants it. For a plain CRUD design the second
  layer is one "N/A" line — the section costs nothing when it doesn't apply and
  prevents the ungated-LLM-feature failure when it does.
- **§8's plan B is written at design time** because that is when the alternatives are
  freshest — a fallback invented during an incident is a guess; one recorded here is
  a decision.
