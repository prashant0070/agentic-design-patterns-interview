# Pattern 7: Multi-Agent Collaboration

## What It Is
Structure a system as a **cooperative ensemble of specialized agents**, each with distinct roles, tools, and capabilities, working together toward a common goal. The collective performance exceeds what any single agent could achieve.

## When to Use
- Task requires diverse specialized skills
- Problem can be decomposed into distinct sub-tasks
- You need parallel expertise (researcher + writer + editor)
- Single agent would be overwhelmed by tool count or context size
- System needs modularity, scalability, and fault tolerance

## Rule of Thumb
Use when a task is too complex for a single agent and can be decomposed into distinct sub-tasks requiring specialized skills or tools.

---

## Collaboration Models

### 1. Sequential Handoffs
```
[Agent A] → output → [Agent B] → output → [Agent C] → Final
```
One completes, passes to next. Like an assembly line.

### 2. Parallel Processing
```
         ┌→ [Agent A: Research] ─┐
[Input] ─┼→ [Agent B: Analysis] ─┼→ [Merger Agent] → Output
         └→ [Agent C: Social] ──┘
```
Multiple agents work simultaneously on different parts.

### 3. Hierarchical (Supervisor)
```
[Manager Agent]
  ├→ delegates to [Worker A]
  ├→ delegates to [Worker B]
  └→ synthesizes results
```
Central coordinator assigns tasks and aggregates.

### 4. Debate and Consensus
Agents with varied perspectives discuss, evaluate options, reach consensus.

### 5. Critic-Reviewer
```
[Creator Agent] → draft → [Reviewer Agent] → feedback → [Creator Agent] → refined
```
One creates, another critiques, iteration improves quality.

### 6. Expert Teams
Specialized agents (researcher, writer, editor, tester) collaborate like a human team.

---

## Practical Use Cases

| Domain | Agent Team |
|--------|-----------|
| Software development | Requirements analyst + code generator + tester + docs writer |
| Research | Search agent + summarizer + trend analyst + report writer |
| Marketing | Market researcher + copywriter + designer + scheduler |
| Customer support | Front-line bot + specialist (billing/tech) + escalation |
| Financial analysis | Data fetcher + sentiment analyzer + technical analyst + advisor |

---

## Key Implementation Points

1. **Clear role boundaries** — each agent has a defined scope, don't overlap
2. **Standardized communication** — use shared data formats between agents
3. **Shared ontology** — agents must agree on terminology and data structures
4. **Failure isolation** — one agent's failure shouldn't crash the system
5. **Coordinator pattern** — have a manager agent for task assignment and flow control
6. **Limit tool access per agent** — each agent only gets the tools it needs

---

## Communication Structures

| Structure | Description | Best For |
|-----------|-------------|----------|
| Single Agent | One agent, no communication needed | Simple tasks |
| Network | Peer-to-peer, decentralized | Resilient systems |
| Supervisor | Central hub coordinates subordinates | Clear authority |
| Hierarchical | Multi-level management tree | Large-scale systems |
| Custom | Task-specific interaction patterns | Specialized workflows |

---

## Trade-offs

| Pro | Con |
|-----|-----|
| Enhanced modularity and scalability | Communication overhead |
| Fault tolerance (one agent failing ≠ system failure) | Harder to debug interactions |
| Specialized agents = better quality per task | More complex orchestration |
| Easy to add new capabilities (new agent) | Potential for incoherent outputs |
| Parallel execution possible | Higher total token usage |

---

## Connection to Other Patterns
- **Planning** — planner creates steps, different agents execute them
- **Routing** — router dispatches to the right specialist agent
- **Parallelization** — multiple agents work concurrently
- **Reflection** — critic agent reviews producer agent's work
- **A2A** — standardizes cross-framework agent communication
- **Memory** — shared state between agents requires memory management
- **HITL** — human oversight of multi-agent decisions

---

## Anti-Patterns
- ❌ Too many agents for a simple task (over-engineering)
- ❌ Agents with overlapping responsibilities (confusion, redundancy)
- ❌ No clear coordinator (agents working at cross-purposes)
- ❌ Ignoring communication costs (every inter-agent message = latency + tokens)
- ❌ Single point of failure in the supervisor

---

# Interview Depth Extension

> Appended for AI/ML Engineer, Applied AI, and Agent Platform interview prep.
## Interview Answer (60–90s)

Multi-agent splits work across specialists with clear roles, tools, and contracts via handoffs, supervisor, or peers. Gains modularity/parallelism; costs tokens, latency, debugging complexity. Start with the fewest agents matching real skill boundaries. Skip when one agent with routing/tools is enough.

## Deep Dive

### Topologies
Supervisor (control, SPOF risk), pipeline handoffs, peer mesh, hierarchy.
Contracts: typed messages, single-writer artifacts, escalation timeouts.

### Failure modes
Role overlap, chatty loops, inconsistent shared memory, cost blowups.

### Metrics
Success, messages/task, duplicate work, $/success.

### When NOT to use
Narrow tasks; early prototypes; nearly identical specialists.

## Common Interview Questions

**Q1. Worth it when?**

A: Real skill/tool/context separation that pays coordination tax.

**Q2. Supervisor vs decentralized?**

A: Supervisor for compliance; mesh for resilience; often hybrid.

**Q3. Stop chatter?**

A: Turn limits, termination messages, supervisor cutoffs.

**Q4. Shared memory risks?**

A: Races + cross-agent injection; typed stores + authz.

**Q5. Evaluate?**

A: E2E + ablate to single-agent baseline.

**Q6. A2A vs in-process?**

A: A2A across trust/framework boundaries.

**Q7. Failure isolation?**

A: Bulkheads: quotas, sandboxes, poison-pill detection.

## Anti-Patterns

- Many agents for FAQ
- Overlapping RACI
- Free-text-only protocol
- No global budget

## Production Checklist

- [ ] Role charters + allowlists
- [ ] Typed messages
- [ ] Budgets
- [ ] Cross-agent traces
- [ ] Bulkheads
- [ ] HITL escalation
