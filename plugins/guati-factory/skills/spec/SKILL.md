---
name: spec
description: Use when a PRD (and optionally a TRD) is ready and needs to become an implementable backlog — slicing an epic into PIV-sized tickets with a dependency graph — AND when giving feedback on tickets already generated ("this ticket is ambiguous", "we learned something during implementation, update the affected tickets"). Accepts a local doc path or a Confluence page id; Jira/Confluence are optional extras, never required.
argument-hint: "[prd-path OR confluence-page-id] [optional-jira-epic-key]"
---

# /spec — The Ticket Factory

The bridge between strategic docs and the PIV loop. Converts business context (PRD) +
technical context (TRD, when it exists) into tickets ready to implement, with a human
approval gate and a structured feedback loop. The epic doc is the destination; the PIV
loop is the unit of motion; **tickets are the bridge.**

Everything works **local-first**: the folder under `docs/specs/` is the source of
truth. Jira and Confluence are optional publishing targets that only activate when you
pass the corresponding argument — the full cycle (generate → gate → approve → PIV →
change requests) runs without any tracker.

## Mode detection (run this first)

**Slug rule:** the epic slug is the input doc's base filename, lowercased, with any
trailing `-prd`/`-epic` suffix removed (`invoicing-prd.md` → `invoicing`); for a
Confluence page id, kebab-case the page title the same way. The first run records the
slug in `_manifest.md`; if any existing folder under `docs/specs/` records this input
doc as its source, **reuse that slug** — never derive a second one for the same doc.

Then check `docs/specs/<epic-slug>/`:

- **Folder does not exist → Generation mode** (steps 1-5 below).
- **Folder exists with a `_manifest.md` → Update mode**: do NOT re-slice. Treat the
  user's input as feedback on existing tickets and run the **Change Request flow**
  (step 6). Re-slicing an epic that already has approved tickets destroys traceability.
- **Folder exists without a `_manifest.md`** (interrupted or manual run): rebuild the
  manifest from the ticket files present, show it to the human, and let them decide
  whether to continue generation or treat the input as feedback. Never re-slice
  blindly over existing tickets.

## Generation mode

### Step 1 — Load and validate the sources (quality gate)

Load the PRD: if `$1` is all digits, treat it as a Confluence page id and fetch via the
Atlassian MCP (`getAccessibleAtlassianResources` → `getConfluencePage`, markdown
format); otherwise read the local file. If `$2` (a Jira epic key) is given, fetch the
epic as supplementary context — on conflict, the PRD wins.

Look for the TRD (linked from the PRD, or in `docs/trds/`). Extract: actors,
capabilities, objectives + metrics (from the PRD); stack, constraints, architecture
decisions (from the TRD).

**Gate before slicing:** the PRD must have objectives with **objective, measurable
metrics**. Without them you cannot fabricate a good backlog — do not compensate with
hidden assumptions: **stop** and route the user to `/create-prd` (or `/create-trd` if
the missing piece is technical direction). If only **some** objectives are measurable,
slice those and report the unmeasurable ones as a PRD gap to fix — never invent
metrics. A missing TRD is acceptable for products with no architectural novelty; note
its absence in the breakdown.

### Step 2 — Slice into PIV-sized vertical tickets

Break the epic into tickets. A well-sized ticket:

- Is a **vertical slice** of behavior the user/agent can actually use — never a
  horizontal layer ("create the table", "set up the endpoint").
- Maps to **one structured plan** of 500-700 lines; executes in a single PIV loop
  (roughly 20-60 minutes of execute time). Bigger → split (by business rule, flow
  step, data variation, CRUD operation, happy vs error paths, or actor).
- Passes **INVEST** (independent, negotiable, valuable, estimable, small, testable).

If the PRD is large enough that slices cluster into several coherent groups, **propose
an epic grouping first** (one line per proposed epic + its tickets) and let the human
decide before writing anything. Each approved epic then gets its **own**
`docs/specs/<epic-slug>/` folder with its own `T-NNN` sequence; cross-epic
dependencies are declared in each `spec.md`.

### Step 3 — Map dependencies (the graph)

For each ticket identify what it needs from others: a contract another ticket defines,
shared files, a previous ticket's output. Then produce:

- A `Depends on:` line per ticket (`none` or ticket ids).
- A **dependency graph** (mermaid) plus a wave-based execution order: Wave 1 = tickets
  with no dependencies (can run in **parallel worktrees**, see `/new-worktrees`),
  Wave 2 = tickets unblocked by Wave 1, and so on. Slicing along vertical seams is what
  maximizes Wave-1 width.

### Step 4 — Write the ticket folder (local source of truth)

Create `docs/specs/<epic-slug>/` containing:

- `spec.md` — epic summary (2-3 lines), the ticket index, the dependency graph, and
  the suggested wave order.
- One file per ticket: `T-<NNN>-<kebab-slug>.md` following **exactly**
  `references/ticket-template.md` (structure and section order are non-negotiable;
  writing rules in `references/ticket-writing-guide.md`). IDs are sequential and
  stable. Each ticket carries traceability: which PRD objective(s) it serves and which
  TRD sections constrain it.
- `_manifest.md` and `_changelog.md` per `references/traceability.md`. All tickets
  start in state **`pending-approval`**.

Write ticket content in the language the project's docs use. Do not copy template
comments or placeholders into the output.

### Step 5 — Human gate, then (optional) publishing

Present a concise review summary: ticket list with title, the PRD objective each
covers, the wave order, and any decision you made to fill a gap. **Nothing is approved
by default.** The human can approve all, approve some, or request changes (which enter
the CR flow). Record approvals in the manifest.

**Only after approval**, publish — each target only if it applies:

- **Confluence** (only if `$1` was a page id): create/update a child page of the PRD
  titled `Spec: <epic name> - Ticket Breakdown` with the content of `spec.md`
  (search for an existing page first; update, don't duplicate).
- **Jira** (only if `$2` was given): read the epic to confirm project key; **check
  existing children first** (JQL `parent = <epic-key>`) and skip any slice already
  covered — never create near-duplicates. Create the missing ones
  (`issueTypeName: "Story"`, or `"Task"` for chores) with the ticket body as
  description. **Record each created issue key in the manifest's Jira column**, then
  report created vs skipped.

If neither argument points at a tracker, skip publishing entirely and say so — the
local folder is the deliverable.

## Update mode — Change Requests and selective regeneration

All feedback on existing tickets is a **Change Request**. First **classify** it (full
rules in `references/change-request.md`):

- **Type A — ticket defect**: the ticket is ambiguous, incomplete, or wrong, but the
  PRD/TRD are still true. → Rewrite **that ticket only** (same ID, version +1). Sources
  untouched.
- **Type B — implementation learning**: reality diverged from the sources (a technical
  decision changed, a business assumption was false). → Edit **the PRD and/or TRD
  first**: apply the source edits yourself as part of the CR (the confirmed CR is the
  authorization; a reversed TRD decision additionally gets its new ADR via
  `/create-trd`), **then regenerate only the affected tickets**. A Type B learning may
  also demand a **new** ticket (e.g. a migration) — create it with the next sequential
  ID and list it in the CR under **New tickets**.

Regeneration rules: only the affected tickets; stable IDs (never renumber); keep
terminology/states/contracts consistent with untouched tickets and the TRD; refresh
`spec.md` (index, graph, waves) if dependencies changed; regenerated tickets return to
`pending-approval` and pass the gate again; log the CR in
`change-requests/CR-<NNN>.md`, the manifest, and the changelog. After re-approval,
update the published copies where they exist: Jira issues (keys live in the manifest's
Jira column) and the Confluence breakdown page.

## Output

1. `docs/specs/<epic-slug>/` — spec.md, ticket files, manifest, changelog (whenever
   the quality gate passes; a gate stop produces only the routing report).
2. Confluence child page and/or Jira issues — only when the matching argument was
   passed, and only after the human gate.
3. A concise report: tickets generated/regenerated, their states, open CRs, and what
   you expect from the human (approve / review). Don't repeat full ticket bodies in
   chat.

Each approved ticket then enters its own PIV loop: `/prime` → `/plan-feature` →
`/execute` → `/validate`. The ticket's **Agent Validation** checklist is the contract
`/code-review` and `/validate` check against.

## References

- `references/ticket-template.md` — mandatory ticket structure (user story + GWT
  criteria + agent validation checklist + PIV metadata).
- `references/ticket-writing-guide.md` — INVEST, vertical slicing, BDD, writing for
  AI-agent consumers, anti-patterns.
- `references/change-request.md` — CR format, Type A/B classification, regeneration
  policy.
- `references/traceability.md` — ticket state cycle, manifest and changelog schema.
