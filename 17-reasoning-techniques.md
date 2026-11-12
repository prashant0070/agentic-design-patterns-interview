# Pattern 17: Reasoning Techniques

## What It Is
Advanced methods for making an agent's **internal reasoning explicit** — enabling multi-step logic, deliberation, self-correction, and exploration of multiple solution paths.

## Core Principle
> Allocate more computational resources during inference. Give the agent more "thinking time" to process, explore, and refine — accuracy improves dramatically for complex problems.

---

## Technique Catalog

### 1. Chain-of-Thought (CoT) ⭐ Most Important
Force the model to generate **intermediate reasoning steps** before the final answer.

```
"Think step by step. First analyze the problem, then..."
```

**Why it works:** Decomposes difficult single-step problems into easier sequential steps.

**Variants:**
- **Zero-Shot CoT:** Just add "Let's think step by step"
- **Few-Shot CoT:** Provide examples with full reasoning shown

**Best practices:**
- Final answer AFTER reasoning steps (not before)
- Temperature = 0 for tasks with single correct answer
- Increases token usage but dramatically improves accuracy

---

### 2. Tree of Thoughts (ToT)
Explore **multiple reasoning paths concurrently** in a tree structure.

```
Problem → Branch A (approach 1) → Evaluate
        → Branch B (approach 2) → Evaluate
        → Branch C (approach 3) → Evaluate → Best branch wins
```

**Use when:** Problem requires exploration, backtracking, or evaluating multiple possibilities.

---

### 3. Self-Correction / Self-Refinement
Agent evaluates its own output and **iteratively improves** it.

```
Generate → Evaluate against criteria → Fix issues → Re-evaluate → Repeat
```

Related to Reflection (Ch4), but here focused on reasoning correctness.

---

### 4. ReAct (Reason and Act) ⭐ Critical for Agents
Interleave **thinking with action** in a loop:

```
Thought: "I need to find the current price..."
Action: Search("current stock price AAPL")
Observation: "$187.50"
Thought: "Now I need to calculate potential profit..."
Action: Calculator(100 * (187.50 - 150))
Observation: "$3,750"
Thought: "I have all the information needed."
Final Answer: "..."
```

**This is THE core agentic loop.** It enables agents to reason about tool outputs and adapt dynamically.

---

### 5. Chain of Debates (CoD)
Multiple agents **debate** different perspectives to reach a better conclusion.

```
Agent A: "I believe X because..."
Agent B: "I disagree because..."
Agent A: "Considering your point, I now think..."
→ Synthesize consensus
```

Reduces individual biases through adversarial collaboration.

---

### 6. Self-Consistency
Generate **multiple diverse reasoning paths** for the same problem, then majority-vote on the answer.

```
Path 1: Reasoning → Answer A
Path 2: Reasoning → Answer B  
Path 3: Reasoning → Answer A
→ Majority: Answer A (2/3 confidence)
```

**Pro:** Pseudo-probability of correctness  
**Con:** Runs model multiple times = higher cost

---

### 7. Step-Back Prompting
First ask the model to consider **general principles**, then solve the specific problem.

```
Step-back: "What are the key factors of a good detective story?"
Then: "Using those factors, write a plot summary for a mystery novel."
```

---

### 8. Program-Aided Language Models (PAL)
Generate **code** to solve computational problems instead of solving them in natural language.

```
LLM generates: "def solve(): return 7 * 24 * 365"
Execute code → get precise answer
```

---

## Scaling Inference Law

> "An agent's performance is not just about model size — it's about allocated 'thinking time.'"

More compute at inference time → better quality autonomous actions. This is why:
- CoT (more tokens = more thinking)
- ToT (more branches = more exploration)
- Self-Consistency (more runs = more confidence)
- ReAct loops (more iterations = more grounded)

---

## Practical Use Cases

| Domain | Reasoning Technique |
|--------|-------------------|
| Complex Q&A | CoT for multi-hop reasoning |
| Math/Science | PAL + CoT for precision |
| Code debugging | ReAct (reason → test → observe → fix) |
| Strategic planning | ToT for evaluating multiple strategies |
| Medical diagnosis | CoT for systematic symptom analysis |
| Legal analysis | CoT + Self-Correction for logical rigor |
| Research | ReAct + Deep Research for iterative exploration |

---

## Key Implementation Points

1. **CoT is nearly free to add** — just "think step by step" can dramatically help
2. **ReAct is the standard agent loop** — every tool-using agent should use it
3. **Temperature 0 for deterministic reasoning** — don't want randomness in logic
4. **Show work before answer** — generation of reasoning influences final token predictions
5. **ToT is expensive** — use only for genuinely complex problems worth the compute
6. **Combine techniques** — CoT + ReAct + Self-Correction is a powerful combo

---

## Connection to Other Patterns
- **Planning** — reasoning generates the plan
- **Reflection** — self-correction is reasoning applied to own output
- **Tool Use** — ReAct combines reasoning with tool calls
- **Multi-Agent** — CoD enables collaborative reasoning
- **Resource-Aware** — more reasoning = more cost; balance against budget
- **Evaluation** — evaluate reasoning quality (trajectory), not just final answer

---

# Interview Depth Extension

> Appended for AI/ML Engineer, Applied AI, and Agent Platform interview prep.
## Interview Answer (60–90s)

Reasoning techniques—CoT, ReAct, ToT, self-consistency—shape how models deliberate before acting. CoT helps multi-step logic; ReAct interleaves tools; ToT explores branches; self-consistency votes diverse samples. Pick based on verifiability and latency. Verbose reasoning isn’t always better—structure and tools often beat longer thoughts.

## Deep Dive

### Map
CoT: latent multi-step. ReAct: thought↔act↔observe. ToT: search over thoughts. Self-consistency: sample+vote. Reflexion: verbal RL via episodic memory.

### Failure modes
Unfaithful CoT, tool-looping ReAct, combinatorial ToT cost, majority vote on shared bias.

### Metrics
Accuracy vs tokens/latency, tool efficiency, calibration.

### When NOT to use
Trivial lookups; when external solvers exist; hard latency caps.

## Common Interview Questions

**Q1. CoT vs ReAct?**

A: CoT internal; ReAct for external grounding via tools.

**Q2. When ToT?**

A: Hard search problems with heuristic scoring; costly.

**Q3. Self-consistency?**

A: Helps math/logic variance; less for open creative tasks.

**Q4. Hide CoT in prod?**

A: Often yes for UX/safety; keep traces internally.

**Q5. Faithful reasoning?**

A: Not guaranteed; prefer verifiers over trusting CoT text.

**Q6. Combine?**

A: Plan sketch → ReAct locally → reflect with verifier.

**Q7. Interview tip?**

A: Discuss trade-offs with concrete latency/cost numbers.

## Anti-Patterns

- Always max thinking tokens
- ToT on FAQs
- Trusting CoT as audit proof
- No stop on ReAct loops

## Production Checklist

- [ ] Technique chosen per route
- [ ] Token/step budgets
- [ ] Verifiers where possible
- [ ] Trace storage
- [ ] A/B vs baseline
