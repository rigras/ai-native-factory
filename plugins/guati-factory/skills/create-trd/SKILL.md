---
name: create-trd
description: Use when a PRD exists and the technical design is still open — architecture, stack, orchestration, "how do we build this at system level" — before slicing into tickets. Also use to complete or revise an existing TRD after an implementation learning invalidates a decision.
argument-hint: "[prd-path] [optional output-name]"
---

# /create-trd — The Architecture Map, Aspect by Aspect

The PRD says **WHAT** and **WHY**; the TRD says **HOW at system level**. The TRD is a
**map**: for each aspect it decides the **approach** (what we will do and why), not the
implementation. This skill provides the structure and the steps to produce those
decisions with rigor — it does not come with pre-cooked answers.

Chain: `/create-prd` → `/create-trd` → `/spec` → PIV loop per ticket.

## Altitude: what goes in the TRD and what does NOT

The TRD captures **architecture decisions**, not implementation specs.

- **YES (map):** which components/agents exist and their responsibility; the
  orchestration pattern; what knowledge each part needs and the approach to provide it;
  the approach for memory, data, infrastructure, security, evals, and cost — and **the
  why** behind each choice.
- **NO (later work — tickets / implementation):** exact prompts, typed schemas and
  contracts, data models and DDL, corpus ontologies, tool signatures.

**Simple rule:** if a decision can only be made by writing the prompt or the code, it
does not belong in the TRD — it comes later.

The catalog of aspects to cover is in `references/aspect-guide.md`.

## The guiding principle

Start simple; add complexity only when the result justifies it. Inject the LLM **only
where reasoning adds value**; in regulated or deterministic domains, prefer
**workflow + LLM steps** over autonomous agents. Design evals alongside each capability
(eval-driven). Aspects the product does not need are marked **N/A with one sentence of
why** — never filled in for completeness.

## How decisions are made (the escalation criterion)

Classify every decision before making it:

- **(a) Resolvable from the PRD, the code, or the evidence** → decide, and **record
  it** — in the TRD as an **ADR** (context → options considered → decision →
  consequences, including the negatives). Never leave a decision implicit.
- **(b) Requires current external information** (which framework? how mature is X? what
  does it cost?) → **web research is mandatory before comparing**. Use WebSearch /
  WebFetch, or dispatch parallel `research-agent` instances for several candidates at
  once. Output: a **tradeoff table** (maturity, fit, cost, latency, ops, privacy) with
  the **verification date noted**. Candidate lists in the aspect guide are a snapshot
  and age fast — re-verify, never adopt from memory. If the open question is "which
  performs better?", don't decide on paper: propose a **spike/PoC**.
- **(c) Genuinely the human's call** (scope, budget, business risk, anything evidence
  cannot resolve) → record it as an **open question with an owner** and keep moving;
  the loop does not close while blocking questions remain.

## The loop (per aspect)

For **each aspect** in `references/aspect-guide.md` — start with the highest-risk one:

1. **Define the approach decisions** that aspect demands (the guide lists them); tie
   them to the PRD's functional and non-functional requirements.
2. **Compare options** using the escalation criterion above — level (b) research with
   dated tradeoff tables for anything external.
3. **Decide** and write the **ADR**: context → options → decision → consequences.
4. **Document the approach and the why** in the matching section of
   `references/trd-template.md`, with traceability to the PRD — **at map level**.
   Granular detail (prompts, schemas, contracts, DDL) is deferred to implementation.

Close each pass by checking against `references/done-criteria.md` + the PRD. Anything
still undecidable → **open question with an owner**, or a **spike/PoC** if the doubt is
empirical.

## Inputs

The PRD (`docs/prds/<product>-prd.md` or the path in `$1`) · the as-built reality
(`CLAUDE.md`, `CONSTITUTION.md` if present, and the repo) · any business-logic or agent
diagram · hard constraints · `references/aspect-guide.md`.

## Outputs

- The TRD at `docs/trds/<product>-trd.md` — `$2`, when given, replaces the
  `<product>-trd.md` filename within `docs/trds/` — following
  `references/trd-template.md`.
- ADRs inline in the TRD's ADR section, or as separate files in `docs/adrs/ADR-NNN-<slug>.md`
  when they deserve their own life. Numbering is monotonic; one ADR = one decision;
  "alternatives considered" is never empty.
- Write the document in the language the project's docs use (Spanish docs → Spanish TRD).

## What this skill does NOT do

It does not decide for you and does not descend to implementation. The candidates in
`references/aspect-guide.md` are there to **compare**, not a verdict; the decision and
its rationale are produced by running the loop. TRD decisions are **not silently
reopened** during implementation — a change goes through a new ADR (and `/spec`'s
change-request flow regenerates the affected tickets).
