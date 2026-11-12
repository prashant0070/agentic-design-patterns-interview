# Agentic Design Patterns — Complete Reference

> Source: "Agentic Design Patterns: A Hands-On Guide to Building Intelligent Systems" by Antonio Gulli (2026)
> This is a condensed knowledge-transfer reference covering all 21 patterns + appendices.

---

## What Makes a System "Agentic"?

An AI system becomes an **agent** when it can:
1. **Perceive** its environment (inputs, tool outputs, observations)
2. **Reason** about what to do next (planning, reflection, prioritization)
3. **Act** on external systems (tool use, function calling, API interaction)
4. **Learn** from outcomes (memory, adaptation, self-correction)
5. **Collaborate** with other agents or humans (multi-agent, HITL, A2A)

A simple chatbot is NOT an agent. An agent has autonomy, goal-orientation, and the ability to take actions that change state.

---

## The 21 Patterns at a Glance

### Cluster 1: Execution & Task Decomposition
| # | Pattern | One-Liner |
|---|---------|-----------|
| 1 | Prompt Chaining | Break complex tasks into sequential sub-prompts |
| 2 | Routing | Conditionally direct flow to the right handler |
| 3 | Parallelization | Run independent sub-tasks concurrently |
| 6 | Planning | Decompose a goal into an actionable multi-step plan |

### Cluster 2: Cognition & Quality
| # | Pattern | One-Liner |
|---|---------|-----------|
| 4 | Reflection | Self-critique and iterative refinement of outputs |
| 17 | Reasoning Techniques | CoT, ToT, ReAct, self-correction for deeper thinking |
| 20 | Prioritization | Rank tasks by urgency, importance, dependencies |

### Cluster 3: World Interaction
| # | Pattern | One-Liner |
|---|---------|-----------|
| 5 | Tool Use | Call external APIs, databases, code interpreters |
| 10 | MCP | Standardized protocol for LLM ↔ external system communication |
| 14 | RAG | Retrieve external knowledge to ground responses |
| 15 | A2A | Inter-agent communication across different frameworks |

### Cluster 4: Reliability & Governance
| # | Pattern | One-Liner |
|---|---------|-----------|
| 12 | Exception Handling | Detect, handle, and recover from operational failures |
| 13 | Human-in-the-Loop | Strategic human oversight at critical decision points |
| 18 | Guardrails/Safety | Input validation, output filtering, behavioral constraints |
| 19 | Evaluation & Monitoring | Continuous measurement of agent performance |

### Cluster 5: Adaptation & Growth
| # | Pattern | One-Liner |
|---|---------|-----------|
| 7 | Multi-Agent | Multiple specialized agents collaborating on a shared goal |
| 8 | Memory Management | Short-term context + long-term persistent knowledge |
| 9 | Learning & Adaptation | Agents that improve autonomously over time |
| 11 | Goal Setting & Monitoring | Define objectives + track progress toward them |
| 21 | Exploration & Discovery | Proactively seek novel information and possibilities |
| 16 | Resource-Aware Optimization | Balance cost, latency, and quality dynamically |

---

## Decision Rules (When to Use What)

```
IF task is linear and deterministic → Prompt Chaining
IF input type varies → add Routing
IF subtasks are independent → Parallelize
IF objective is multi-step and uncertain → Planning
IF correctness must be high → add Reflection + Critique
IF data is external/private/current → Tool Use + RAG/MCP
IF systems must cooperate → A2A with explicit contracts
IF risk is non-trivial → Guardrails + HITL + Audits
IF runtime budget is constrained → Resource-Aware Router + Fallbacks
IF long-term quality matters → Memory + Learning + Continuous Eval
```

---

## Key Non-Obvious Insights

1. **MCP is a contract, not a quality guarantee** — wrapping bad APIs in MCP doesn't make them agent-friendly
2. **Reflection adds cost** — use selectively for high-value outputs, not every path
3. **HITL doesn't scale linearly** — keep humans for ambiguous/high-risk; automate volume
4. **Evaluate trajectories, not just final answers** — bad reasoning with lucky output is still risky
5. **RAG depends on chunking quality** — weak retrieval creates confident-but-wrong synthesis
6. **Multi-agent needs strict boundaries** — without protocols, systems become noisy and expensive
7. **Context engineering > prompt engineering** — what the agent knows matters more than how you ask
8. **Structured output is not optional** — JSON/Pydantic validation is mandatory for reliable pipelines

---

## Pattern Composition Example

A research assistant combining multiple patterns:
```
User Query
  → [Planning] Decompose into research steps
  → [Parallelization] Search multiple sources concurrently
  → [Tool Use] Call search APIs, databases
  → [RAG] Retrieve and inject relevant documents
  → [Multi-Agent] Writer agent synthesizes findings
  → [Reflection] Critic agent reviews draft
  → [Memory] Store research state across steps
  → [Goal Monitoring] Track completion against objectives
  → Final Output
```

---

## File Index

| File | Topic |
|------|-------|
| `01-prompt-chaining.md` | Sequential task decomposition |
| `02-routing.md` | Conditional flow control |
| `03-parallelization.md` | Concurrent execution |
| `04-reflection.md` | Self-critique and refinement |
| `05-tool-use.md` | External function calling |
| `06-planning.md` | Goal decomposition into steps |
| `07-multi-agent.md` | Collaborative agent systems |
| `08-memory-management.md` | Short-term + long-term memory |
| `09-learning-adaptation.md` | Autonomous improvement |
| `10-mcp.md` | Model Context Protocol |
| `11-goal-setting-monitoring.md` | Objectives + progress tracking |
| `12-exception-handling.md` | Error detection and recovery |
| `13-human-in-the-loop.md` | Human oversight integration |
| `14-rag.md` | Knowledge retrieval (RAG) |
| `15-inter-agent-communication.md` | A2A protocol |
| `16-resource-aware-optimization.md` | Cost/latency/quality balancing |
| `17-reasoning-techniques.md` | CoT, ToT, ReAct, self-correction |
| `18-guardrails-safety.md` | Safety patterns |
| `19-evaluation-monitoring.md` | Performance measurement |
| `20-prioritization.md` | Task ranking and scheduling |
| `21-exploration-discovery.md` | Novel information seeking |
| `22-prompting-techniques.md` | Advanced prompting (Appendix A) |
| `23-agent-interfaces.md` | GUI/environment interaction (Appendix B) |
| `24-frameworks-comparison.md` | LangChain vs ADK vs CrewAI (Appendix C) |
| `25-coding-agents.md` | CLI agents and vibe coding (Appendix G) |

---

# Interview Depth Extension

## New Modules (Interview Pack)

| File | Focus |
|------|-------|
| [`README.md`](README.md) | Curriculum hub, learning paths, decision tree |
| [`26-interview-qa-bank.md`](26-interview-qa-bank.md) | 100+ Q&As by topic (junior→staff) |
| [`27-system-design-agent-platforms.md`](27-system-design-agent-platforms.md) | Design an agent platform + 3 E2E designs |
| [`28-orchestration-loops-and-state-machines.md`](28-orchestration-loops-and-state-machines.md) | ReAct, plan-execute, graphs, checkpoints, sagas |
| [`29-observability-tracing-and-cost.md`](29-observability-tracing-and-cost.md) | Traces, tokens, latency budgets, debugging trajectories |
| [`30-prompt-injection-and-adversarial-robustness.md`](30-prompt-injection-and-adversarial-robustness.md) | Injection, exfil, RAG poisoning, red team |
| [`31-structured-outputs-and-tool-schemas.md`](31-structured-outputs-and-tool-schemas.md) | JSON schema, constrained decoding, tool choice |
| [`32-coding-round-problems.md`](32-coding-round-problems.md) | 10 implementation/design problems + sketches |
| [`33-pattern-composition-cheatsheet.md`](33-pattern-composition-cheatsheet.md) | Combine patterns + whiteboard scripts |
| [`docs/STARTER_PACK.md`](docs/STARTER_PACK.md) | 1-page cheat sheet of core patterns |

## Interview Readiness Checklist

### Fundamentals (must be fluent)
- [ ] Define agent vs chatbot; perceive–reason–act–learn loop
- [ ] Explain ReAct vs plan-and-execute vs fixed chains
- [ ] Tool calling lifecycle + validation + least privilege
- [ ] RAG pipeline pitfalls (chunking, hybrid, faithfulness)
- [ ] When multi-agent helps vs hurts

### Production bar (Applied AI / Platform)
- [ ] Sketch orchestration with state, retries, idempotency
- [ ] Guardrails: injection, tool exfil, HITL gates
- [ ] Eval harness: offline + trajectory + online drift
- [ ] Observability: traces/spans for LLM+tools, cost accounting
- [ ] Multi-tenant memory isolation and SLAs

### Staff signals
- [ ] Cost/quality/latency Pareto and degraded modes
- [ ] Platform building blocks: registry, memory, eval, policy
- [ ] Failure taxonomies and saga-style compensation
- [ ] Clear “when NOT to use” for every pattern you pitch

### Suggested oral drill
1. 60–90s answer from any pattern file
2. Whiteboard one composition from `33-pattern-composition-cheatsheet.md`
3. System-design one of the three apps in `27-system-design-agent-platforms.md`
4. Live-code a mini ReAct loop from `32-coding-round-problems.md`
5. Adversarial questions from `30-prompt-injection-and-adversarial-robustness.md`
