# Ticket writing guide (for humans + AI agents)

These rules are the "process" that turns a spec into quality tickets. Don't cite them
in the output; reflect them in the result.

## INVEST — the quality filter for every ticket

- **Independent**: can be built and delivered without depending on the order of other
  tickets. A real dependency is declared explicitly (`Depends on:`), never left hidden.
- **Negotiable**: describes the *what* and the *why*, not the *how*. Leaves room for
  implementation decisions.
- **Valuable**: delivers observable value to a concrete actor. If you can't name the
  benefit, it's not a ticket.
- **Estimable**: the scope is clear enough to size. Ambiguity makes it unestimable.
- **Small**: fits one PIV loop (a 500-700 line plan, ~20-60 min of execute). Too many
  main-flow steps or more than five criteria → split.
- **Testable**: compliance is checked with objective, ideally automatable, criteria.

## Vertical slicing

Every ticket is a vertical slice through all the layers needed to deliver value (UI,
logic, data, integration) — not a horizontal technical task ("create the table",
"set up the endpoint"). The user or agent must be able to *use* the ticket's result.

Splitting strategies for big stories: by business rule, by flow step, by data
type/input variation, by CRUD operation, by happy path vs error cases, by role/actor.

## BDD

Acceptance criteria describe **observable behavior**, not implementation.
Given/When/Then must be concrete enough that an automated test derives directly from
each criterion. Avoid "the system works correctly"; prefer "the system responds 200 and
persists the record with state `active`".

## User-centered

Start from the actor and their real intent. The "So that [benefit]" must be a genuine
motive. Empty states, errors, and recovery are part of the experience, not an extra.

## Writing for AI-agent development (AI-First)

Agents are primary actors of development, which changes how you write:

- **Minimize interpretation**: an agent must not "guess". Every expected behavior is
  explicit; every gap is closed with a declared decision.
- **Spec as source of truth**: the ticket is the contract. The implementation must be
  derivable from it and validatable against it.
- **Built for the agent loop**: a developer agent implements; a reviewer/QA agent
  validates against the criteria and the Agent Validation checklist. That's why
  criteria must be objective and the checklist actionable.
- **Multi-agent consistency**: different agents (dev, review, QA, docs) read the same
  ticket. Keep terminology consistent across tickets (same names for the same
  entities, states, actions) to avoid semantic collisions.
- **Traceability**: stable ticket ids and sequential numbering enable reliable
  cross-references between tickets, code, and tests.
- **Automatic testability**: if a criterion can't become a test without additional
  human decisions, rewrite it until it can.

## Anti-patterns

- Epics disguised as tickets (too big).
- Vague phrases: "etc.", "among others", "ideally", "should", "handle correctly".
- Acceptance criteria that describe the UI pixel-by-pixel or the technical
  implementation.
- Hidden dependencies between tickets.
- Restating the user story as if it were an acceptance criterion.
- Mixing unrelated functionality in one ticket.
