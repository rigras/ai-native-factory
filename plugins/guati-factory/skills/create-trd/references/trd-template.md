# TRD Template — architecture map of a system

> The TRD is a **map**: each section captures the **approach** and the **why**, not the
> implementation. Granular detail — exact prompts, schemas, typed contracts, DDL,
> ontologies — is **later work** (tickets / implementation) and does NOT go here. The
> questions to answer and the candidates to compare per section are in
> `aspect-guide.md`. Delete this note in the final version.

## 0. Metadata
Product · author/technical owner · date/version · status · links (PRD, business rules,
component/agent diagram, repo).

## 1. Technical summary, scope, and success criterion
Architectural approach in 3-5 sentences · target autonomy level · what defines
measurable "success" — including the **target reliability** (consistency à la *pass^k*,
not just average accuracy: that number decides how much autonomy vs HITL you can
afford) · what is **out of scope**.

## 2. System architecture and orchestration pattern
End-to-end diagram and flow · **deterministic workflow vs agent** (and why) · single vs
multi-agent · pattern(s) used · who owns control flow (code vs LLM).

## 3. Component/agent map
Which agents/components exist and the **responsibility** of each · what each consumes
and produces at a high level · which model **class** (fast vs reasoning) · when it
escalates to another agent or a human. *(Prompt and typed contract → implementation.)*

## 4. Actions / capabilities
What **actions** the system needs (e.g. extract data from a document, query an external
source, generate a deliverable, notify a human) · what is built vs integrated ·
**map-level tool strategy**: consolidation (few clear tools vs "tool soup") and MCP
exposure (load all defs vs code-execution on demand — a context/cost lever, not a
detail). *(Each tool's signature/contract → implementation.)*

## 5. Knowledge and context engineering (approach)
Per agent: **what knowledge** it needs (business rules, reference data, deliverable
formats) and the **injection approach** (in-context / RAG / structured lookup /
just-in-time) · governance (who edits it). *(Schema/ontology → implementation.)*

## 6. Memory and continuous learning (approach)
What is remembered and what for · **conversation memory** vs **product learning** ·
summarization/retrieval approach. *(Storage schema → implementation.)*

## 7. Data, state, and multi-tenancy (approach)
**Approach** for persisting case/session state · tenant isolation strategy · PII
handling. *(Data model and DDL → implementation.)*

## 8. Application and infrastructure
Backend/API · task queue / durable orchestration · deployment · scaling · base
observability. Include the **benchmark table** of evaluated candidates, with the
verification date.

## 9. Guardrails, security, and traceability (approach)
Layered defense · trust boundary for external text · prompt injection / OWASP LLM ·
**lethal trifecta** (private data + untrusted content + exfiltration → say **which leg
is cut**) · **sandboxing** (files + network) and **secrets out of context** (the model
never sees credentials) · what is logged for audit.

## 10. Failure handling, uncertainty, and HITL
Which actions require human approval · the risk threshold that pauses and escalates ·
retries, fallback, graceful degradation · **idempotency** on state-mutating actions ·
**bounding the loop** outside the model (step/turn caps + USD budget guard +
no-progress detection).

## 11. Evaluation and observability (LLMOps)
**Offline** evals (golden dataset, metrics, CI gate) + **online** (traces, feedback) ·
**reliability metric** (*pass^k*/consistency, not just average accuracy) with a
threshold · capability (hill-climbing) vs regression (~100%) · the production →
correction → dataset loop · tooling approach.

## 12. Cost, latency, and scaling
Cost/latency budget **per execution** · levers (model routing, budget guards,
caching) · **rate limits as a design constraint** (RPM/ITPM/OTPM per model class →
throughput ceiling that shapes queueing and concurrency) · **caching is also
throughput** (cache hits don't count toward ITPM) · **Batch API** (separate pool, −50%)
for offline loads.

## 13. Architecture decisions (ADRs)
Per key decision: context → options considered → decision → consequences (including the
negatives). Monotonic numbering; one ADR = one decision; "alternatives considered" is
never empty. Decisions are not silently reopened — a new ADR supersedes the old one.

## 14. Phased implementation plan
Maps to the PRD's phases: what gets built, dependencies, exit criterion per phase.

## 15. Technical risks and spikes/PoCs
The highest-risk items, each with its validation plan.

## 16. Open questions
Table: question · owner · blocking (yes/no).
