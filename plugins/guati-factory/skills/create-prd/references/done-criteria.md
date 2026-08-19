# PRD done criteria ("definition of ready")

The loop ends when ALL these boxes are checked (or the unchecked ones are explicitly
deferred with an owner and a date). Check each one.

## Clarity and completeness
- [ ] Every required section of the template is filled (no "TBD").
- [ ] A new reader understands what is being built and why in 5 minutes.

## Value and metrics
- [ ] The problem is backed by evidence (feedback or data).
- [ ] Success metrics are measurable, with baseline and target.

## Evidence coverage (the data is your PRD)
- [ ] Available evidence was inventoried (conversations, authority/reviewer
  rejections, spreadsheets, documents, expert judgment).
- [ ] Every source was translated into requirements, rules, or test cases.
- [ ] If input→output examples exist, the spec covers them and none contradicts it.
- [ ] If there is no golden dataset yet, it is defined as a deliverable with an owner,
  and an initial case set exists derived from rejections/conversations.

## Rule coverage
- [ ] Every rule/warning in the catalog appears as a requirement or acceptance
  criterion.

## (AI) Behavior and evaluation
- [ ] The behavior contract is defined (must / must not).
- [ ] Uncertainty handling and escalation to human are defined.
- [ ] Eval acceptance thresholds exist and evals run on every change.
- [ ] (Non-AI products: these boxes are N/A — say so in one line.)

## Non-functional
- [ ] Security and data privacy defined (encryption, retention, compliance).
- [ ] Target latency, cost, and availability defined.
- [ ] Accessibility and traceability addressed (or marked N/A with one line).

## Feasibility
- [ ] Technical assumptions and risks listed.
- [ ] Feasibility probed for the highest-risk points (e.g. by prompting over real
  cases).
- [ ] TRD referenced (or explicitly pending with `/create-trd` as the next step).

## Open questions
- [ ] Every gap is an open question with an owner; none of the remaining ones is
  blocking. (This box can never be "deferred".)

## Housekeeping
- [ ] Every required section is filled or explicitly marked N/A with one line.
- [ ] The change log records each loop pass (what changed and why).
