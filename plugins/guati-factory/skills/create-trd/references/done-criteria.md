# TRD done criteria ("definition of ready")

The loop ends when ALL these boxes are checked (or the unchecked ones are explicitly
deferred with an owner and a date).

## Altitude (the TRD is a map)
- [ ] Every aspect sits at **approach + why** level, not implementation.
- [ ] Granular detail (exact prompts, schemas, typed contracts, DDL, ontologies) is
  **deferred** to implementation / tickets, not in the TRD.

## Clarity and traceability to the PRD
- [ ] An engineer understands the system map in 10 minutes (there is an end-to-end
  diagram).
- [ ] Every functional requirement traces to a component/agent; every non-functional
  requirement to a decision; every anti-goal to a safeguard; every business rule to a
  mechanism.

## Coverage per aspect (approach decided, not detail)
- [ ] Orchestration: workflow vs agent, single vs multi, with its why.
- [ ] **Component/agent map**: which exist, their responsibility, and what each
  consumes/produces at a high level (no prompt, no typed schema).
- [ ] Actions/capabilities identified + consolidation and exposure strategy (MCP loaded
  vs code-execution), without each tool's contract.
- [ ] Knowledge: per agent, what it needs and the injection **approach** (without the
  schema/ontology).
- [ ] Memory: what is remembered and the approach; conversational vs product learning.
- [ ] Data/state and multi-tenancy: persistence and isolation **approach** (without the
  data model).
- [ ] Infra: backend + queue/orchestration + deployment, with a **benchmark** of
  options.
- [ ] Guardrails, security, traceability: layered defense, **lethal trifecta** checked
  (which leg is cut), sandbox/secrets out of the model.
- [ ] Failures/uncertainty and HITL: which actions require human approval, idempotency
  on state mutations, and loop bounds (steps + budget + no-progress).
- [ ] Offline + online evals, **reliability metric (pass^k) with a threshold**, and the
  feedback loop.
- [ ] Cost/latency/scaling: budget per execution, **rate-limit ceiling**
  (RPM/ITPM/OTPM), and levers (routing, caching, Batch).
- [ ] Aspects the product does **not** need are marked **N/A** with one sentence of why
  (not filled in for completeness).

## Decisions and method
- [ ] Every important decision has an **ADR** (options compared + why); technologies
  were **researched, not assumed** — every comparison table carries its **verification
  date** (web), not just the guide's snapshot.
- [ ] "Start simple" is justified: every increment of complexity has its why.
- [ ] **Evals are defined** for the key capabilities (eval-driven), with thresholds.

## Feasibility
- [ ] Technical risks listed; the highest-risk ones have a spike/PoC.
- [ ] No blocking question is left without an owner.

## Housekeeping
- [ ] Every template section is filled or marked N/A with one line — including
  metadata (§0), the target autonomy level and out-of-scope (§1), and the phased
  implementation plan mapped to the PRD's phases with exit criteria (§14).
