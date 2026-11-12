# Pattern 20: Prioritization

## What It Is
Enable agents to **assess, rank, and schedule** tasks, objectives, or actions based on significance, urgency, dependencies, and resource constraints. Focus effort on what matters most.

## When to Use
- Agent encounters multiple potential actions simultaneously
- Conflicting goals with limited resources
- Time-sensitive tasks mixed with background work
- Multi-step plans where step ordering impacts efficiency
- Dynamic environments where priorities shift in real-time

## Rule of Thumb
Use when an agentic system must autonomously manage multiple, often conflicting tasks under resource constraints.

---

## Prioritization Criteria

| Criterion | Description |
|-----------|-------------|
| Urgency | Time sensitivity — deadline proximity |
| Importance | Impact on primary objective |
| Dependencies | Must this complete before other tasks can start? |
| Resource availability | Are required tools/data/budget ready? |
| Cost/Benefit | Effort required vs. expected outcome |
| User preferences | Personalized priority for user-facing agents |
| Risk | What's the cost of NOT doing this now? |

---

## Prioritization Levels

| Level | Description | Example |
|-------|-------------|---------|
| Strategic (high) | Choose overarching objective | "Focus on revenue features over tech debt" |
| Tactical (mid) | Order steps within a plan | "Do data validation before processing" |
| Operational (low) | Pick immediate next action | "Answer urgent ticket before routine one" |

---

## Dynamic Re-Prioritization

Critical capability: priorities change as conditions change.

```
[Initial Priority Queue]
  → New urgent event arrives
  → Re-evaluate all priorities
  → Re-order queue
  → Continue with highest-priority item
```

Triggers for re-prioritization:
- New critical event (system outage, deadline change)
- Task failure requiring replanning
- Resource constraint change (budget depleted)
- New information changing importance assessment

---

## Practical Use Cases

| Domain | Prioritization Applied |
|--------|----------------------|
| Customer support | Urgent outage reports > routine password resets |
| Cloud computing | Critical apps get resources in peak; batch jobs off-peak |
| Autonomous driving | Collision avoidance > lane discipline > fuel efficiency |
| Trading | High-margin, time-sensitive trades > low-margin bulk orders |
| Project management | Deadline + dependency + strategic importance |
| Cybersecurity | Critical threat severity > low-risk alerts |
| Personal assistant | Calendar urgency + user-defined importance |

---

## Key Implementation Points

1. **Define criteria explicitly** — agent must know HOW to evaluate priority
2. **Scoring function** — combine criteria into a single priority score for comparison
3. **Dependency awareness** — can't do Step 3 before Step 2 completes
4. **Time-aware** — deadlines affect urgency dynamically
5. **Feedback loop** — track outcomes to improve priority scoring over time
6. **Escalation for ties** — when priorities are equal, have a tiebreaker rule

---

## Implementation Skeleton

```javascript
function prioritize(tasks) {
  return tasks
    .map(task => ({
      ...task,
      score: 
        (task.urgency * 3) +      // urgency weighted highest
        (task.importance * 2) +    // importance next
        (task.hasDependents ? 2 : 0) +  // blocking others = boost
        (task.resourceReady ? 1 : -1)   // ready = boost, not ready = penalty
    }))
    .sort((a, b) => b.score - a.score);
}
```

---

## Trade-offs

| Pro | Con |
|-----|-----|
| Focused effort on highest-value work | Criteria definition requires domain knowledge |
| Efficient resource utilization | Re-prioritization adds compute overhead |
| Adaptive to changing conditions | Can starve low-priority tasks indefinitely |
| Mirrors human decision-making | Complex scoring functions are hard to tune |

---

## Connection to Other Patterns
- **Planning** — plan steps need ordering by priority
- **Resource-Aware** — budget constraints influence priority (can't afford expensive task)
- **Goal Setting** — goals define what's important
- **Multi-Agent** — agents negotiate priority across the team
- **Exception Handling** — failures may re-prioritize recovery tasks to top
- **HITL** — human sets strategic priorities; agent handles tactical

---

## Anti-Patterns
- ❌ Fixed priority that never changes (ignoring dynamic conditions)
- ❌ Everything is "high priority" (= nothing is)
- ❌ No dependency tracking (tasks execute in wrong order)
- ❌ Starving low-priority tasks indefinitely (they accumulate)
- ❌ Complex scoring without clear justification for weights

---

# Interview Depth Extension

> Appended for AI/ML Engineer, Applied AI, and Agent Platform interview prep.
## Interview Answer (60–90s)

Prioritization ranks competing goals/actions under urgency, importance, dependencies, and resources. Agents without it thrash or starve critical work. Encode policies (e.g., urgency×impact with dependency constraints) and re-prioritize as state changes.

## Deep Dive

### Levels
Strategic objectives → tactical plan order → operational next-action.
Algorithms: scoring, constrained scheduling, bandits for explore/exploit.

### Failure modes
Priority inversion, starvation, oscillating priorities, ignoring dependencies.

### Metrics
SLA hit rate, starvation time, context-switch cost, value delivered/time.

### When NOT to use
Single-task agents; fixed pipelines with no contention.

## Common Interview Questions

**Q1. Eisenhower for agents?**

A: Useful 2×2 prior; still need dependency/resource constraints.

**Q2. Dynamic reprioritization?**

A: On new high-urgency events; debounce to avoid thrash.

**Q3. Multi-agent priorities?**

A: Global scheduler or market/auction for resources.

**Q4. Starvation?**

A: Aging boosts; fairness quotas.

**Q5. Relate to planning?**

A: Planner proposes; prioritizer orders under constraints.

**Q6. User interrupts?**

A: Preemption policy with safe pause/resume.

**Q7. Eval?**

A: Simulated queues with known optimal schedules.

## Anti-Patterns

- FIFO everything
- Priority without dependencies
- Constant reordering every token

## Production Checklist

- [ ] Explicit scoring policy
- [ ] Dependency-aware scheduler
- [ ] Preemption rules
- [ ] Starvation guards
- [ ] Telemetry on queue wait
