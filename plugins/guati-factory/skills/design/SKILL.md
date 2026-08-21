---
name: design
description: Use when a ticket is about to enter planning and it carries decisions that outlive it — a new contract or interface, a new component or boundary, a data/state model, behavior that needs an eval strategy, or several plausible shapes. Also use when /plan-feature stalls because the approach itself is undecided.
argument-hint: "[ticket-path]"
---

# /design — Decide the Approach Before the Plan

The missing rung between the ticket and the plan. The TRD decides the **system**
("which components exist"); the ticket decides the **WHAT** (observable behavior,
Given/When/Then); `/plan-feature` decides the **files**. This skill decides what sits
between: the **approach** — the contract, the shape, the boundaries — for ONE ticket,
with a human gate, before a 500-line plan silently commits to the first shape the
model thought of.

**Why it exists:** fixing an approach costs a conversation; fixing a plan costs a
sentence; fixing a diff costs 300 lines. The approach is the cheapest correction point
in the whole chain — this skill puts a gate on it.

Chain: `/spec` (ticket approved) → `/prime` → **`/design`** → `/plan-feature` → `/execute`.

<HARD-GATE>
Do NOT invoke /plan-feature, write any code, scaffold anything, or take any
implementation action until the design document's State is **approved** by a human.
Presenting the design and starting the plan in the same breath is skipping the gate.
</HARD-GATE>

## Step 0 — Classify the ticket (the skip rule)

Announce you are using the skill. Read the ticket file **completely**. Then apply one
test:

> **Does this ticket contain a decision that outlives it?**

Concrete signals that it does:

- a **new contract or interface** other tickets/components/teams will consume
- a **new component, service, or boundary** (including an agent, a pipeline step, a subsystem)
- a **data model, schema, or state machine**
- a **pattern the next tickets will copy** (first-of-its-kind in this repo)
- **probabilistic or budgeted behavior** that needs a threshold-based verification
  strategy (LLM output quality, latency/cost budgets, error-rate targets)
- **more than one plausible shape**, where reasonable engineers would argue

**If none apply:** declare the skip in two lines — "T-NNN applies the existing
<pattern>; no outliving decisions; skipping design" — and hand off to `/plan-feature`.
That is a successful run of this skill, not a failure.

**If any apply — or you are in doubt:** run the full process. The ratchet is one-way:
doubt takes the heavier path, and hidden complexity discovered later (e.g. mid-plan)
upgrades back to `/design`. Nothing downgrades mid-task.

## Step 1 — Load the decided context (never re-litigate it)

- The ticket's **Given/When/Then are verbatim, non-negotiable constraints.** They are
  transcribed into the design doc, not paraphrased.
- Load the **TRD/PRD sections the ticket cites**. TRD decisions and ADRs are
  **consumed, never reopened** here. If one looks wrong, that is a change-request to
  the TRD in its own PR — never a silent deviation in a design doc.
- Load `CONSTITUTION.md` (if present) and the repo's existing patterns for the area
  (from `/prime` or by reading now). An existing proven pattern is the default
  candidate; departing from it is a decision to justify in §5.

## Step 2 — Ask what the ticket does NOT resolve

Questions **one per message**, only about the genuinely open space. Never re-ask what
the ticket, TRD, or constitution already decided — the fastest way to lose the human's
trust in this gate is to make them repeat decisions. Prefer multiple choice when the
options are enumerable.

## Step 3 — Propose 2-3 approaches

With trade-offs, **your recommendation first**, and the reasoning explicit. YAGNI
applied to every option: strip anything the ticket does not require. Rejected options
are recorded (they become §5 and §8's plan B — the loser's cost is knowledge).

## Step 4 — Present, gate, write

1. Present the design **in sections** (following `references/design-template.md`),
   asking after each whether it looks right.
2. Write the document to `docs/designs/T-NNN-<slug>-design.md` with State `draft`.
   Write it in the language the project's docs use; keep ticket IDs canonical.
3. Self-review: no TBDs or placeholders, no internal contradictions, no requirement
   interpretable two ways, scope fits one implementation plan.
4. Ask the human to review the file. Only when they approve, set State to
   **approved (name, date)**. A `draft` survives sessions but unlocks nothing.

Terminal state is exactly one: hand off to `/plan-feature`, which treats the approved
design as **binding** (its §2 becomes the plan's Global Constraints; its §3 contract
and §4 shape are not renegotiated by the plan).

## Escape hatches (through existing machinery, never improvised)

- **The ticket is mis-sliced** (designing reveals wrong scope, a hidden dependency, a
  missing ticket): emit a **Change Request via `/spec`** and pause. The source docs
  get fixed first and the affected tickets regenerate through the human approval gate;
  the design resumes as `draft` against the revised ticket.
- **The design creates or changes a shared contract** (`contracts/` or equivalent):
  flag it in §3 as **"own PR — approved before any dependent ticket runs."** This is
  what makes parallel waves (`/new-worktrees`) safe: a wave's contracts are pinned
  before the wave starts.

## Red Flags

| Thought | Reality |
|---|---|
| "The approach is obvious" | Obvious to you now ≠ decided. If it's truly obvious, the skip rule exits in two lines — run Step 0, don't bypass it. |
| "I'll decide the approach inside the plan" | That's the exact failure this skill exists to prevent: an ungated decision buried in 500 lines. |
| "The ticket basically says how" | Tickets state WHAT. If HOW leaked into the ticket, that's a ticket defect — raise it, don't inherit it. |
| "The TRD already designed this" | The TRD chose the system map. It explicitly defers contracts, schemas, and shapes to later work. This is the later work. |
| "This ADR seems wrong, I'll design around it" | ADRs are never reopened silently. Propose a TRD change in its own PR, or comply. |
| "Design approved — I'll code straight away" | The terminal state is /plan-feature. Only /plan-feature. |
| "It grew mid-design, but I'm almost done" | Scope growth beyond one plan = decompose or emit a CR. The ratchet only goes up. |

## Checklist

1. **Classify** — read the whole ticket, apply the outliving-decision test, declare skip or proceed
2. **Load context** — GWT verbatim, cited TRD/PRD sections, constitution, repo patterns
3. **Ask** — one question per message, only about the open space
4. **Propose 2-3 approaches** — trade-offs, recommendation first
5. **Present in sections** — approval per section
6. **Write the doc** — `docs/designs/T-NNN-<slug>-design.md`, State: draft
7. **Self-review** — placeholders, contradictions, ambiguity, scope
8. **Human gate on the file** — State: approved only on explicit yes
9. **Hand off** — to `/plan-feature`, nothing else

## What this skill does NOT do

It does not write the implementation plan (that's `/plan-feature`), does not reopen
TRD decisions, does not slice or resize tickets (that's `/spec`, via CR), and does not
run for tickets that merely apply an existing pattern — the skip rule is part of the
skill, and using it is compliance, not evasion.
