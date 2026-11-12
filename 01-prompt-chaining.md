# Pattern 1: Prompt Chaining (Pipeline Pattern)

## What It Is
Break a complex task into a **sequence of smaller, focused sub-prompts** where the output of one step becomes the input for the next. Each step has a single responsibility.

## When to Use
- Task is too complex for a single prompt
- Multiple distinct processing stages exist
- You need interaction with external tools between steps
- Building multi-step reasoning systems that maintain state

## When NOT to Use
- Simple single-turn Q&A
- Tasks that can be handled reliably in one prompt
- When latency of multiple calls is unacceptable

---

## How It Works

```
[User Input]
  → Step 1: Summarize (role: "Market Analyst")
  → Step 2: Extract trends (role: "Trend Analyst") 
  → Step 3: Draft email (role: "Documentation Writer")
  → [Final Output]
```

Each step:
1. Receives focused input (output from previous step)
2. Has a specific role/persona assignment
3. Produces structured output (ideally JSON) for the next step
4. Can be independently tested and debugged

---

## Key Implementation Points

### Structured Output Between Steps
The reliability of a chain depends on data integrity between steps. Always use structured formats:

```json
{
  "trends": [
    {
      "trend_name": "AI-Powered Personalization",
      "supporting_data": "73% of consumers prefer personalized experiences"
    }
  ]
}
```

### Role Assignment Per Step
Each step should have a distinct persona:
- Step 1: "You are a Market Analyst. Summarize the key findings..."
- Step 2: "You are a Trend Analyst. Extract the top 3 trends..."
- Step 3: "You are a Documentation Writer. Draft a concise email..."

### Context Engineering
The quality of output depends more on what context you provide than how you phrase the ask. Each step should receive:
- Clear instruction
- Relevant data from previous step
- Format specification for output

---

## Problems Single Prompts Cause

| Problem | Description |
|---------|-------------|
| Instruction Neglect | Parts of long prompts get overlooked |
| Contextual Drift | Model loses track of initial context |
| Error Propagation | Early errors amplify through the response |
| Hallucination | Cognitive load increases incorrect generation |

---

## Trade-offs

| Pro | Con |
|-----|-----|
| Better accuracy per step | Higher latency (multiple API calls) |
| Easier debugging | More complex orchestration code |
| Each step testable independently | Higher token cost |
| Can integrate tools between steps | Chain failure if one step fails |

---

## Connection to Other Patterns
- **Routing** adds conditional branching between chain steps
- **Parallelization** runs independent chain branches concurrently
- **Reflection** adds a critique step after generation
- **Tool Use** enables external actions between chain steps
- **Planning** generates the chain dynamically instead of hardcoding it

---

## Implementation Skeleton (Conceptual)

```javascript
async function promptChain(input) {
  // Step 1: Analyze
  const analysis = await llm.call({
    role: "Market Analyst",
    prompt: `Summarize key findings: ${input}`,
    outputFormat: "json"
  });

  // Step 2: Extract
  const trends = await llm.call({
    role: "Trend Analyst", 
    prompt: `Extract top 3 trends from: ${JSON.stringify(analysis)}`,
    outputFormat: "json"
  });

  // Step 3: Compose
  const email = await llm.call({
    role: "Documentation Writer",
    prompt: `Draft email about these trends: ${JSON.stringify(trends)}`,
    outputFormat: "text"
  });

  return email;
}
```

---

# Interview Depth Extension

> Appended for AI/ML Engineer, Applied AI, and Agent Platform interview prep.
## Interview Answer (60–90s)

Prompt chaining breaks a hard task into sequential focused LLM stages where each stage’s structured output feeds the next. Use it when a mega-prompt would suffer instruction neglect or error amplification. In production I enforce schemas between hops, validate, checkpoint, and retry only the failed stage. Trade-off: higher latency/cost for debuggability and accuracy. Skip when one call meets the SLA or steps should be parallel.

## Deep Dive

### Architecture
```
Input → Stage_i (role + schema) → Validator → … → Output
              ↑ bounded repair/retry
```
- Typed contracts per hop (JSON Schema/Pydantic/Zod).
- Carry a minimal state bag; drop intermediate prose.
- Idempotent stage keys `(run_id, stage_id, input_hash)`.
- Resume from last good checkpoint.

### Failure modes
Schema drift, silent truncation, cascading hallucination, latency blowups from too many serial calls.

### Metrics
Per-stage schema-valid %, success rate, tokens/stage, p95 latency, E2E task success.

### When NOT to use
Single-turn FAQ; sub-300ms paths; deterministic ETL better in code; dynamic plans that need a planner.

## Common Interview Questions

**Q1. Why not one prompt?**

A: Attention dilution and mixed objectives. Chaining gives testable single-responsibility stages.

**Q2. Stop error propagation?**

A: Validate schemas, mid-chain tool checks, critic on high-risk stages, fail closed.

**Q3. Chain vs Planning?**

A: Chain is fixed/lightly parameterized; planning discovers steps at runtime.

**Q4. How to test?**

A: Golden I/O per stage, schema contract tests, trajectory integration tests.

**Q5. Where put tools?**

A: Between stages needing external truth—before synthesis.

**Q6. Bound cost?**

A: Fewer stages, small models for extract, cache intermediates.

**Q7. What breaks first?**

A: Free-text handoffs and unbounded full-chain retries.

## Anti-Patterns

- Raw prose between stages
- 12+ serial calls for simple tasks
- Retry entire chain on late failure
- No stage-level traces

## Production Checklist

- [ ] Typed schema between stages
- [ ] Per-stage timeout/retry/budget
- [ ] Checkpoints
- [ ] Tracing
- [ ] Model tiering
- [ ] Eval per stage + E2E
