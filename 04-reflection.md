# Pattern 4: Reflection (Self-Correction)

## What It Is
An agent **evaluates its own output**, identifies flaws, and **iteratively refines** until quality criteria are met. This creates a feedback loop: generate → critique → improve → repeat.

## When to Use
- Output quality, accuracy, and detail matter more than speed/cost
- Tasks like polished content, code generation, detailed plans
- When you need high objectivity (use separate critic agent)
- Complex outputs that benefit from multiple revision passes

## Rule of Thumb
Use Reflection when the quality of the final output is more important than speed and cost.

---

## How It Works

### Self-Reflection (Single Agent)
```
[Generate Output] → [Evaluate Against Criteria] → [Refine] → [Repeat?]
```

### Producer-Critic Model (Two Agents) ⭐ Preferred
```
[Producer Agent] → generates output
       ↓
[Critic Agent] → evaluates against criteria, provides structured feedback
       ↓
[Producer Agent] → refines based on feedback
       ↓
[Stopping Condition?] → Yes: return | No: loop back to Critic
```

The two-agent model is preferred because it prevents "cognitive bias" of reviewing your own work.

---

## The Reflection Process

1. **Execution** — Agent performs the task, generates initial output
2. **Evaluation/Critique** — Check for: factual accuracy, coherence, style, completeness, adherence to instructions
3. **Reflection/Refinement** — Use critique to generate improved version
4. **Iteration** — Repeat until satisfactory or max iterations reached

---

## Practical Use Cases

| Domain | Reflection Applied |
|--------|-------------------|
| Content writing | Generate draft → critique flow/tone/clarity → rewrite |
| Code generation | Write code → run tests/static analysis → fix errors |
| Problem solving | Propose step → evaluate if it leads to solution → backtrack if not |
| Summarization | Generate summary → compare against source → refine accuracy |
| Planning | Create plan → simulate execution → revise for feasibility |
| Conversations | Review last response → check coherence → improve |

---

## Key Implementation Points

1. **Separate Producer and Critic roles** — different system prompts, different personas
2. **Critic should be specific** — "You are a senior software engineer reviewing for bugs, performance, and readability"
3. **Set max iterations** — prevent infinite loops (typically 2-3 rounds)
4. **Structured critique output** — critic should produce actionable feedback, not vague comments
5. **Memory of previous critiques** — agent should not repeat fixed errors

---

## Trade-offs

| Pro | Con |
|-----|-----|
| Dramatically higher output quality | 2-3x more API calls = higher cost |
| Catches errors single-pass misses | Higher latency (each loop = full LLM call) |
| More robust code generation | Context window fills up with history |
| Self-correcting behavior | Can get stuck in loops on subjective criteria |

---

## Intersection with Other Patterns

- **Goal Setting & Monitoring** — goal provides the benchmark for self-evaluation
- **Memory Management** — conversation history enables cumulative reflection
- **Planning** — reflection on a plan's feasibility before execution
- **Multi-Agent** — critic agent is a specialized collaborator
- **Exception Handling** — failed attempt → reflect → retry with different approach

---

## Implementation Skeleton

```javascript
async function reflectiveGeneration(task, maxIterations = 3) {
  // Initial generation
  let output = await producer.call({ prompt: task });
  
  for (let i = 0; i < maxIterations; i++) {
    // Critique
    const critique = await critic.call({
      role: "Senior Engineer",
      prompt: `Evaluate this output against these criteria:
        - Correctness
        - Completeness  
        - Code quality
        Output: ${output}
        Provide specific, actionable feedback as JSON.`
    });
    
    // Check if satisfactory
    if (critique.passesAllCriteria) break;
    
    // Refine
    output = await producer.call({
      prompt: `Improve this based on feedback:
        Original: ${output}
        Feedback: ${JSON.stringify(critique.feedback)}
        Generate improved version.`
    });
  }
  
  return output;
}
```

---

## Anti-Patterns
- ❌ Using reflection for simple factual lookups (overkill)
- ❌ No stopping condition (infinite refinement loops)
- ❌ Critic that's too vague ("make it better" vs. "fix the null check on line 5")
- ❌ Same agent critiquing its own work without role separation
- ❌ Reflecting without memory of previous iterations

---

# Interview Depth Extension

> Appended for AI/ML Engineer, Applied AI, and Agent Platform interview prep.
## Interview Answer (60–90s)

Reflection is generate→critique→revise with a stop condition. Prefer a separate critic with a rubric over self-critique. Use when quality beats latency. Stop on pass, max iterations, or diminishing returns. Don’t reflect on every trivial turn—it’s an expensive quality knob.

## Deep Dive

### Architecture
Producer → Critic(rubric) → gate → revise while k<K_max.
Prefer external verifiers (tests, retrieval) over pure LLM critique.

### Failure modes
Infinite polish, sycophancy, rubric overfitting, cost explosion.

### Metrics
Win-rate vs single-pass, iterations, $/latency multiplier.

### When NOT to use
Low-stakes chat; hard latency SLAs; tasks with automatic verifiers.

## Common Interview Questions

**Q1. Self vs critic?**

A: Separate critic reduces bias; can use different model.

**Q2. Stop loops?**

A: Max k, min score delta, blocker checklist.

**Q3. Hide bad reasoning?**

A: Yes—evaluate trajectories + external verifiers.

**Q4. Where in a graph?**

A: After high-risk user-visible generation.

**Q5. Vs rerank?**

A: Rerank selects; reflection edits. Often generate-N→rank→reflect.

**Q6. Always improve?**

A: No—can inject new errors; A/B test.

**Q7. Good rubric?**

A: Concrete dimensions + failure examples + evidence required.

## Anti-Patterns

- Unbounded improve loops
- Vague ‘make better’
- Reflect without available verifiers

## Production Checklist

- [ ] Rubric + stop
- [ ] Max iterations
- [ ] External verifiers
- [ ] A/B baseline
- [ ] Skip low-risk routes
