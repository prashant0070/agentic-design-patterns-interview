# Pattern 12: Exception Handling and Recovery

## What It Is
Equip agents to **detect, handle, and recover from operational failures** — tool errors, API failures, invalid data, network issues, or unexpected states — ensuring continued reliable operation.

## When to Use
- Any agent deployed in a real-world environment
- Systems where tool calls can fail (APIs, databases, external services)
- When operational reliability is a key requirement
- Environments with unpredictable inputs or conditions

## Rule of Thumb
Use for any AI agent in a dynamic, real-world environment where failures are possible.

---

## Three-Phase Process

### Phase 1: Error Detection
- Invalid/malformed tool outputs
- API errors (404, 500, timeout)
- Unusually long response times
- Incoherent/nonsensical responses
- Format violations in LLM output
- Proactive anomaly monitoring

### Phase 2: Error Handling
| Strategy | Description |
|----------|-------------|
| Logging | Record error details for debugging |
| Retries | Retry with adjusted parameters (exponential backoff) |
| Fallbacks | Switch to alternative tool/method/model |
| Graceful Degradation | Maintain partial functionality |
| Notification | Alert humans or other agents |

### Phase 3: Recovery
| Strategy | Description |
|----------|-------------|
| State Rollback | Undo recent changes to restore stable state |
| Diagnosis | Investigate root cause |
| Self-Correction | Adjust plan/logic/parameters to avoid repeat |
| Replanning | Generate new plan to work around the failure |
| Escalation | Delegate to human or higher-level system |

---

## Practical Use Cases

| Domain | Exception Scenario | Handling |
|--------|--------------------|----------|
| Chatbot | Database temporarily down | Inform user, retry, escalate |
| Trading bot | Insufficient funds error | Log, stop retry, notify user |
| Smart home | Light won't turn on | Retry → notify user of failure |
| Data processing | Corrupted file in batch | Skip file, continue, report at end |
| Web scraping | CAPTCHA / site structure changed | Pause, try proxy, report failed URL |
| Robotics | Component pickup failure | Readjust, retry, alert operator |

---

## Key Implementation Points

1. **Anticipate failure modes** — list all tools and their possible failure states
2. **Never crash silently** — always log and surface errors
3. **Retry with backoff** — don't hammer failing services
4. **Fallback chain** — primary → secondary → degraded → escalate
5. **State checkpointing** — save state before risky operations for rollback
6. **Combine with Reflection** — failed attempt → reflect on why → retry differently

---

## Implementation Pattern (Sequential Fallback)

```javascript
async function robustToolCall(primaryTool, fallbackTool, input) {
  try {
    // Attempt primary
    const result = await primaryTool(input);
    if (isValid(result)) return result;
    throw new Error('Invalid result from primary');
  } catch (primaryError) {
    log('warn', `Primary failed: ${primaryError.message}`);
    
    try {
      // Attempt fallback
      const fallbackResult = await fallbackTool(input);
      return fallbackResult;
    } catch (fallbackError) {
      log('error', `Fallback also failed: ${fallbackError.message}`);
      // Graceful degradation
      return { partial: true, message: 'Could not retrieve data', error: fallbackError.message };
    }
  }
}
```

---

## Trade-offs

| Pro | Con |
|-----|-----|
| Robust, reliable operation | Adds code complexity |
| Graceful degradation vs. crash | Retry logic can mask root issues |
| User trust through reliability | Fallbacks may produce lower quality |
| Self-healing behavior | Difficult to test all failure paths |

---

## Connection to Other Patterns
- **Reflection** — failed attempt triggers reflection on cause, then refined retry
- **Guardrails** — prevent errors from producing harmful outputs
- **Goal Monitoring** — monitoring detects when errors push agent off-goal
- **HITL** — escalation path when agent cannot self-recover
- **Multi-Agent** — one agent's failure shouldn't cascade to others
- **Resource-Aware** — fallback to cheaper/simpler model when primary fails

---

# Interview Depth Extension

> Appended for AI/ML Engineer, Applied AI, and Agent Platform interview prep.
## Interview Answer (60–90s)

Agent exceptions include model, tool, and semantic failures—wrong but confident answers. Classify errors, retry only when idempotent, degrade gracefully, escalate on risk, and use circuit breakers. ‘LLM said OK’ is not success—verify and recover.

## Deep Dive

### Taxonomy
Transient→retry; validation→repair; tool semantic→alt tool; policy→refuse/HITL; fatal→abort/alert.
Also: compensation/saga, dead-letter trajectories.

### Metrics
Error by class, MTTR, retry success, silent failure rate.

## Common Interview Questions

**Q1. Semantic failure?**

A: HTTP 200 but wrong answer—needs verifiers/evals.

**Q2. Retry LLMs?**

A: Timeouts/rate limits yes; careful with non-idempotent tools.

**Q3. Circuit breaker?**

A: Trip on error threshold; fail fast; half-open later.

**Q4. Compensation?**

A: Undo partial side effects when later steps fail.

**Q5. User messaging?**

A: Honest actionable errors; never invent success.

**Q6. Logging?**

A: Structured with trace_id; redact secrets.

**Q7. Chaos?**

A: Inject tool failures in staging.

## Anti-Patterns

- Bare except continue
- Infinite retries
- Fail open on safety
- Expose secrets in errors

## Production Checklist

- [ ] Error taxonomy
- [ ] Idempotent retry policy
- [ ] Circuit breakers
- [ ] Compensation
- [ ] Failure UX
- [ ] Dead-letter trajectories
