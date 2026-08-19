# Aspect Guide — designing a system (for the TRD)

> How to use: for **each aspect**, run the mini-loop from the SKILL (define → compare →
> decide → document). Per aspect this guide gives you: **(1) what to decide** (at
> approach level), **(2) what to compare** (candidates to evaluate — *not* a verdict),
> **(3) how to decide** (criteria), and **(4) what to document** (the approach, not the
> detail). Candidate names are a **snapshot and age in months** — re-verify them on the
> web (state, maturity, pricing) before comparing, and note the verification date.

## The TRD is a MAP (the right altitude)

The TRD decides, per aspect, the **approach**: what we will do and why, at system
level. **Not** the implementation. Explicitly deferred to **later** (tickets /
implementation):

- the **exact prompt** of each agent (here only: its role and what it consumes/produces
  at a high level),
- the **typed schemas and contracts** (here only: what information flows in/out,
  conceptually),
- the **data model and DDL** (here only: the data and isolation approach),
- the **corpus ontology/schema** (here only: what knowledge and how it is injected, at
  approach level).

**Rule:** if a decision can only be made by writing the prompt or the code, it is later
work, not TRD work.

## Cross-cutting principles

- **Start simple.** The simplest pattern/stack that solves your worst failure; raise
  complexity only when traces prove it. This also governs **depth** per aspect: go deep
  only where your worst failure lives, and mark **N/A** (with one sentence of why)
  whatever the product does not need — e.g. multi-tenant in a single-user system.
- **The LLM only where it adds value.** Deterministic things (rules, formats,
  compliance) in code; the LLM interprets exceptions. In regulated domains:
  **workflow + LLM steps**.
- **Eval-driven.** Define the evals of a capability before/alongside building it.
- **Traceability.** Every system decision must be reconstructible and cite its source.
  Critical in regulated domains.

## Anti-patterns

Premature multi-agent or heavy framework · "tool soup" · reacting to a failure by
adding more prompt/tools instead of fixing the structure · context drift in long
flows · building without evals · no human oversight on irreversible actions.

## Aspect index

A. Business logic / workflow · B. Component/agent map · C. Orchestration and state ·
D. Actions / capabilities · E. Knowledge and context engineering · F. Memory and
learning · G. Data, state, and multi-tenancy · H. Application and infrastructure ·
I. Guardrails, security, and traceability · J. Failures, uncertainty, and HITL ·
K. Evaluation and observability (LLMOps) · L. Cost, latency, and scaling.

Template mapping: aspects A and C both document into template §2 (architecture and
orchestration); C's HITL pause points go in template §10. Every other aspect maps
one-to-one to template §3-§12 in order.

For products with no AI components, aspects B/E/F/K reduce to their classic versions
(module map, configuration/reference data, caching, tests+monitoring) — apply the same
define → compare → decide → document loop at the same altitude.

---

## A. Business logic / workflow
- **What to decide:** what are the process steps and what role does each component
  play? Is the flow fixed or unpredictable? What triggers each step and what is the end
  condition?
- **What to compare:** model as a **workflow** (fixed steps in code) vs an
  **autonomous agent** vs **hybrid**; patterns (chaining, routing,
  orchestrator-workers, evaluator-optimizer).
- **How to decide:** known, auditable steps → workflow; open-ended path → agent. The
  simplest thing that works.
- **What to document:** the flow diagram, the chosen pattern and why, the mapping
  step → actor (rule / agent / human).

## B. Component/agent map
- **What to decide:** which agents/components exist and what is the **responsibility**
  of each? What does each consume and produce (high level)? What **class** of model
  (fast vs reasoning)? When does it escalate?
- **What to compare:** one multi-purpose agent vs several specialized; single vs
  multi-agent; model class per agent.
- **How to decide:** a single agent first; specialize only on real need; bounded,
  non-overlapping responsibilities.
- **What to document:** the list of agents/components and their responsibility (the
  "agent map"). *Prompt and typed contract → implementation.*

## C. Orchestration and state
- **What to decide:** does your code own the control flow, or the LLM? How do you know
  which step a case is in? Where does it pause for a human?
- **What to compare:** state machine in your code vs an agent framework; deterministic
  routing vs an LLM classifier.
- **How to decide:** in regulated domains, control flow lives in your code; the LLM
  routes, it does not govern.
- **What to document:** the orchestration approach and the HITL pause points. *The
  detailed state model → implementation.*

## D. Actions / capabilities
- **What to decide:** what **actions** does the system need (e.g. extract data from a
  document, query an external source, generate a deliverable, notify a human)? Are they
  consolidated into few, clear tools?
- **What to compare:** build vs integrate each capability; MCP exposure **loading all
  defs vs code-execution on demand** (a context lever: can save ~98% of tokens).
- **How to decide:** minimal set of capabilities without overlap; consolidate to avoid
  "tool soup" (ambiguous decision points = the first failure mode).
- **What to document:** the list of actions/capabilities and the
  consolidation/exposure strategy. *Each tool's contract/signature → implementation.*

## E. Knowledge and context engineering
- **What to decide:** what **knowledge** does each agent need and what is the
  **approach** to provide it?
- **What to compare:** in-context · vector RAG · structured lookup (DB/files) ·
  just-in-time · fine-tuning; structured catalog vs vector store vs knowledge graph.
- **How to decide:** structured, curated knowledge with traceability → lookup /
  in-context, not vector RAG; a vector store only for fuzzy search.
- **What to document:** per agent, what knowledge and the injection approach. *The
  concrete schema/ontology → implementation.*

## F. Memory and continuous learning
- **What to decide:** what is remembered and what for? Separate **user-conversation
  memory** from **product learning** (a living catalog/dataset). Summarization and
  retrieval approach?
- **What to compare:** hand-rolled memory (files + LLM summarization) vs frameworks
  (Letta/MemGPT, Mem0, Zep, LangMem).
- **How to decide:** start with the minimum that adds value; justify persistent memory.
- **What to document:** what is remembered and the approach. *The storage schema →
  implementation.*

## G. Data, state, and multi-tenancy
- **What to decide:** the **approach** for persisting case/session state? Tenant
  isolation strategy? PII handling?
- **What to compare:** isolation via RLS vs schema-per-tenant vs DB-per-tenant; shared
  vs per-tenant reference data.
- **How to decide:** by required isolation, cost, and scale.
- **What to document:** the data/state and isolation approach. *The data model and
  DDL → implementation.*

## H. Application and infrastructure
- **What to decide:** which backend/API? How do async steps and long pauses (HITL of
  hours) execute? Deployment, scaling, base observability?
- **What to compare (benchmark):** backend framework · task queue / durable
  orchestration: **Celery, Temporal, DBOS, Prefect, Inngest, Restate, Dramatiq/Arq**.
- **How to decide:** long pauses with irreversible effects → durable execution; weigh
  ops burden, cost, maturity, fit with your language.
- **What to document:** the benchmark table, the choice, and the deployment diagram.

## I. Guardrails, security, and traceability
- **What to decide:** the layered-defense approach? Trust boundary for external text?
  Does the system hit the **lethal trifecta** (private data + untrusted content +
  exfiltration) and which leg gets cut? **Sandboxing** and secrets handling? What gets
  logged for audit?
- **What to compare:** LLM guardrails vs rules/regex vs moderation; sanitization at the
  chokepoint; OWASP Top 10 LLM coverage; file+network isolation; secrets via proxy (the
  model never sees credentials).
- **How to decide:** one guardrail is never enough; layers + auditable traces. External
  text = data, not instruction. Assume injection is **not** solved → cut one leg of the
  trifecta (the practical one: the exfiltration vector).
- **What to document:** the defense layers, the trust boundary, which trifecta leg is
  closed, and the sandbox/secrets approach.

## J. Failures, uncertainty, and HITL
- **What to decide:** which actions require human approval? What
  uncertainty/risk threshold pauses and escalates? How is the **loop bounded** and how
  is **idempotency** guaranteed for anything that mutates state?
- **What to compare:** human-in-the-loop vs human-on-the-loop by action reversibility;
  retries with backoff+jitter; brakes outside the model (step/turn caps, USD budget
  guard, no-progress detection).
- **How to decide:** irreversible actions (sign, file, delete) → approval with audit
  trail; on low confidence, flag for review, never invent. Idempotency key on every
  state-mutating action (a retry must not execute twice).
- **What to document:** the action → risk → approver matrix, and the loop bounds.

## K. Evaluation and observability (LLMOps)
- **What to decide:** what is measured **offline** (golden dataset, metrics, CI gate)
  and **online** (traces, sampled LLM-as-judge, feedback)? What is the **reliability
  metric** (*pass^k*/consistency, not just average accuracy) and its thresholds? How
  does the loop close?
- **What to compare:** offline (DeepEval, Promptfoo, Braintrust) · observability
  (LangSmith, Langfuse, Arize Phoenix, Helicone) · the OpenTelemetry GenAI standard.
  Self-host when there is PII.
- **How to decide:** evals from the start; grade the **outcome** (final state), not the
  path; separate capability evals (climb from low) from regression (~100%).
- **What to document:** the evals/observability approach, the reliability metric, and
  the feedback loop.

## L. Cost, latency, and scaling
- **What to decide:** cost budget per execution and latency budget? What is the **rate
  limit ceiling** (RPM/ITPM/OTPM per model class) and how does it shape queueing and
  concurrency? How is it monitored?
- **What to compare:** model routing · budget guards · caching (also a **throughput**
  lever: cache hits don't count toward ITPM) · **Batch API** (separate pool, −50%) for
  offline loads · parallelization.
- **How to decide:** the context (E) and memory (F) decisions are the cost/latency
  levers; rate limits set the throughput ceiling → size the queue accordingly.
- **What to document:** target budgets, the rate-limit ceiling, and the levers applied.

---

> **Reminder:** candidates are for **comparing**, not for blind adoption. Keep the TRD
> at **map** level; granular detail is deferred to implementation.
