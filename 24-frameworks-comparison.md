# Appendix C: Agentic Frameworks Comparison

## Quick Decision Guide

```
Simple linear chain → LangChain
Complex loops/cycles/state → LangGraph  
Multi-agent team orchestration → Google ADK or CrewAI
Data-intensive retrieval → LlamaIndex
Production-grade with lifecycle → Google ADK
```

---

## Framework Comparison Table

| Feature | LangChain | LangGraph | Google ADK | CrewAI |
|---------|-----------|-----------|------------|--------|
| Core Abstraction | Chain (LCEL) | Graph of Nodes | Agent Team | Crew of Agents |
| Workflow Type | Linear (DAG) | Cyclical (loops) | Multi-agent orchestration | Role-based collaboration |
| State Management | Generally stateless | Explicit, persistent state | Implicit (Session/State) | Managed by framework |
| Primary Use | Simple sequences | Complex stateful agents | Production multi-agent | Team simulation |
| Learning Curve | Low | Medium | Medium | Low |
| Control Level | High (explicit) | Very High (every node) | Medium (opinionated) | Low (declarative) |

---

## LangChain

**What:** Framework for LLM-powered applications using pipe-able components.

**Core:** LangChain Expression Language (LCEL) — pipe components together:
```python
chain = prompt | model | output_parser
```

**Use for:**
- Simple RAG: retrieve → prompt → answer
- Summarization: input → summarize → output
- Data extraction: input → extract JSON → output

**Limitation:** No loops. Linear only (DAG).

---

## LangGraph

**What:** Built on LangChain for advanced agentic systems with **state and cycles**.

**Core:** Graph with nodes (functions) and edges (conditional logic). Can loop.

```python
# Define state, nodes, edges
builder = StateGraph(State)
builder.add_node("research", research_fn)
builder.add_node("evaluate", evaluate_fn)
builder.add_edge("research", "evaluate")
builder.add_conditional_edges("evaluate", should_continue)  # Can loop back!
```

**Use for:**
- Multi-agent systems with supervisor routing
- Plan-and-execute agents (plan → execute → replan)
- Human-in-the-loop (graph waits for human input)
- Any workflow requiring retry/loop behavior

---

## Google ADK (Agent Development Kit)

**What:** High-level, production-oriented framework for multi-agent systems.

**Core:** Pre-built agent types (SequentialAgent, ParallelAgent, LlmAgent) with automatic orchestration.

```python
from google.adk.agents import LlmAgent
agent = LlmAgent(
    model="gemini-2.0-flash",
    name="search_agent",
    instruction="Answer using google search",
    tools=[google_search],
)
```

**Use for:**
- Production multi-agent deployment
- Google Cloud integration
- Built-in evaluation capabilities
- MCP server/client support

**Key difference from LangGraph:** ADK abstracts away graph construction; provides pre-built patterns instead of requiring manual wiring.

---

## CrewAI

**What:** Orchestration framework with role-based collaboration (mimics human teams).

**Core:** Agents with personas (role, goal, backstory) + Tasks + Crew:

```python
crew = Crew(
    agents=[researcher, writer, editor],
    tasks=[research_task, write_task, edit_task],
    process=Process.sequential,
)
result = crew.kickoff()
```

**Use for:**
- Simulating team workflows
- Content creation pipelines
- When role clarity matters
- Quick multi-agent prototyping

**Processes:** Sequential (one after another) or Hierarchical (manager delegates)

---

## Other Frameworks

| Framework | Specialty |
|-----------|-----------|
| **LlamaIndex** | Data indexing + RAG pipelines. Best for knowledge-intensive apps. |
| **Haystack** | Scalable production search/QA systems. Enterprise-grade. |
| **MetaGPT** | SOP-driven multi-agent (simulates dev company). Code generation. |
| **SuperAGI** | Agent lifecycle management. GUI, monitoring, reliability. |
| **Semantic Kernel** (MS) | LLM + traditional code via plugins. Enterprise .NET/Python. |
| **AutoGen** (MS) | Conversation-driven multi-agent collaboration. |
| **Strands Agents** (AWS) | Lightweight SDK, model-agnostic, MCP native. |

---

## When to Choose What

| Scenario | Best Choice |
|----------|-------------|
| Quick prototype, linear flow | LangChain |
| Complex reasoning with loops | LangGraph |
| Production multi-agent system | Google ADK |
| Team simulation / role-based | CrewAI |
| Heavy data retrieval / RAG focus | LlamaIndex |
| Enterprise search at scale | Haystack |
| Code generation pipeline | MetaGPT |
| Model-agnostic, lightweight | Strands Agents |

---

## Key Insight

> "The true craft lies in selecting the precise level of abstraction your project demands — not always using the most powerful framework, but the one that fits your complexity."

- Don't use LangGraph for a simple chain
- Don't use LangChain for a cyclic agent
- Don't build from scratch when ADK/CrewAI provides the pattern

---

# Interview Depth Extension

> Appended for AI/ML Engineer, Applied AI, and Agent Platform interview prep.
## Interview Answer (60–90s)

Framework choice depends on control vs speed: LCEL for linear pipes, LangGraph for stateful cyclic agents, CrewAI for role crews, LlamaIndex for data/RAG-heavy, ADK-like stacks for production multi-agent ops. Interviews care that you can justify trade-offs—state, debugging, eval hooks, vendor lock-in—not brand loyalty.

## Deep Dive

### Decision cues
Linear DAG→LangChain; loops/checkpoints/HITL→LangGraph; RAG apps→LlamaIndex; opinionated teams→CrewAI; enterprise lifecycle→ADK-like.

### Failure modes
Framework sprawl, hiding bad design behind abstractions, unobservable graphs.

### Metrics
Time-to-prod, debuggability, perf overhead, hiring familiarity.

### When NOT to use
Sometimes raw SDK + thin orchestrator is clearer for simple agents.

## Common Interview Questions

**Q1. LangChain vs LangGraph?**

A: Chains vs explicit stateful graphs with cycles.

**Q2. Why graphs?**

A: Durable state, interrupts, replay, clear control flow.

**Q3. CrewAI fit?**

A: Quick role-play multi-agent; less fine control.

**Q4. LlamaIndex?**

A: Strong data connectors/retrieval abstractions.

**Q5. Lock-in?**

A: Keep tools/prompts/evals portable; thin framework coupling.

**Q6. Build vs buy?**

A: Buy speed; build when control/compliance demands.

**Q7. What interviewers want?**

A: Trade-offs + when you’d drop the framework.

## Anti-Patterns

- Cargo-cult framework
- Business logic only in opaque helpers
- No eval hooks

## Production Checklist

- [ ] Justify framework in ADR
- [ ] Observability first-class
- [ ] Portable prompts/tools
- [ ] Exit strategy
- [ ] Spike before standardize
