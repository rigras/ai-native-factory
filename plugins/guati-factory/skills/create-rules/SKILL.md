---
name: create-rules
description: Derive the AI Layer's foundation — CLAUDE.md global rules, on-demand context modules, and the engineering CONSTITUTION.md — for a codebase that doesn't have them yet. Use on a brownfield codebase with no CLAUDE.md (Brownfield Type A), or on a greenfield once the PRD/TRD exist.
argument-hint: "[optional focus areas]"
---

# /create-rules — Derive the AI Layer from the codebase

Build the foundation of the AI Layer for a codebase that doesn't have one yet. One run
produces two artifacts with **deliberately different sources**:

- **`CLAUDE.md` + `.claude/context/`** are **descriptive**: derived from what the code
  already does, cited to real files — for consistency.
- **`CONSTITUTION.md`** is **normative**: instantiated from best practice
  (`references/constitution-template.md`), NOT from the existing code — it states what
  good code should be here. In an ugly brownfield the constitution does not adopt the
  ugliness; it records the gap.

## Process

### 1. Analyze the real codebase
- `git ls-files`; read the entry points, configs, models, services, routes, and tests.
- Identify the *actual* conventions in use: naming, error handling, the auth pattern(s),
  datetime/timezone handling, how tests are written, logging, and the build/validation
  commands.
- Note inconsistencies or competing patterns (e.g. two auth systems) and decide which is
  the intended/forward one — capture that, mark the other as legacy.

### 2. Draft a LEAN CLAUDE.md (global rules)
Keep it short. **Every rule must trace to something real in the code — cite it (file:line).**
Do not invent aspirational rules the code doesn't follow; capture the *intended* convention
and mark legacy exceptions explicitly.

#### CLAUDE.md structure — order sections by DESCENDING generality

The generated CLAUDE.md must follow this section order, from most general (applies to every
single task) at the top, to most specific at the bottom:

1. **Project one-liner** — what the codebase is and its primary tech stack (1–2 sentences).
2. **Naming conventions** — file names, module names, function/variable casing across the project.
3. **Core code patterns** — the universal patterns every file follows (error handling, logging,
   async conventions, etc.).
4. **Build & validation commands** — how to lint, type-check, test, and build. These are needed
   before every PR and must be accurate.
5. **On-demand context table** — a Markdown table listing `.claude/context/<topic>.md` modules
   and when to load each. Keep this section; it is mandatory.
6. **Hard rules** — GENERAL constraints that apply to every task type. Examples of correct scope
   (adapt to what this repo actually has and enforces — the file:line rule applies here too):
   "always run the validation gate before opening a PR", "never commit secrets", "migrations
   must be reversible". **Do NOT put ultra-specific implementation one-offs here** (e.g.
   "render meeting datetimes through TimezoneAwareTime" or "auth routes use JWT not legacy
   session") — those belong in on-demand `.claude/context/<topic>.md` modules, not in global
   rules. Hard rules go near the **bottom** of CLAUDE.md, not the top.
7. **Miscellaneous / Gotchas** — a running-list catch-all section (see below).

#### Testing conventions — be honest

Document the testing conventions that *actually exist* in the repo. If the project has no tests,
say so. If it only has integration tests but no unit tests, say that. Do not claim a coverage
standard the codebase doesn't enforce.

#### Miscellaneous / Gotchas (mandatory final section)

Add this section at the very bottom of CLAUDE.md:

```markdown
## Miscellaneous / Gotchas

<!-- Running list — add entries as you discover things the agent repeatedly misunderstands -->
- <first entry derived from codebase analysis, if any>
```

This is a catch-all for anything that doesn't fit neatly into the sections above: surprising
import paths, an environment variable that must be set locally, a tool that breaks on Windows,
a pattern the auto-formatter changes back, etc. It grows over time as the team finds new edge
cases.

### 3. Extract on-demand context modules (`.claude/context/`)
For the areas a task would need depth on (architecture map, the subtle/risky pattern,
auth, the IO/export pattern, testing), write a focused `.claude/context/<topic>.md` that
the agent loads only when relevant. Layered, not bloated (on-demand over nested rules).

### 4. Instantiate the CONSTITUTION.md (normative)

Instantiate `references/constitution-template.md` into `CONSTITUTION.md` at the repo
root. This step follows different rules than steps 2-3:

- **Source is the template, not the code.** The template encodes best practice
  (hexagonal architecture as the default, principles with tiebreak tables, two-layer
  testing, the universal negative list, ADR discipline, the enforcement ladder). The
  codebase only supplies the **facts** to fill the template's placeholders — exactly
  `{PROJECT}`, `{DATE}`, `{LINTER}`, and the `{FRAMEWORK}` list; the template's
  instantiation note explains each (including what to do when no structural linter
  exists yet: prescribe one for the stack, record its adoption as a gap/setup task).
- **Hexagonal stays unless it genuinely doesn't apply.** The rule is the template
  note's: section-level defaults stay unless the project genuinely doesn't need them,
  judged with the template's §1.4 question — *does this product have business
  invariants that can break?* If the honest answer is no for the whole product
  (one-off scripts, a static site), the section may be dropped, with a one-line
  justification under the kept heading and **no renumbering**. Never drop it because
  the current code doesn't follow it.
- **Resolve every opinionated framework** the project uses (agent graphs, durable
  workflow engines like Temporal, full-stack frameworks) via the template's §2:
  replace its generic procedure with **one concrete subsection per framework** —
  which layer its primitive occupies, the narrowly-scoped import exception, what
  stays out, and the piece-to-layer mapping table. Record each resolution as an ADR
  file in `docs/adrs/ADR-NNN-<slug>.md` (the same convention `/create-trd` uses; §2
  holds the resolved text, the ADR records the decision and its alternatives).
  Research the framework's current recommended architecture (web) if you are not
  certain; don't guess.
- **Brownfield: fill §14 (Known gaps).** Where the real code contradicts the
  constitution, do NOT soften the principle — record the gap with a citation and a
  migration stance (fix-on-touch / refactor ticket / accepted-with-ADR). Greenfield:
  delete §14.
- Add one pointer line to CLAUDE.md: `CONSTITUTION.md` is the authority on how code is
  written; CLAUDE.md describes the current conventions.
- Write it in the language the project's docs use.

### 5. Confirm with the human
Show the drafted rules + context (citing the code each came from), and the
constitution (flagging every placeholder decision and §14 gap for review). Let the
human refine. The rules are the team's implicit knowledge made explicit; the
constitution is the team's engineering standard made explicit.

## Greenfield variant (no code yet, PRD/TRD exist)

Steps 1-3 assume code; without it, adapt: skip step 1's code analysis and derive a
**minimal** CLAUDE.md instead — project one-liner, the intended stack and conventions
**cited to the PRD/TRD** (doc + section instead of file:line), and the build commands
once they exist (mark them pending). Create `.claude/context/` modules only for what
the TRD already fixes (e.g. the architecture map). Step 4 runs unchanged — the
constitution is normative and needs no code; its framework list comes from the TRD.
Re-run this skill once real code exists to upgrade the descriptive half.

## Output
- `CLAUDE.md` at the repo root — lean global rules + an on-demand context table + the
  constitution pointer.
- `.claude/context/<topic>.md` modules derived from the real code.
- `CONSTITUTION.md` at the repo root — normative engineering principles, instantiated
  from the template, with the framework-coexistence resolutions in §2 and
  (brownfield) the known gaps in §14.
- `docs/adrs/ADR-NNN-<slug>.md` — one per framework-coexistence (or
  ports-location, §1.3) decision made during instantiation.

## Notes
- For a codebase that already has an AI Layer, you **evolve** it for the new epic
  (Type B: update CLAUDE.md, add epic-specific context) rather than deriving from scratch.
- Pair with `prime` (which can pull the ticket/spec) so the rules are anchored to the work.
