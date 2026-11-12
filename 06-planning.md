# Pattern 6: Planning

## What It Is
An agent **formulates a sequence of actions** to move from an initial state toward a goal state. The agent autonomously creates a multi-step plan, potentially adapting it as new information emerges.

## When to Use
- Task is too complex for a single action
- Multi-step processes with dependencies
- The "how" needs to be discovered (not pre-determined)
- Automating workflows: research reports, onboarding, competitive analysis

## When NOT to Use
- Solution is already well-understood and repeatable → use a fixed workflow instead
- Simple single-step tasks
- When predictability matters more than flexibility

## Key Insight
> "The decision to use a planning agent versus a simple task-execution agent hinges on a single question: does the 'how' need to be discovered, or is it already known?"

---

## How It Works

```
[High-Level Goal]
  → Agent analyzes: initial state, goal state, constraints
  → Generates plan: Step 1 → Step 2 → Step 3 → ...
  → Executes steps (may use tools, sub-agents)
  → Monitors progress
  → Adapts plan if obstacles arise
  → Achieves goal
```

### Key Properties of a Good Plan
- **Decomposed** — broken into discrete, executable steps
- **Ordered** — respects dependencies between steps
- **Adaptive** — can be revised when new information arrives
- **Bounded** — has clear completion criteria

---

## Practical Use Cases

| Domain | Planning Application |
|--------|---------------------|
| Business automation | Onboard employee: create accounts → assign training → coordinate departments |
| Research | Gather sources → summarize → identify trends → synthesize report |
| Customer support | Diagnose issue → check systems → apply fix → verify → close ticket |
| Robotics | Navigate from A to B while avoiding obstacles |
| Content creation | Outline → draft sections → review → finalize |

---

## Key Implementation Points

1. **Plan structure** — each step should have: action, expected output, success criteria
2. **Dependency tracking** — know which steps depend on previous steps
3. **Replanning triggers** — define when to replan (new info, step failure, constraint change)
4. **LLMs are natural planners** — they can generate plausible plans from task descriptions
5. **Separate planning from execution** — plan first, then execute (don't interleave unless adaptive)

---

## Planning vs. Fixed Workflows

| Aspect | Planning | Fixed Workflow |
|--------|----------|---------------|
| Flexibility | High — adapts to context | None — same steps always |
| Predictability | Lower | High |
| Cost | Higher (LLM generates plan) | Lower (pre-defined) |
| Use when | Novel/varied tasks | Repeatable processes |

---

## Trade-offs

| Pro | Con |
|-----|-----|
| Handles novel, complex tasks | Plan quality depends on LLM capability |
| Adaptive to changing conditions | Can generate infeasible plans |
| Reduces need for hardcoded workflows | Higher latency (plan + execute) |
| Scales to arbitrary complexity | Over-planning on simple tasks wastes resources |

---

## Connection to Other Patterns
- **Prompt Chaining** — planning generates the chain dynamically
- **Tool Use** — plan steps trigger tool calls
- **Reflection** — evaluate plan feasibility before/during execution
- **Goal Setting** — goal provides the target state for planning
- **Multi-Agent** — different agents may execute different plan steps
- **Prioritization** — order plan steps by urgency/importance

---

## Implementation Skeleton

```javascript
async function planAndExecute(goal, constraints) {
  // Step 1: Generate plan
  const plan = await llm.call({
    role: "Strategic Planner",
    prompt: `Create a step-by-step plan to achieve: "${goal}"
    Constraints: ${JSON.stringify(constraints)}
    Output as JSON array of steps with: action, expectedOutput, dependencies`
  });

  // Step 2: Execute plan steps
  for (const step of plan.steps) {
    const result = await executeStep(step);
    
    // Step 3: Check if replanning needed
    if (result.failed || result.newInfo) {
      const revisedPlan = await replan(goal, plan, result);
      plan.steps = revisedPlan.steps;
    }
  }

  return collectResults(plan);
}
```

---

# Interview Depth Extension

> Appended for AI/ML Engineer, Applied AI, and Agent Platform interview prep.
## Interview Answer (60–90s)

Planning decomposes a goal into an ordered, dependency-aware action sequence that can be revised. Use when the ‘how’ is unknown; use fixed workflows when known. Good plans are structured data with budgets and acceptance checks. Watch over-planning and plan thrashing.

## Deep Dive

### Architecture
Goal → Plan DAG → execute → observe → continue|replan|abort|escalate.
Hierarchical: milestones + local planners.

### Failure modes
Hallucinated steps, impossible tools, infinite replan, eloquent-but-inexecutable plans.

### Metrics
Steps-to-success, replan count, budget overrun, human override rate.

### When NOT to use
Known SOPs; single-tool lookups; ultra-low latency.

## Common Interview Questions

**Q1. Planning vs chaining?**

A: Chaining predetermined; planning generates/adapts at runtime.

**Q2. When replan?**

A: Tool failure, constraint break, new critical info, stall.

**Q3. Represent plans?**

A: JSON DAG with preconditions/outcomes—not prose.

**Q4. Plan-execute vs ReAct?**

A: Plan drafts ahead; ReAct interleaves. Hybrid common.

**Q5. Prevent over-planning?**

A: Max depth/steps + execute-when-clear.

**Q6. Multi-agent planning?**

A: Manager plans/assigns; workers execute.

**Q7. Success criteria?**

A: Machine-checkable where possible.

## Anti-Patterns

- Prose-only plans
- Plan every trivial query
- Never update after contradictions
- No budget

## Production Checklist

- [ ] Structured plan schema
- [ ] Budgets
- [ ] Replan policy
- [ ] Per-step checks
- [ ] Plan version logging
- [ ] HITL for high-impact commits
