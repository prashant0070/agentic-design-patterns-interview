# Pattern 3: Parallelization

## What It Is
Execute **multiple independent sub-tasks concurrently** rather than sequentially. Identify tasks that don't depend on each other's output and run them at the same time.

## When to Use
- Multiple independent API calls, searches, or computations
- Tasks involve waiting for external I/O (APIs, databases)
- You need to gather information from multiple sources
- Generating multiple options/variations for comparison (A/B)

## Rule of Thumb
Use when a workflow contains multiple independent operations that can run simultaneously.

---

## How It Works

```
              ┌→ [Task A: Search news] ─────────┐
[Input] ──→  ├→ [Task B: Pull stock data] ──────├→ [Aggregator] → [Output]
              └→ [Task C: Check social media] ──┘
```

1. **Fan-out**: Split input into independent sub-tasks
2. **Execute concurrently**: All tasks run at the same time
3. **Fan-in**: Wait for all to complete, aggregate results
4. **Synthesize**: A final sequential step combines everything

---

## Practical Use Cases

| Domain | Parallel Tasks |
|--------|---------------|
| Research | Search multiple databases simultaneously |
| Travel planning | Check flights + hotels + restaurants + events concurrently |
| Data analysis | Run sentiment + keywords + categorization at once |
| Content creation | Generate headline + body + CTA + image simultaneously |
| Validation | Check email format + phone + address + profanity in parallel |
| Multi-modal | Analyze text sentiment + image content at the same time |
| A/B generation | Generate 3 headline variations simultaneously, pick best |

---

## Key Implementation Points

1. **Identify independence** — tasks must NOT depend on each other's output
2. **Handle partial failures** — if one task fails, others should still complete
3. **Set timeouts** — don't let one slow task block the entire pipeline
4. **Aggregate intelligently** — the synthesis step is usually sequential and needs all results
5. **Consider rate limits** — parallel API calls may hit rate limits faster

---

## Trade-offs

| Pro | Con |
|-----|-----|
| Dramatically reduced total latency | Higher complexity in orchestration |
| Better resource utilization | Harder to debug (non-deterministic ordering) |
| More responsive user experience | Rate limit pressure on APIs |
| Natural fit for gather-then-synthesize | Error handling is more complex |

---

## Framework Support

| Framework | Mechanism |
|-----------|-----------|
| LangChain (LCEL) | `RunnableParallel` — runs multiple runnables side-by-side |
| LangGraph | Multiple nodes from single state transition (parallel branches) |
| Google ADK | `ParallelAgent` — native parallel sub-agent execution |
| JavaScript | `Promise.all()` or `Promise.allSettled()` |

---

## Implementation Skeleton

```javascript
async function parallelGather(topic) {
  // Fan-out: launch all independent tasks
  const [news, stocks, social] = await Promise.all([
    searchNews(topic),
    getStockData(topic),
    checkSocialMedia(topic)
  ]);

  // Fan-in: synthesize results sequentially
  const report = await llm.call({
    prompt: `Synthesize these findings into a report:
    News: ${JSON.stringify(news)}
    Stocks: ${JSON.stringify(stocks)}
    Social: ${JSON.stringify(social)}`
  });

  return report;
}
```

---

## Connection to Other Patterns
- **Prompt Chaining** — parallel steps are often embedded within a larger sequential chain
- **Routing** — router decides which tasks to parallelize
- **Multi-Agent** — different agents handle different parallel branches
- **Planning** — planner identifies which plan steps can run in parallel
- **Resource-Aware** — parallel execution increases cost; balance with budget constraints

---

## Anti-Patterns
- ❌ Parallelizing tasks that actually depend on each other's output
- ❌ No error handling for partial failures
- ❌ Ignoring API rate limits when fanning out
- ❌ Making the aggregation step too simple (just concatenating without synthesis)

---

# Interview Depth Extension

> Appended for AI/ML Engineer, Applied AI, and Agent Platform interview prep.
## Interview Answer (60–90s)

Parallelization fans out independent subtasks, runs them concurrently, then aggregates. Best for I/O-bound work and candidate generation. Use bounded concurrency, per-branch timeouts, and explicit join policies (all/majority/first-success). Don’t parallelize dependent steps or unbounded fan-out—cost and races explode.

## Deep Dive

### Architecture
Fan-out → N workers (semaphore) → join policy → synthesizer.
Cancel siblings on deadline/first-success. Deterministic merge by source id.

### Failure modes
Thundering herd, partial failures, inconsistent merges, duplicate side effects.

### Metrics
Wall-clock speedup, branch success, $/request, p95 vs serial.

### When NOT to use
Sequential dependencies; tiny tasks; non-idempotent writes.

## Common Interview Questions

**Q1. Map-reduce vs speculative?**

A: Map-reduce partitions work; speculative runs alternatives and keeps best.

**Q2. Slow branch?**

A: Timeout + partial aggregate with missing-source disclosure.

**Q3. Races?**

A: Idempotent writes; immutable results; no shared mutable state.

**Q4. Best-of-N when?**

A: High variance tasks when cost is OK; score with critic.

**Q5. Bound cost?**

A: Max width, cheaper branch models, cache.

**Q6. Tools vs agents parallel?**

A: Tools for I/O; agents only if distinct policies needed.

**Q7. Why idempotency?**

A: Retries/duplicates can double-charge or double-create.

## Anti-Patterns

- Unbounded gather on LLMs
- Concat without reconciliation
- No global deadline

## Production Checklist

- [ ] Independence analysis
- [ ] Concurrency limit
- [ ] Branch timeouts
- [ ] Idempotency keys
- [ ] Cost breaker
- [ ] Per-branch spans
