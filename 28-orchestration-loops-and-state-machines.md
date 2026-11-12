# 28 — Orchestration Loops & State Machines

## Interview Answer (60–90s)

Agent orchestration is the runtime that decides the next step, executes it, and persists state. Three common styles: **ReAct loops** (think→act→observe), **plan-and-execute**, and **explicit state machines/graphs** (LangGraph-style) with checkpoints for retries and human interrupts. Production systems need idempotent nodes, bounded retries, deadlines, and saga-like compensation for partial side effects. I pick graphs when I need durability/HITL; simple ReAct for short toolish tasks; workflows when the path is known.

---

## ReAct loop

```text
while not done and steps < max:
  thought = reason(state)
  action = select_tool_or_answer(thought)
  if action is answer: return
  obs = execute(action)  # policy + timeout
  state.append(action, obs)
```

**Pros:** Adaptive. **Cons:** Myopic; can loop; weaker global structure.

### Failure modes
Repeated identical tool calls; thrashing; ignoring observations; exceeding budgets.

### Mitigations
Detect duplicate actions; max steps; force finalize; critic on loops.

---

## Plan-and-execute

```text
plan = planner(goal)
for step in plan:
  result = execute(step)
  if invalid(result): plan = replan(goal, state)
```

**Pros:** Better structure/explainability. **Cons:** Stale plans; planning cost.

**Hybrid:** Coarse plan + ReAct inside each step.

---

## LangGraph-style state machines

```text
          ┌─ retrieve ─┐
 start → route → tools ─┼→ generate → end
          │      ↑ crit │
          └──────┴─reflect┘
```

- **State**: typed dict (messages, data, flags)  
- **Nodes**: pure-ish functions with explicit writes  
- **Edges**: conditional routing  
- **Cycles**: reflection/tool loops  
- **Checkpoints**: after each node for resume/replay  

### Human interrupts
```text
node_propose_refund → interrupt(packet) → wait
resume(decision) → node_execute_or_abort
```

Serialize state to durable store; don’t hold HTTP request open forever.

---

## Checkpoints & replay

| Feature | Why |
|---------|-----|
| Resume after crash | Durability |
| HITL | Pause/resume |
| Time-travel debug | Replay with same inputs |
| Exactly-once effects | Combine with idempotency keys |

Store: `run_id`, `node_id`, `state_hash`, `timestamp`, redacted payloads.

---

## Retries & idempotency

```python
key = f"{run_id}:{node_id}:{input_hash}"
if seen(key): return cached_result
result = do_side_effect(key=key)
remember(key, result)
```

- Retry **transient** errors with jitter  
- Do **not** blind-retry non-idempotent writes  
- Distinguish orchestrator retry vs tool-level retry  

---

## Saga-like compensation

For multi-step writes (create draft → charge → ship):

```text
+A create  +B charge  +C ship
on fail after B: compensate B', then A'
```

Compensations are **code-defined**, not LLM-invented. LLM may choose *whether* to trigger abort policy.

---

## Comparison table

| Style | Best for | Avoid when |
|-------|----------|------------|
| ReAct | Short tool use | Long HITL, complex deps |
| Plan-execute | Multi-step research | Highly stochastic world |
| Graph/FSM | Prod agents, HITL | Tiny scripts |
| Fixed workflow | Known SOP | Unknown how |

---

## Metrics
Steps/run, replan rate, duplicate-action rate, checkpoint restore success, compensation success, p95 time-in-interrupt.

## Common interview Qs

**Q: How do you prevent infinite loops?**  
A: Max steps, wall clock, duplicate detectors, forced final answer, supervisor cutoffs.

**Q: Workflow engine or agent graph?**  
A: Known deterministic SOP → workflow calling LLM nodes. Exploratory → agent graph. Hybrid common.

**Q: How ensure refund isn’t double-applied?**  
A: Idempotency key on payment API; orchestrator at-least-once; effect layer dedupes.

## Anti-patterns
- Holding long HITL in a single web worker memory  
- LLM inventing compensations  
- Unbounded ReAct without budgets  
- Mutable global state across parallel branches  

## Production checklist
- [ ] Typed state schema
- [ ] Checkpoints durable
- [ ] Deadlines + max steps
- [ ] Idempotency store
- [ ] Compensation map for write flows
- [ ] Interrupt UX + timeouts
- [ ] Trace node spans
