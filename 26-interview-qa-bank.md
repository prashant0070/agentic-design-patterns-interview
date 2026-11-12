# 26 — Interview Q&A Bank (100+)

> Mix of **Junior → Staff** questions for AI/ML Engineer, Applied AI, and Agent Platform loops.
> Count: **149** Q&As. Use timed drills: 90s oral answers without notes.

## How to use
1. Cover answers; speak for 60–90s.
2. Then read the answer; note gaps.
3. Tag misses into Anki / your notes.
4. Pair with coding problems in `32` and system design in `27`.


## Basics

### BAS-1 [Junior]
**Q:** What is an AI agent vs a chatbot?

**A:** A chatbot primarily generates responses. An agent perceives inputs/observations, reasons about goals, acts via tools or environment changes, and optionally learns/remembers across steps. Autonomy + goal orientation + state-changing actions are the interview keywords.

### BAS-2 [Junior]
**Q:** Explain the perceive–reason–act loop.

**A:** Perceive: user messages, tool results, sensors. Reason: plan, route, or decide next action. Act: call tools/APIs or respond. Loop until stop criteria (goal met, budget exhausted, refuse).

### BAS-3 [Mid]
**Q:** When should you NOT build an agent?

**A:** When a deterministic workflow/API orchestration solves it; when latency budgets forbid multi-step LLM calls; when risk of tool misuse is high without HITL ROI; when a single RAG answer suffices.

### BAS-4 [Mid]
**Q:** What is context engineering?

**A:** Curating what enters the context window—instructions, tools, retrieved docs, memory, observations—under token budgets. Often beats clever prompt wording for agent reliability.

### BAS-5 [Senior]
**Q:** How do you define done for an agent task?

**A:** Machine-checkable acceptance when possible (schema valid, tests pass, ticket field set), plus constraints (cost, policy). Vague ‘user happy’ is not a GoalSpec.

### BAS-6 [Staff]
**Q:** How would you introduce agents into a mature platform org?

**A:** Start with shadow mode on low-risk reads; shared tool registry + policy; eval harness before expansion; clear ownership of prompts/models; progressive autonomy with HITL; platform SLOs for cost/latency/safety.

### BAS-7 [Junior]
**Q:** What is tool hallucination?

**A:** Model invents tool names/args/APIs. Mitigate schema validation and allowlists.

### BAS-8 [Mid]
**Q:** Structured output—why mandatory in pipelines?

**A:** Free text breaks downstream parsers; schemas enable validation/repair loops.

### BAS-9 [Senior]
**Q:** Temperature for agents?

**A:** Lower for tool calling/routing; higher only for creative branches; keep deterministic where audited.

### BAS-10 [Mid]
**Q:** What is a trajectory?

**A:** The full sequence of thoughts/tool calls/observations/outputs for a run—unit of debug/eval.

### BAS-11 [Staff]
**Q:** North-star metric for agents?

**A:** Successful task completion under safety/cost constraints—not tokens or demos.

### BAS-12 [Mid]
**Q:** Stop conditions list?

**A:** Goal success, max steps, timeout, policy refuse, user cancel, low confidence escalate.

## Tool Use

### TOO-1 [Junior]
**Q:** Walk through a function call.

**A:** Tools declared with name/description/JSON schema → model returns structured call → runtime validates/authz/executes → observation returned as tool message → model continues or finishes.

### TOO-2 [Junior]
**Q:** Why do tool descriptions matter?

**A:** The model selects tools largely from descriptions. Vague descriptions → wrong tool choice. Include when to use / when not to use.

### TOO-3 [Mid]
**Q:** How do you validate tool arguments?

**A:** Server-side JSON Schema; coerce carefully; reject unknowns; never trust model types; for side effects require idempotency keys and authz checks.

### TOO-4 [Mid]
**Q:** Parallel tool calls—when safe?

**A:** Independent read-only calls yes. Writes need serialization or conflict control. Merge observations deterministically.

### TOO-5 [Senior]
**Q:** How many tools can an agent handle?

**A:** Quality degrades as catalogs grow (dozens+). Mitigate with routing to subsets, hierarchical tools, or MCP discovery with scoped manifests.

### TOO-6 [Senior]
**Q:** How prevent duplicate side effects on retry?

**A:** Idempotency keys, upsert semantics, dedupe tables, at-least-once with exactly-once business effects via transactional outbox.

### TOO-7 [Staff]
**Q:** Design a tool execution layer for multi-tenant SaaS.

**A:** Per-tenant credentials, allowlists by role, sandboxed code tools, timeouts/circuit breakers, DLP on args/results, full audit, rate limits, schema versioning, blast-radius quotas.

### TOO-8 [Junior]
**Q:** What is an observation?

**A:** Tool result fed back to the model as context for next reasoning step.

### TOO-9 [Mid]
**Q:** Timeout strategy?

**A:** Per-tool timeouts + global deadline; cancel and degrade with partial results.

### TOO-10 [Senior]
**Q:** OAuth tools with agents?

**A:** User-delegated tokens, short TTL, refresh out-of-band, never put refresh tokens in prompts.

### TOO-11 [Staff]
**Q:** Policy-as-code for tools?

**A:** OPA/Cedar-like rules on who/what/when; tested in CI; logged decisions.

### TOO-12 [Mid]
**Q:** Agent-as-tool?

**A:** Wrap specialist agent behind tool I/O contract for cleaner composition.

## Multi-Agent

### MUL-1 [Junior]
**Q:** Why multi-agent?

**A:** Specialize skills/tools/context; parallelize; isolate failures. Not for vanity architecture.

### MUL-2 [Mid]
**Q:** Supervisor vs handoff pipeline?

**A:** Supervisor centralizes assignment/merge (clear, SPOF). Pipeline is simple assembly line. Choose based on coordination needs.

### MUL-3 [Mid]
**Q:** How stop infinite agent chatter?

**A:** Turn budgets, explicit termination messages, shared blackboard with locks, supervisor cutoffs, global token budget.

### MUL-4 [Senior]
**Q:** When is multi-agent worse than one agent?

**A:** Overlapping roles, no contracts, tasks fitting one context/toolset—coordination tax exceeds quality gains. Always compare to single-agent baseline.

### MUL-5 [Staff]
**Q:** Cross-team agents with different trust levels?

**A:** A2A with mutual auth, capability scopes, typed envelopes, data minimization, per-agent privileges; never let low-trust agents invoke high-trust tools directly.

### MUL-6 [Junior]
**Q:** Critic-reviewer pattern?

**A:** Producer drafts; critic scores rubric; iterate—reduces self-bias.

### MUL-7 [Senior]
**Q:** Shared ontology?

**A:** Agree on schemas/terms so agents don’t talk past each other.

### MUL-8 [Staff]
**Q:** Market/auction scheduling?

**A:** Agents bid for scarce tools/budget; can improve allocation but adds complexity—use when contention real.

### MUL-9 [Mid]
**Q:** Failure isolation/bulkhead?

**A:** Separate quotas/sandboxes so one noisy agent can’t exhaust shared budget.

## RAG

### RAG-1 [Junior]
**Q:** Explain RAG in 60 seconds.

**A:** Embed/store chunks; retrieve top-k for a query; inject as context; generate grounded answer preferably with citations. Moves model from closed-book to open-book.

### RAG-2 [Mid]
**Q:** Why hybrid search?

**A:** BM25 exact tokens + vectors semantics. Improves proper nouns, IDs, rare terms.

### RAG-3 [Mid]
**Q:** Chunking mistakes?

**A:** Too small loses context; too large dilutes; no overlap at boundaries; ignoring document structure; no metadata.

### RAG-4 [Senior]
**Q:** How measure RAG quality?

**A:** Retrieval: recall@k, nDCG. Generation: faithfulness, answer relevance, citation precision. Offline golden sets + online thumbs + spot checks.

### RAG-5 [Senior]
**Q:** Agentic RAG vs naive RAG?

**A:** Agent decides what to retrieve, rewrites queries, multi-hops, critiques sufficiency—higher cost, better hard questions.

### RAG-6 [Staff]
**Q:** RAG poisoning defenses?

**A:** Ingest ACL, provenance, malware scanning, signed sources, anomaly detection on embeddings, separate untrusted corpora, cite-or-refuse, never let retrieved text escalate privileges.

### RAG-7 [Junior]
**Q:** What is an embedding?

**A:** Vector representation capturing semantic similarity for retrieval.

### RAG-8 [Mid]
**Q:** Lost-in-the-middle?

**A:** Models underweight mid-context; put critical evidence at edges or shorten.

### RAG-9 [Senior]
**Q:** Reranking role?

**A:** Second-stage cross-encoder/LLM filters top-k for precision before generation.

### RAG-10 [Staff]
**Q:** Cross-encoder vs bi-encoder?

**A:** Bi-encoder for fast recall; cross-encoder for precise rerank—classic two-stage.

### RAG-11 [Mid]
**Q:** Citation theater?

**A:** Pretty citations not actually supporting claims—need span-level grounding checks.

## Memory

### MEM-1 [Junior]
**Q:** STM vs LTM?

**A:** STM: context window / session. LTM: external stores retrieved later.

### MEM-2 [Mid]
**Q:** What should be written to long-term memory?

**A:** Salient preferences, corrections, decisions—structured facts after PII scrub. Not raw transcripts blindly.

### MEM-3 [Senior]
**Q:** How avoid memory distraction?

**A:** Rerank, metadata filters, token budgets, separate stores by type, eval distraction tests.

### MEM-4 [Staff]
**Q:** Multi-tenant memory isolation?

**A:** Tenant_id on every record and mandatory query filter; preferably separate collections/indexes; encryption; deletion/TTL APIs; audit.

### MEM-5 [Mid]
**Q:** Hierarchical summarization?

**A:** Compress old turns into summaries while pinning atomic facts.

### MEM-6 [Staff]
**Q:** GDPR deletion with vector DBs?

**A:** Store doc_id mapping; delete by id from vector+metadata; verify tombstones; encrypt backups with TTL.

### MEM-7 [Junior]
**Q:** Why TTL on memories?

**A:** Preferences/facts go stale; reduces privacy and distraction risk.

## Eval

### EVA-1 [Junior]
**Q:** Why eval agents differently than chatbots?

**A:** Must score tool sequences, state, long-horizon goals, safety—not only final text.

### EVA-2 [Mid]
**Q:** LLM-as-judge pitfalls?

**A:** Bias to verbosity/style; position bias; need rubrics + human calibration + holdouts.

### EVA-3 [Senior]
**Q:** Trajectory evaluation?

**A:** Grade whether steps were necessary/correct/safe even if final answer luckily correct.

### EVA-4 [Staff]
**Q:** Build an eval platform?

**A:** Dataset versioning, graders (rules/LLM/human), CI gates, production trace sampling into datasets, slice dashboards, ownership SLAs.

### EVA-5 [Junior]
**Q:** Offline vs online eval?

**A:** Offline: golden sets before ship. Online: live KPIs, sampling, drift.

### EVA-6 [Senior]
**Q:** Prevent eval overfitting?

**A:** Holdout tasks, refresh from prod failures, adversarial sets.

### EVA-7 [Staff]
**Q:** Counterfactual eval?

**A:** Replay traces with new prompt/model offline to estimate lift before canary.

### EVA-8 [Mid]
**Q:** Slice analysis?

**A:** Break metrics by intent, language, tenant, tool—averages hide pain.

## Safety

### SAF-1 [Junior]
**Q:** What is prompt injection?

**A:** Malicious instructions in user or retrieved/tool content that try to override system policy.

### SAF-2 [Mid]
**Q:** Direct vs indirect injection?

**A:** Direct: user message. Indirect: hidden in docs/emails/pages the agent reads.

### SAF-3 [Mid]
**Q:** Tool exfiltration?

**A:** Model tricked into sending secrets via tool args (email, HTTP). Mitigate allowlists, DLP, confirmations.

### SAF-4 [Senior]
**Q:** Defense in depth for agents?

**A:** Ingress filters, instruction hierarchy, untrusted content delimiters, tool policy, egress DLP, sandbox, HITL, monitoring, red team.

### SAF-5 [Staff]
**Q:** Fail open vs fail closed?

**A:** Safety-critical: fail closed. Availability-only low risk: maybe open with logging. Document explicitly per route.

### SAF-6 [Junior]
**Q:** Jailbreak?

**A:** User attempts to bypass safety policies via roleplay/obfuscation.

### SAF-7 [Senior]
**Q:** Instruction hierarchy?

**A:** System/developer policy outranks user/tool text; enforce in runtime not just prose.

### SAF-8 [Staff]
**Q:** Red team cadence?

**A:** Continuous automated probes + quarterly deep manual; track ASR trends; feed failures to suite.

### SAF-9 [Mid]
**Q:** Least privilege tools?

**A:** Each agent/route only gets needed tools; default deny.

## System Design

### SYS-1 [Mid]
**Q:** Core components of an agent platform?

**A:** Orchestrator, model gateway, tool registry, memory/RAG stores, policy/guardrails, eval harness, observability/cost, tenant identity, HITL service.

### SYS-2 [Senior]
**Q:** How design multi-tenant isolation?

**A:** Authn/z at edge; tenant keys everywhere; separate encrypt keys; quotas; per-tenant tool credentials; no shared prompt cache across tenants for sensitive data.

### SYS-3 [Senior]
**Q:** SLA for an agent API?

**A:** Define p95 latency, availability, task success, safety incident budget, cost ceiling; degraded modes when dependencies fail.

### SYS-4 [Staff]
**Q:** How control LLM cost at scale?

**A:** Model tiering, caches, budgets per request, early exit, batch where possible, circuit breakers, product-level quotas, Pareto monitoring $/success.

### SYS-5 [Staff]
**Q:** Orchestrator: workflow engine vs LLM loop?

**A:** Known SOPs → durable workflows. Unknown how → LLM loop/graph with checkpoints. Hybrid: workflow calls LLM nodes.

### SYS-6 [Mid]
**Q:** Model gateway benefits?

**A:** Central auth, routing, caching, budget, logging, failover across providers.

### SYS-7 [Senior]
**Q:** Human approval service?

**A:** Async/sync tasks with decision packets, SLAs, timeout escalation, audit.

### SYS-8 [Mid]
**Q:** Why durable execution?

**A:** Long-running agents survive process death; timers/HITL need persistence.

### SYS-9 [Junior]
**Q:** What is a tool registry?

**A:** Catalog of tools with schemas, owners, auth, SLOs, versions.

## MCP/A2A

### MCP-1 [Mid]
**Q:** What problem does MCP solve?

**A:** Standardizes discovery and invocation of tools/resources/prompts across hosts/servers—reduces bespoke connectors.

### MCP-2 [Mid]
**Q:** MCP vs function calling?

**A:** FC is model API feature. MCP is ecosystem protocol between app and tool servers.

### MCP-3 [Senior]
**Q:** A2A vs MCP?

**A:** A2A agent↔agent collaboration; MCP agent↔tools/context.

### MCP-4 [Staff]
**Q:** Secure MCP deployment?

**A:** Consent UX, scoped OAuth, allowlists, treat server content untrusted, schema versioning, audit, least-privilege backend creds.

### MCP-5 [Junior]
**Q:** MCP resource vs tool?

**A:** Resource=read context; tool=action/side effect.

### MCP-6 [Mid]
**Q:** Capability discovery risks?

**A:** Agents may see dangerous tools; scope manifests per identity.

## Coding Agents

### COD-1 [Mid]
**Q:** What dominates coding-agent quality?

**A:** Context packaging (repo map, brief, failing tests) + tight tool sandbox + test feedback loop.

### COD-2 [Senior]
**Q:** How eval coding agents?

**A:** Repo golden tasks, SWE-bench-like, measure pass rate, iterations, human edit distance, escaped defects.

### COD-3 [Staff]
**Q:** Platform for enterprise coding agents?

**A:** Shared runtime, policy, secret hygiene, eval suites per repo, PR gates, model routing, audit.

### COD-4 [Junior]
**Q:** Why run tests in the loop?

**A:** External verifier grounds reflection; prevents polished wrong code.

### COD-5 [Mid]
**Q:** Drive-by refactors why bad?

**A:** Expand blast radius, hard reviews, hide bugs; constrain agents to task-scoped diffs.

### COD-6 [Senior]
**Q:** Secret scanning in agent PRs?

**A:** Block commits with keys/PII; never allow prod secrets in sandbox env.

## Orchestration

### ORC-1 [Mid]
**Q:** ReAct vs plan-and-execute?

**A:** ReAct interleaves thinking and tools—adaptive. Plan-and-execute drafts steps first—better global structure, weaker if world changes; often hybrid.

### ORC-2 [Senior]
**Q:** Why checkpoints in agent graphs?

**A:** Resume after failure/HITL, replay, audit, exactly-once effects with idempotent nodes.

### ORC-3 [Staff]
**Q:** Saga compensation for agents?

**A:** Each side-effecting step registers undo/neutralize; on later failure run compensations in reverse; LLM should not invent compensations—code owns them.

### ORC-4 [Junior]
**Q:** What is a state machine agent?

**A:** Nodes=steps; edges=transitions; state persisted; cycles allowed for loops.

### ORC-5 [Mid]
**Q:** Human interrupt?

**A:** Pause at node, serialize state, resume with human input as state update.

### ORC-6 [Staff]
**Q:** Exactly-once tool side effects?

**A:** Inbox/outbox + idempotency store; orchestrator at-least-once; effects deduped.

### ORC-7 [Mid]
**Q:** Idempotent node design?

**A:** Node effects safe under retry; key by run_id+node_id.

## Observability

### OBS-1 [Mid]
**Q:** What is an LLM span?

**A:** A traced unit: model call with prompt hash, tokens, latency, model id, parent trajectory id—plus child tool spans.

### OBS-2 [Senior]
**Q:** Debug a bad trajectory?

**A:** Open trace → find wrong route/tool/retrieval → check schemas/observations → reproduce with fixtures → add eval case → patch prompt/policy.

### OBS-3 [Staff]
**Q:** OpenTelemetry for LLM apps?

**A:** Standardize attributes (gen_ai.*), propagate traceparent across tools/agents, export to backend; pair with domain UI like LangSmith-style trajectory views.

### OBS-4 [Junior]
**Q:** Token accounting?

**A:** Track prompt/completion tokens per call, roll up per request/tenant for cost.

### OBS-5 [Staff]
**Q:** Privacy in traces?

**A:** Redact PII/secrets at write; field-level allowlists; separate raw vs derived stores; retention policies.

### OBS-6 [Mid]
**Q:** Latency budget allocation?

**A:** Split p95 across retrieve/model/tools/guardrails; enforce per stage.

## Prioritization

### PRI-1 [Mid]
**Q:** Priority inversion example?

**A:** Low-value chatty task blocks high-urgency refund because FIFO queue.

### PRI-2 [Senior]
**Q:** Preemption policy?

**A:** Higher priority can pause lower if safe pause/resume exists; else finish critical section.

## Resources

### RES-1 [Mid]
**Q:** Early exit?

**A:** Stop when acceptance criteria met; skip reflection on easy routes.

### RES-2 [Senior]
**Q:** Quality cliff?

**A:** Cheaper tier suddenly fails hard tasks; detect via confidence/escalation metrics.

## Reflection

### REF-1 [Mid]
**Q:** Diminishing returns stop?

**A:** If score delta < ε or critic repeats issues, stop and return best.

### REF-2 [Senior]
**Q:** External verifier examples?

**A:** Unit tests, linters, calculators, retrieval entailment checks, policy engines.

### REF-3 [Junior]
**Q:** Producer-critic preference?

**A:** Separate critic reduces self-bias vs self-reflection.

## Planning

### PLA-1 [Mid]
**Q:** Over-planning smell?

**A:** Many steps before any tool call on a simple lookup—cap depth.

### PLA-2 [Senior]
**Q:** Hierarchical planning?

**A:** Coarse milestones + local planners—reduces brittle mega-plans.

### PLA-3 [Junior]
**Q:** Plan as data why?

**A:** Version, validate, execute, diff, audit—prose plans are not operable.

## Routing

### ROU-1 [Junior]
**Q:** Safe default route?

**A:** Generalist + retrieval + escalate; never destructive tools.

### ROU-2 [Senior]
**Q:** Shadow routing?

**A:** Log new router decisions without affecting prod; compare outcomes before cutover.

### ROU-3 [Mid]
**Q:** Model tier routing signals?

**A:** Length, intent, risk score, confidence, customer tier, historical difficulty.

## HITL

### HIT-1 [Mid]
**Q:** Rubber stamping fix?

**A:** Show diffs, risks, rationale; require structured reasons for approve/deny.

### HIT-2 [Senior]
**Q:** Async review queues?

**A:** Non-blocking for medium risk; SLA timers; auto-escalate on timeout.

### HIT-3 [Junior]
**Q:** Decision packet contents?

**A:** Summary, proposed action, risks, diffs, evidence, expire time.

## Exceptions

### EXC-1 [Mid]
**Q:** Semantic error example?

**A:** Search returns 200 but empty/irrelevant; need alternate query/tool.

### EXC-2 [Senior]
**Q:** Circuit breaker half-open?

**A:** After cooldown allow probe traffic; restore if healthy else reopen breaker.

### EXC-3 [Junior]
**Q:** Transient vs permanent?

**A:** Transient: retry. Permanent: fix input/policy or abort—don’t spin.

## Frameworks

### FRA-1 [Mid]
**Q:** LangGraph vs LCEL?

**A:** LCEL linear pipes; LangGraph stateful cyclic graphs with checkpoints.

### FRA-2 [Senior]
**Q:** When raw SDK?

**A:** Simple agents, need full control, avoid abstraction tax; add thin orchestration.

### FRA-3 [Junior]
**Q:** CrewAI in one line?

**A:** Opinionated role-based multi-agent crews for fast prototypes.

## Interfaces

### INT-1 [Mid]
**Q:** Prefer API over GUI agent?

**A:** APIs are stable and cheaper; GUI when no API exists.

### INT-2 [Senior]
**Q:** Action verification?

**A:** After click/type, re-observe DOM/screenshot for expected state transition.

## Exploration

### EXP-1 [Senior]
**Q:** UCB-style exploration?

**A:** Prefer uncertain high-upside actions under budget—structured curiosity.

### EXP-2 [Mid]
**Q:** When exploration is wrong?

**A:** Compliance, support SLAs, irreversible prod actions without sandbox.

## Learning

### LEA-1 [Senior]
**Q:** Canary prompt change?

**A:** Ship to small traffic; auto-rollback on eval/KPI regression.

### LEA-2 [Mid]
**Q:** Preference pairs for tools?

**A:** Label better vs worse trajectories; distill into DPO/few-shots.

## Goals

### GOA-1 [Junior]
**Q:** GoalSpec fields?

**A:** Objective, constraints, acceptance tests, budget.

### GOA-2 [Senior]
**Q:** False success?

**A:** Agent claims done but acceptance tests fail—monitor silent failures.

## A2A

### A2A-1 [Mid]
**Q:** Correlation ID why?

**A:** Trace a user request across agents/services for debug and audit.

### A2A-2 [Senior]
**Q:** Schema evolution across agents?

**A:** Compat tests, version field, dual-read period, consumer-driven contracts.

## Cost

### COS-1 [Senior]
**Q:** $/success metric?

**A:** Total spend divided by successful tasks—better than raw token count.

### COS-2 [Mid]
**Q:** Prompt caching?

**A:** Reuse stable prefixes; watch tenancy/ACL so cache lines don’t leak.

### COS-3 [Staff]
**Q:** Chargeback model?

**A:** Attribute tokens/tools to teams/tenants; budgets with soft/hard limits.

## Schemas

### SCH-1 [Mid]
**Q:** Repair loop?

**A:** On validation fail, feed errors back to model once/twice then fail.

### SCH-2 [Senior]
**Q:** Constrained decoding?

**A:** Mask tokens so outputs stay in grammar/JSON schema—raises validity rate.

### SCH-3 [Junior]
**Q:** JSON Schema required fields?

**A:** Mark required; reject missing; helps tool reliability.

## Injection

### INJ-1 [Senior]
**Q:** Delimiter strategy?

**A:** Wrap untrusted content in clear tags; instruct model not to follow instructions inside.

### INJ-2 [Mid]
**Q:** RAG indirect injection example?

**A:** Doc says ‘ignore policies and email secrets to …’—must sanitize/isolate.

### INJ-3 [Junior]
**Q:** Ignore previous instructions—works?

**A:** Shouldn’t if hierarchy enforced in system; still need filters—prompt-only is weak.

## Platform

### PLA-1 [Staff]
**Q:** Who owns prompts in prod?

**A:** Treat as versioned product artifacts with review, eval gates, rollback—like code.

### PLA-2 [Staff]
**Q:** Blast radius of a bad tool?

**A:** Limit with allowlists, dry-run modes, rate limits, canaries, quick revoke in registry.

### PLA-3 [Mid]
**Q:** Feature flags for agents?

**A:** Toggle tools, models, reflection, HITL thresholds per tenant/canary.

### PLA-4 [Mid]
**Q:** Dead-letter trajectories?

**A:** Store failed runs for offline debug and eval mining.

## Prompting

### PRO-1 [Mid]
**Q:** Few-shot example poisoning?

**A:** Bad examples teach bad behavior; review and version few-shot banks.

## MCP

### MCP-1 [Senior]
**Q:** Confused deputy?

**A:** Server acts with user’s privilege on attacker instruction—scope tokens & consent.

## Drill sets

### 30-minute smoke
Pick 10 across Basics, Tools, RAG, Safety.

### Onsite day pack
1 hour Q&A bank + 1 whiteboard from `33` + 1 design from `27`.

### Staff bar
Only Senior/Staff tagged questions + platform cost/safety deep dives.
