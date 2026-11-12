# Pattern 19: Evaluation and Monitoring

## What It Is
Systematically **assess agent performance**, monitor progress toward goals, and detect operational anomalies. Continuous measurement of effectiveness, efficiency, and compliance — not just at development time, but in production.

## When to Use
- Agents deployed in live production environments
- Need to compare different agent versions (A/B testing)
- Regulated domains requiring compliance/safety audits
- Performance may degrade over time (drift)
- Evaluating complex behavior (trajectories, not just final output)

## Rule of Thumb
Use when deploying agents in live environments where real-time performance and reliability are critical.

---

## What to Measure

### Basic Metrics
| Metric | What It Measures |
|--------|-----------------|
| Response Accuracy | Is the answer factually correct? |
| Latency | How long does the agent take? |
| Token Usage | How many tokens consumed (cost proxy)? |
| Tool Success Rate | What % of tool calls succeed? |
| Task Completion Rate | What % of tasks reach goal? |

### Advanced Metrics
| Metric | What It Measures |
|--------|-----------------|
| Trajectory Quality | Did the agent take the RIGHT steps (not just reach right answer)? |
| Drift Detection | Is performance degrading over time? |
| Anomaly Detection | Any unexpected/unusual agent behaviors? |
| Faithfulness (RAG) | Does response align with retrieved data? |
| Relevance (RAG) | Are retrieved documents actually relevant? |

---

## Evaluation Methods

### 1. Trajectory Evaluation ⭐ Critical
Don't just evaluate the final answer — evaluate the **sequence of steps**:
```
Expected: Query → Search → Analyze → Respond
Actual:   Query → Search → Search → Search → Respond (excessive searching)
```
Compare actual agent trajectory against ideal "ground truth" trajectory.

### 2. LLM-as-a-Judge
Use one LLM to evaluate another's output on subjective qualities:
- Helpfulness, coherence, completeness
- More nuanced than string matching
- Score on multiple dimensions

### 3. Automated Test Suites
- Unit tests for individual agent components
- Integration tests for full workflows
- Regression tests for known failure cases
- Eval sets with expected behaviors

### 4. A/B Testing
Run two agent versions in parallel:
- Compare metrics on same traffic
- Identify which version performs better
- Statistically validate improvements

### 5. Contractor Model (Formalized Contracts)
For high-stakes: define formal "AI contracts" that specify:
- Exact deliverables
- Quality criteria
- Verification methods
- Agent self-validates against contract before returning

---

## Key Implementation Points

1. **Log everything** — actions, tool calls, reasoning, latency, tokens, outcomes
2. **Structured logging** — JSON format, not free text; queryable
3. **Baselines first** — establish performance baseline before optimizing
4. **Alert on anomalies** — automated detection of unusual behavior patterns
5. **Evaluate both output AND process** — trajectory matters as much as answer
6. **Continuous, not one-time** — deploy monitoring as part of the system
7. **Multiple eval dimensions** — accuracy alone is insufficient

---

## Monitoring Infrastructure

```
[Agent in Production]
  → Structured Logs → [Time-series DB / Observability Platform]
  → Metrics Dashboard → [Accuracy, Latency, Cost, Success Rate]
  → Anomaly Detection → [Alert if drift/degradation detected]
  → Periodic Eval Runs → [Compare against ground-truth test sets]
```

---

## Trade-offs

| Pro | Con |
|-----|-----|
| Catch regressions before users notice | Infrastructure cost |
| Data-driven improvement decisions | Defining good metrics is hard |
| Compliance/audit evidence | Over-monitoring can slow system |
| Early warning on degradation | False alarms if thresholds too tight |

---

## Connection to Other Patterns
- **Goal Monitoring (Ch11)** — self-monitoring vs. external evaluation
- **Reflection** — internal quality check vs. external measurement
- **Guardrails** — measure guardrail false positive/negative rates
- **Learning** — evaluation data feeds learning loops
- **HITL** — human evaluation as ground truth
- **Resource-Aware** — track cost efficiency alongside quality

---

## Anti-Patterns
- ❌ Only evaluating final output (ignoring trajectory quality)
- ❌ No baseline to compare against
- ❌ Exact string matching for semantic evaluation (use embedding similarity or LLM-judge)
- ❌ One-time eval at development, then deploy-and-forget
- ❌ Measuring everything but acting on nothing

---

# Interview Depth Extension

> Appended for AI/ML Engineer, Applied AI, and Agent Platform interview prep.
## Interview Answer (60–90s)

Eval & monitoring measure whether agents work—offline suites, trajectory graders, online metrics, and drift detection. Final-answer accuracy alone is insufficient; score process, tools, safety, and cost. Ship with golden sets, LLM-as-judge carefully calibrated, and production traces feeding failure clusters.

## Deep Dive

### Stack
Unit (tools/schemas) → trajectory evals → human prefs → online KPIs → alerts.
Judge models need agreement checks with humans.

### Failure modes
Metric theater, overfitting to judges, ignoring rare safety fails, no slice analysis.

### Metrics
Task success, faithfulness, tool correctness, latency/$, safety incidents, drift.

### When NOT to use
Never—if you can’t eval, you can’t ship responsibly. Scale depth to risk.

## Common Interview Questions

**Q1. Trajectory vs final answer?**

A: Lucky correct answers can hide bad tool use/risk.

**Q2. LLM-as-judge pitfalls?**

A: Bias, verbosity preference; calibrate with humans; use rubrics.

**Q3. Online monitoring?**

A: Success proxies, thumbs, escalation, cost, guardrail hits.

**Q4. Eval for agents vs chatbots?**

A: Include tool sequences, state, long-horizon goals.

**Q5. Prevent overfitting?**

A: Holdouts, fresh tasks, production resampling.

**Q6. CI integration?**

A: Gate merges on eval regressions like unit tests.

**Q7. Staff system?**

A: Eval harness as platform product with ownership.

## Anti-Patterns

- Only vibe-check demos
- One aggregate accuracy number
- No safety slice
- Judges without calibration

## Production Checklist

- [ ] Golden + adversarial sets
- [ ] Trajectory scoring
- [ ] Judge calibration
- [ ] Prod trace sampling
- [ ] CI gates
- [ ] Drift alerts
- [ ] Cost/quality dashboards
