# Appendix A: Advanced Prompting Techniques

## Overview
Prompting is not just "asking questions" — it's a disciplined engineering practice for converting general-purpose LLMs into specialized, reliable tools.

---

## Core Principles (Non-Negotiable)

| Principle | Description |
|-----------|-------------|
| Clarity & Specificity | Unambiguous, precise instructions. No vague language. |
| Conciseness | Direct phrasing, active verbs. Cut fluff. |
| Action Verbs | "Summarize", "Extract", "Classify" — not "Think about" |
| Instructions > Constraints | Tell what TO DO, not what NOT to do |
| Iteration | Prompt engineering is iterative. Document attempts. |

### Power Verbs for Prompting
Act, Analyze, Categorize, Classify, Compare, Contrast, Create, Define, Describe, Evaluate, Extract, Find, Generate, Identify, List, Measure, Organize, Parse, Predict, Provide, Rank, Recommend, Retrieve, Rewrite, Select, Show, Sort, Summarize, Translate, Write.

---

## Basic Techniques

### Zero-Shot
No examples. Relies on model's training.
```
Translate "Hello, how are you?" to French.
```

### One-Shot
One example to set the pattern.
```
English: "Thank you." → Spanish: "Gracias."
English: "Please." → Spanish: ?
```

### Few-Shot
3-5 examples to establish clear pattern.
- **Quality matters**: Accurate, representative examples
- **Diversity matters**: Cover variations and edge cases
- **Mix classes**: For classification, randomize example order
- **Many-shot** (modern LLMs): Hundreds of examples with long context

---

## Structuring Prompts

### System Prompting
Sets overall context, persona, rules for the entire session:
```
You are a helpful AI assistant. Respond politely. Do not generate harmful content.
```

### Role Prompting
Assigns specific expertise:
```
Act as a senior data analyst with 10 years of experience in fintech.
```

### Delimiters
Separate instructions from data:
```
<instruction>Summarize the article focusing on main arguments.</instruction>
<article>[full text here]</article>
```

### Context Engineering ⭐ Critical Concept
> "The quality of a model's output depends more on the richness of provided context than on the model's architecture."

Context layers:
1. **System prompts** — foundational behavior rules
2. **External data** — retrieved documents, tool outputs
3. **Implicit data** — user identity, history, environment state

Context Engineering ≠ Prompt Engineering. It's about building a **complete operational picture** at runtime.

### Structured Output ⭐ Mandatory for Pipelines
Request specific formats (JSON, XML, tables):
```
Extract name, address, phone from the text. Return as JSON with keys "name", "address", "phone_number".
```

**Validate with Pydantic** — parse LLM output into typed objects:
```python
class User(BaseModel):
    name: str
    email: EmailStr
    interests: List[str]

user = User.model_validate_json(llm_output)
```

Without structured output + validation, agent pipelines WILL break.

---

## Reasoning Techniques (in prompts)

### Chain of Thought (CoT)
```
Let's think step by step.
1. First, identify the key variables...
2. Then, calculate...
3. Finally, conclude...
```

### Self-Consistency
Run same prompt multiple times with high temperature, majority-vote on answers.

### Step-Back Prompting
First ask for general principles, then solve specific problem:
```
Step 1: "What makes a good API design?"
Step 2: "Using those principles, design this specific API."
```

### Tree of Thoughts
Explore multiple reasoning paths, evaluate each, select best.

---

## Action Techniques

### Tool Use / Function Calling (in prompts)
```
You have access to: get_weather(city), search_web(query), calculate(expression).
When you need external info, output a function call in JSON format.
```

### ReAct (Reason + Act)
```
Thought: I need to find the current stock price...
Action: search_web("AAPL stock price today")
Observation: $187.50
Thought: Now I can calculate the profit...
Action: calculate("100 * (187.50 - 150)")
Observation: 3750
Final Answer: The profit would be $3,750.
```

---

## Advanced Techniques

### Automatic Prompt Engineering (APE)
Use an LLM to generate, evaluate, and refine prompts for you:
```
"Analyze this prompt and suggest improvements for consistently extracting entities from news articles..."
```

### Iterative Refinement
Start simple → test → analyze shortcomings → refine → repeat.

### Negative Examples
Show what NOT to do (use sparingly):
```
Do NOT include the Eiffel Tower in your list.
```

### Factored Cognition / Decomposition
Break complex task into sub-prompts, combine results.

### RAG in Prompts
Inject retrieved documents as context:
```
Based on these documents: [retrieved text]
Answer the user's question: [query]
```

### Using Analogies
```
Act as a "data chef". Take raw ingredients (data) and prepare a "summary dish" (report).
```

---

## Best Practices Checklist

- [ ] Provide examples (few-shot) whenever possible
- [ ] Keep prompts concise and clear
- [ ] Specify exact output format
- [ ] Use instructions over constraints
- [ ] Control max token length
- [ ] Use variables for dynamic prompts
- [ ] Mix classes in classification examples
- [ ] Set temperature=0 for deterministic tasks
- [ ] Document all prompt attempts
- [ ] Store prompts in version-controlled codebase
- [ ] Implement automated eval tests
- [ ] Adapt prompts when model updates

---

## Key Insight

> "Mastering this full spectrum of prompting is the definitive skill that elevates a generalist language model from a simple text generator into a truly sophisticated agent, capable of performing complex tasks with autonomy, awareness, and intelligence."

---

# Interview Depth Extension

> Appended for AI/ML Engineer, Applied AI, and Agent Platform interview prep.
## Interview Answer (60–90s)

Prompting is specifying objectives, constraints, formats, and examples so the model’s distribution peaks on useful outputs. For agents, prompts define roles, tool policies, and output contracts. Prefer clear action verbs, schemas, and eval-driven iteration over folklore. Context engineering—what you put in the window—usually beats clever wording.

## Deep Dive

### Techniques map
Zero/few-shot, CoT, role, delimiting, structured outputs, tool instructions, constitutional/self-reminders, dynamic prompt assembly.

### Failure modes
Contradictory instructions, buried key rules, example leakage, overlong system prompts.

### Metrics
Constraint adherence, schema validity, task success, sensitivity to paraphrase.

### When NOT to use
Trying to prompt away the need for tools/RAG/guardrails.

## Common Interview Questions

**Q1. System vs user vs tool msgs?**

A: System=policy; user=intent; tool=observations. Keep hierarchy clear.

**Q2. Few-shot risks?**

A: Spurious pattern copy; keep diverse/relevant; watch token cost.

**Q3. Structured outputs?**

A: Schemas + constrained decoding when available.

**Q4. Prompt injection defense?**

A: Hard delimiters, ignore-user-policy for tool text, post-filters.

**Q5. Eval prompts?**

A: Version prompts; A/B; regression suites.

**Q6. Context engineering?**

A: Select/order/budget content; often > micro-wording.

**Q7. Agents-specific?**

A: Explicit tool-choice policy, stop conditions, refusal style.

## Anti-Patterns

- Kitchen-sink system prompts
- Contradictory rules
- No versioning
- Prompt-only security

## Production Checklist

- [ ] Prompt versions in git
- [ ] Schemas for outputs
- [ ] Eval on change
- [ ] Delimiter standards
- [ ] Token budgets
- [ ] Injection tests
