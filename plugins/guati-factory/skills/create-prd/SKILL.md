---
name: create-prd
description: Use when product requirements need to be formalized into a PRD — whether they live in the current conversation, in evidence files (expert interviews, reviewer feedback, spreadsheets, existing docs), or in a half-finished draft that needs another pass. Also use when asked what belongs in a PRD, or to iterate an existing PRD against new evidence.
argument-hint: "[output-filename or existing-prd-path] [optional evidence paths]"
---

# /create-prd — Build a PRD by Iterating Over the Evidence

With current AI, writing is no longer the bottleneck: deciding **what** to build is. A
PRD is not achieved in one shot; you reach it in **several passes (a loop)** that start
from whatever evidence exists and end when the document meets a **verifiable done
criterion** — not when the template looks full.

The PRD says **WHAT** and **WHY**. The technical design (HOW at system level) is a
separate TRD — produce it with `/create-trd`, referenced from the PRD. They evolve
**in parallel** (engineering probes feasibility while the PRD is drafted), but the TRD
loop starts once the PRD draft is stable enough to trace requirements from — that is
what the `/create-prd` → `/create-trd` → `/spec` chain means.

For AI products, a good PRD is four things at once: a *blueprint* (what and why), a
*behavior contract* (what the system must and must NOT do), a *safety spec*, and an
*evaluation plan* (evals). The phrase that sums it up: **the data is your PRD.**

## Inputs: what counts as evidence

Any evidence about correct behavior and failure modes, from "what you usually have" to
"the ideal". You do NOT need a perfect dataset to start:

- **Conversations with experts/operators** — tacit knowledge, the unwritten rules.
  Usually your starting point.
- **Rejections/observations from an authority or reviewer** — the most valuable input:
  each one is simultaneously a *validation rule* and a *real failure case*. The natural
  seed of your evals.
- **Spreadsheets with notes** — half-structured rule catalogs.
- **Existing documents and templates** of the current process.
- **Input→output examples (golden dataset)** — ideal for measuring, but **not a
  precondition**. "Build the golden dataset" is a deliverable of the loop with an
  owner, not a blocker.
- **Expert gut feeling** where no records exist; mark it as an assumption to validate,
  not a fact.

Evidence files arrive as the arguments after `$1` (or named in conversation) — read
them all during loop step 1. If no evidence files are given, the current conversation
IS the evidence — extract explicit requirements, implicit needs, constraints, and
success criteria from it.

## How decisions are made (the escalation criterion)

Classify every open decision before filling a section:

- **(a) Resolvable from the evidence** → decide and **record it explicitly** in the
  relevant section (never a silent assumption).
- **(b) Requires current external information or an experiment** — market, pricing,
  comparable tools → research it (WebSearch / `research-agent`); when comparing
  options, produce a small tradeoff table with the verification date (same rigor as
  `/create-trd`'s level b). "Can this even be built?" is also level (b): resolve it
  with a **feasibility probe** (prompt over the cases you do have, measure, summarize
  the result).
- **(c) Genuinely the human's call** (scope, budget, business risk — anything evidence
  cannot resolve) → record it in the **Open Questions** section with an owner, and
  keep moving. The loop does not close while blocking questions remain.

## The loop (the heart of the skill)

Repeat — reason, draft, verify — section by section until the done criteria pass. Do
not try to fill the whole PRD in one pass; work the highest-uncertainty sections first.

1. **Inventory the evidence.** Gather conversations, rejections, spreadsheets, docs.
   Turn each rejection/observation into a candidate rule. Note explicitly what is
   missing.
2. **Draft or update one section** of the template below.
3. **Verify the section against three things:**
   - the items of `references/done-criteria.md` that touch this section (the full
     checklist is the exit gate of step 6);
   - the **evidence**: is the text consistent with the conversations and rejections?
     Does any evidence contradict it? (If they contradict, one of the two is wrong —
     resolve it.);
   - the **rule catalog**: is every rule (and every rejection reason) represented as a
     requirement or acceptance criterion?
4. **Turn gaps into open questions** with an owner (including "we don't have
   input→output examples yet").
5. **Ask for human input — only when blocking open questions exist.** The human
   injects context the AI does not have; this step is never automated away. Passes
   with no blocking questions skip it; non-blocking questions stay recorded with their
   owner and work continues.
6. **Evaluate the done criteria.** All checked (or explicitly deferred with owner and
   date), all evidence reflected, all rules mapped, no blocking questions — blocking
   questions can never be "deferred" — → the PRD is ready. Otherwise back to step 2.

Keep a short log per pass (what changed and why) at the bottom of the PRD.

## How evidence maps to sections

- Conversations with experts/operators → problem, personas, tacit rules, behavior
  contract.
- Authority/reviewer rejections → validation requirements + acceptance criteria + test
  cases (the seed of the evals).
- Spreadsheets/rule catalogs → functional requirements.
- Existing docs/templates → scope, current-process context.
- Input→output examples → success criteria, eval dataset.

## PRD structure

Write to `$1` (default: `docs/prds/<product>-prd.md`), in the language the project's
docs use. **Required for every product:** sections 1-6, 9-10, 15-16. Sections marked
**(AI)** (7-8) apply to AI products; omit them with one line of why otherwise.
Sections 11-14 may be marked N/A with one line when they genuinely don't apply. No
"TBD" in required sections — a gap becomes an open question instead.

1. **Executive Summary** — what, for whom, problem solved, expected outcome (2-4
   sentences); MVP goal.
2. **Problem & Context (why)** — user problem and its evidence; why now; cost of not
   doing it.
3. **Target Users & Use Cases** — personas, technical comfort, key needs.
4. **Goals & Success Metrics** — outcomes (not features); measurable metrics with
   baseline and target; anti-goals (what we must not break).
5. **Scope** — In (✅) / Out (❌), grouped by category. Out-of-scope is explicit.
6. **User Stories / Functional Requirements** — per requirement: story ("As a … I want
   … so that …") + verifiable acceptance criteria. Map every catalog rule here.
7. **(AI) Behavior Contract** — what the system MUST and must NOT do; uncertainty
   handling; escalation to human (when and how); tone and guardrails.
8. **(AI) Evaluation Framework** — golden dataset (location/size/categories, or the
   plan + seed source to build it); eval metrics and acceptance thresholds; cadence
   (evals run on every change).
9. **Non-Functional Requirements** — security & data privacy (encryption, least
   privilege, retention, compliance), latency, cost, availability, accessibility,
   traceability.
10. **Technical Feasibility, Assumptions & Risks** — summary only: what we assume
    possible, feasibility-probe results (e.g. "prompted over the N cases we had: 85%"),
    known technical risks + mitigations, **link to the TRD** for the actual design.
11. **Dependencies & Constraints** — systems, teams, data, or decisions this hinges on.
12. **UX & Design** — links to flows/wireframes; key UX notes.
13. **Milestones & Phases** — MVP / pilot / GA with exit criteria per phase.
14. **Continuous Feedback Plan** — how production feedback is captured and fed back
    into the loop and the evals.
15. **Open Questions** — table: question · owner · blocking (yes/no).
16. **Change Log** — one line per loop pass: date, what changed, why.

## Output confirmation

When the loop closes (or pauses on blocking questions): confirm the file path,
summarize the PRD in a few lines, and list the open questions with owners. If the loop
**closed**, point to the next step — `/create-trd` for the technical design, then
`/spec` to slice into tickets. If it **paused**, the next step is resolving the
blocking questions — not the TRD.

## Notes

- Style: professional, concrete examples over abstractions, markdown with checkboxes
  (✅/❌), comprehensive but scannable.
- The done criteria checklist lives in `references/done-criteria.md` — the loop's exit
  gate is that file, not your sense of completeness.
