# Starter Pack — 21 Patterns in One Page

> Print this. Drill 60s answers. Then go deep in the numbered files.

## Agent definition (10s)
Perceive → Reason → Act → Learn (optional Collaborate). Chatbots answer; **agents change state** via tools/plans under goals.

## Decision tree
```
Linear known steps? → Prompt Chaining
Varying intents? → Routing (+ model/tool tiering)
Independent I/O? → Parallelization
Unknown how? → Planning (+ monitor goals)
Need external truth/action? → Tool Use (+ MCP)
Private/current knowledge? → RAG
Quality > speed? → Reflection / Reasoning (ReAct, ToT)
Long-running / personalization? → Memory (+ Learning carefully)
Specialized skills? → Multi-Agent (+ A2A if cross-process)
Risk / irreversible? → Guardrails + HITL + Exceptions
Ship safely? → Eval/Monitoring + Observability + Resource budgets
Open-ended research? → Exploration (budgeted)
Contention for work? → Prioritization
```

## One-liners (memorize)

| # | Pattern | Say this |
|---|---------|----------|
| 1 | Prompt Chaining | Sequential single-responsibility LLM stages with typed handoffs |
| 2 | Routing | Classify → dispatch to workflow/model/specialist with confidence fallback |
| 3 | Parallelization | Fan-out independent work; bounded concurrency; explicit join policy |
| 4 | Reflection | Generate → critique(rubric) → revise; prefer external verifiers |
| 5 | Tool Use | Structured calls + validate + least privilege + idempotent writes |
| 6 | Planning | Goal → DAG of steps; replan on observations; budgets required |
| 7 | Multi-Agent | Specialists + contracts; start with fewest agents that match real seams |
| 8 | Memory | STM context + LTM stores; write/read policies; tenant isolation |
| 9 | Learning | Adapt via eval-gated prompt/policy/pref data—not unsupervised chaos |
| 10 | MCP | Standard host↔tool/resource/prompt servers; not a safety guarantee |
| 11 | Goals/Monitor | GoalSpec + acceptance tests + stuck detection |
| 12 | Exceptions | Taxonomy: transient/validate/semantic/policy/fatal + compensation |
| 13 | HITL | Risk-based interrupts; decision packets; labels → evals |
| 14 | RAG | Hybrid retrieve → rerank → cite → faithfulness; chunking is destiny |
| 15 | A2A | Typed agent messages, identity, deadlines across runtimes |
| 16 | Resource-Aware | Optimize $/latency/quality; degrade modes; Pareto operating point |
| 17 | Reasoning | CoT / ReAct / ToT / self-consistency—pick by verifiability & SLA |
| 18 | Guardrails | Defense in depth; fail closed on safety; red-team continuously |
| 19 | Eval/Monitor | Trajectories not just finals; CI gates; drift alerts |
| 20 | Prioritization | Score urgency×impact under dependencies; prevent starvation |
| 21 | Exploration | Budgeted novelty search; sandbox; exploit floor |

## Composition cheat (support agent)
Route → retrieve (RAG) → tools → (optional reflect) → guardrails → HITL if refund/PII → memory write → eval sample

## Metrics that impress interviewers
Task success, trajectory quality, tool correctness, faithfulness, p95 latency, $/success, attack success rate, escalation quality

## Day-of checklist
1. 60s answer from any pattern  
2. Whiteboard one composition  
3. Design platform slice (registry, memory, eval, policy)  
4. Name failure modes + when NOT to use  
5. Mention idempotency, traces, cost budgets
