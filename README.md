# AI Layer Starter Pack

The reusable **AI Layer** for agentic engineering - the skills, agents, and reference
docs you install **once** into any codebase. This is the generic foundation used in the
free *"AI-Native Engineering Org"* workshop: you install this, then **derive the rest from
your own code** and wire it into your team's process.

> **Install once → customize from your codebase.** The pack is intentionally generic.
> The codebase-specific part of the AI Layer - your `CLAUDE.md` rules and on-demand
> `.claude/context/` modules - you generate with the **`/create-rules`** skill below,
> reading *your* actual code. That's the whole idea: the AI Layer is your team's own
> knowledge and process, encoded.

## Install (as the GuatiFactory plugin)

This repo is a Claude Code **plugin marketplace** (`ai-factory`) hosting the
**`guati-factory`** plugin (all the skills, agents, references, and the Atlassian MCP
wiring). Install it once at user scope and the whole AI Layer is available in
**every** repo you open:

```
/plugin marketplace add rigras/ai-native-factory
/plugin install guati-factory@ai-factory          # choose "User" scope
```

Once installed, skills are invoked with the plugin namespace: `/guati-factory:prime`,
`/guati-factory:spec`, `/guati-factory:plan-feature`, etc.

**Per-team repos** — to have the marketplace auto-registered for anyone who clones a
project, add this to that repo's `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "ai-factory": {
      "source": { "source": "github", "repo": "rigras/ai-native-factory" }
    }
  },
  "enabledPlugins": { "guati-factory@ai-factory": true }
}
```

**Updates**: bump the `version` in `.claude-plugin/marketplace.json` when the plugin
changes; consumers pick it up with `/plugin marketplace update ai-factory` (or by
enabling auto-update for the marketplace in `/plugin`).

**Extras not shipped in the plugin**: the PR review workflow is copied per-repo
(`cp -r .github <your-repo>/.github`, needs a `CLAUDE_CODE_OAUTH_TOKEN` repo secret).

Then, in each project: run `/guati-factory:create-rules` to derive `CLAUDE.md` +
`.claude/context/` + `CONSTITUTION.md` from your real code, and
`/guati-factory:prime <jira-keys> <confluence-page-ids>` to pull external context (the
plugin ships the Atlassian MCP).

## The workflow, step by step

You just got handed a repo and a list of features. This is the whole flow — every
command is a skill from the plugin (namespace prefix omitted for readability):

**Phase 0 — Land on the repo (once per repo)**

1. `/init-project` — set it up and run it locally (env, deps, DB, migrations, dev server).
2. `/create-rules` — build the repo's AI Layer: a descriptive `CLAUDE.md` +
   `.claude/context/` modules (cited to the real code) and the normative
   `CONSTITUTION.md` (engineering standard: hexagonal architecture by default,
   framework-coexistence ADRs, known-gaps table for messy brownfields).

**Phase 1 — From "some features" to an executable backlog** (rite is proportional:
small well-specified features can skip straight to Phase 2)

3. `/create-prd` — formalize the requirements by iterating over the evidence
   (conversations, reviewer feedback, docs) until the done-criteria checklist passes;
   unresolvables become open questions with owners.
4. `/create-trd` — only if architecture decisions are open: the system map, aspect by
   aspect, each decision researched (web-verified, dated) and recorded as an ADR.
5. `/spec` — the ticket factory: slices the PRD into PIV-sized vertical tickets
   (user-story format, max 5 Given/When/Then criteria, an agent-validation checklist)
   with a dependency graph and wave order. Tickets are born `pending-approval` — **you
   approve before anything is published**; Jira/Confluence are optional.

**Phase 2 — The PIV loop, one ticket per clean session**

6. `/prime` — load the codebase context relevant to the ticket.
7. `/design` — **only when the ticket carries decisions that outlive it** (a new
   contract, boundary, schema, pattern, or eval-worthy behavior): decide the approach
   — 2-3 options, trade-offs, the frontier contract — in a human-gated design doc
   *before* any plan. Trivial pattern-following tickets skip this in two lines (the
   skill classifies itself). Fixing an approach costs a conversation; a plan, a
   sentence; a diff, 300 lines. If designing reveals the ticket is mis-sliced, it
   emits a Change Request back through `/spec`.
8. `/plan-feature` — **P**lan: a one-pass implementation plan; must comply with the
   constitution and the approved design when one exists (and it stops and ratchets
   back to `/design` if it hits an undecided approach). You approve the plan.
9. `/execute` — **I**mplement: build strictly from the approved plan.
10. `/validate` — **V**alidate: tests, type-check, lint, build — the gate before any PR.
11. `/code-review` → `/code-review-fix` — review against the standards, the
    constitution, and the ticket's agent-validation checklist. Then `/commit`.

Independent tickets (Wave 1 of the dependency graph) can run **in parallel** with
`/new-worktrees` → one PIV loop each → `/merge-worktrees`.

**Phase 3 — The feedback loops (what makes the system improve)**

- After a feature: `/execution-report` → `/system-review` — what diverged from the
  plan and which rule/context/skill to tighten so the next ticket goes better.
- On a bug: `/rca` → `/implement-fix` — root cause, fix, regression test, and a rule
  so the bug class can't recur.
- When implementation contradicts the PRD/TRD: run `/spec` again — it detects the
  existing folder, files a Change Request, fixes the **source docs first**, and
  regenerates only the affected tickets (back through your approval gate).

```
/init-project → /create-rules                                   (once per repo)
/create-prd → [/create-trd if needed] → /spec                   (once per feature batch)
per ticket:   /prime → [/design if outliving decisions] → /plan-feature → /execute → /validate → /code-review → /commit
after:        /execution-report → /system-review
```

Your three decision points: approve the backlog (`/spec`), approve each plan
(`/plan-feature`), approve the review. Everything else the agent executes.

## What's in here

**Context & priming**
- `prime` - load codebase context; optionally pull Jira issues + Confluence pages first (`prime [jira-keys] [confluence-page-ids]`, via the Atlassian MCP)
- `prime-backend` / `prime-frontend` - focused priming for one side of a full-stack repo

**Build the layer (codebase-specific, derived)**
- `create-rules` - **derive `CLAUDE.md` + `.claude/context/` from your real codebase** (Brownfield Type A), and instantiate the normative **`CONSTITUTION.md`** (engineering principles: hexagonal architecture, testing strategy, ADR discipline) from best practice. The one you run first per project.

**Product definition (evidence → PRD → TRD → tickets)**
- `create-prd` - build a PRD by iterating over the evidence (conversations, reviewer feedback, spreadsheets) until a verifiable done-criteria checklist passes; gaps become open questions with owners
- `create-trd` - the architecture map, aspect by aspect: research candidates (web-verified, dated), decide with ADRs, defer implementation detail. The bridge between the PRD and `spec`.

**The PIV loop** (Plan → Implement → Validate - the core methodology)
- `design` - decide the **approach** for a ticket that carries outliving decisions (contract, boundary, schema, shape, eval strategy) in a human-gated design doc before the plan; self-classifying — trivial tickets skip in two lines
- `plan-feature` - **P**lan: a context-rich, one-pass implementation plan; binding on the approved design when one exists
- `execute` - **I**mplement: build strictly from the approved plan
- `validate` - **V**alidate: run the project's tests / type-check / lint / build before a PR
- `commit` - structured commit at the end of a loop

**Review**
- `code-review` (+ the `code-reviewer` agent) - first-pass review on a diff/PR
- `code-review-fix` - apply review findings

**System evolution** (improve the AI Layer over time)
- `rca` - root-cause a bug *and* propose a rule + regression test so the class can't recur
- `system-review` - diff intent vs outcome; surface rules/context to tighten
- `execution-report` - capture what a loop actually did vs the plan

**Slicing & parallelism**
- `spec` - the ticket factory: slice an epic / PRD into PIV-sized tickets (user-story format with Given/When/Then criteria + an agent-validation checklist) with a dependency graph and wave order. Local-first (`docs/specs/`), human approval gate before anything is published, Jira/Confluence optional. Feedback on existing tickets runs as Change Requests with selective regeneration.
- `new-worktrees` / `merge-worktrees` - run independent tickets in parallel git worktrees

**Examples / extras**
- `end-to-end-feature`, `implement-fix`, `ast-grep`, `init-project` - additional reusable skills

**Agents:** `code-reviewer`, `system-reviewer`, `research-agent`
**References (universal best-practice):** `architecture-patterns`, `backend-api-best-practices`, `frontend-component-best-practices`, `vertical-slice-architecture`
**MCP wiring:** `.mcp.json` - ships pointing at the **Atlassian MCP** (Jira + Confluence) so `prime` can pull tickets + linked spec pages out of the box; edit it to point at your own stack.

## The diagrams from the workshop

The maps from the live session, free to reuse.

### The AI Layer at a glance

What actually goes in the layer: global rules and on-demand context, skills and agents, then the
wiring (MCP, hooks, LSP) that connects the agent to the tools you already use.

![The AI Layer at a glance](diagrams/ai-layer-at-a-glance.png)

### The same epic, two systems

The whole workshop in one frame. Same Confluence epic down two paths: one where the team burns
its time cleaning up slop, one where it ships. The only difference is the AI Layer.

![Two-lane SDLC map](diagrams/two-lane-sdlc.png)

### The AI-Native SDLC, in detail

The same flow with the tool zones (Confluence, Jira, your IDE, GitHub), the artifact produced at
each step, and the bug-to-rule loop that feeds the layer back into the next ticket.

![The AI-Native SDLC in detail](diagrams/ai-native-sdlc-detailed.png)

## Relationship to the Dynamous Agentic Coding course

This is a **focused subset** for the 2-hour workshop - enough to build the AI Layer and run
the PIV loop + system evolution end-to-end. The full **Dynamous Agentic Coding course** goes
much deeper across many more modules, commands, subagents, and the complete validation,
remote-coding, MCP, and Archon workflows. This pack is the on-ramp.

## License / use

Free to use. Built for attendees of the *"AI-Native Engineering Org Transformation"* workshop, but
you don't need to have been there. Clone it, install it, make it yours.
