# Appendix G: Coding Agents & the Augmented Developer

## Overview
The era of the lone coder is over. The most effective teams now augment themselves with specialized AI agents — not replacing developers, but amplifying creativity and velocity.

> "Over 30% of new code at Google and Microsoft is now AI-assisted/generated." (2025)

---

## Three Foundational Principles

### 1. Human-Led Orchestration
Developer = team lead and architect. Always in the loop.
- Directs which agent to engage
- Provides necessary context
- Exercises FINAL judgment on all output

### 2. The Primacy of Context ⭐
> "An agent's performance is entirely dependent on the quality and completeness of its context. A powerful LLM with poor context is useless."

What to provide:
- Complete relevant source code
- External documentation / API definitions
- Clear goals, requirements, style guides ("the brief")

### 3. Direct Model Access
Use frontier models directly (Gemini 2.5 Pro, Claude Opus 4, GPT-4).
- Don't route through intermediaries that truncate context
- Don't use weaker models when task demands precision

---

## The Specialist Agent Team

| Agent | Role | Invocation |
|-------|------|-----------|
| **Scaffolder** | Writes new code / implements features | "You are a senior SW engineer. Based on 01_BRIEF.md..." |
| **Test Engineer** | Writes unit/integration/E2E tests | "You are a QA engineer. Write full test suite for..." |
| **Documenter** | Generates documentation | "You are a tech writer. Generate markdown docs for..." |
| **Optimizer** | Refactors for performance/readability | "Analyze for bottlenecks. Propose specific changes..." |
| **Code Supervisor** | Reviews code (Critique + Reflection) | "You are a principal engineer. First critique, then reflect to prioritize." |

### The Code Supervisor's Two-Step Process
1. **Critique** — identify bugs, style violations, logical flaws
2. **Reflect** — synthesize findings, prioritize critical issues, dismiss pedantic ones

This mirrors the Reflection pattern (Ch4) applied to code review.

---

## Context Staging Area

For each task, prepare a focused "briefing package":
```
task-context/
├── 01_BRIEF.md        # Goals, requirements, constraints
├── 02_CODE/           # Relevant source files
├── 03_DOCS/           # API specs, design docs
└── 04_STYLE.md        # Coding standards, patterns to follow
```

---

## Practical Setup Checklist

1. **Provision 2+ frontier model APIs** — dual-provider for comparison and redundancy
2. **Local context orchestrator** — CLI tool / config file to compile context payloads
3. **Version-controlled prompt library** — `/prompts/reviewer.md`, `/prompts/tester.md`
4. **Git hooks integration** — pre-commit triggers Code Supervisor for instant feedback

---

## Principles for Leading the Augmented Team

### Maintain Architectural Ownership
You define the "what" and "why". Agents accelerate the "how".

### Master the Art of the Brief
Think of prompts as complete briefing packages for a new team member, not simple commands.

### Act as the Ultimate Quality Gate
Agent output = proposal, never command. You validate, challenge, approve.

### Engage in Iterative Dialogue
If output isn't perfect, don't discard — refine. Provide corrective feedback, add context, retry.

---

## CLI Coding Agents

### Claude CLI (Claude Code)
- Deep understanding of entire project architecture
- Multi-file refactoring, API integration
- Git-integrated (branch/commit management)
- Extensible via MCP tools

### Gemini CLI
- Open-source, free tier available
- Multimodal (images + text)
- Google Cloud integration
- Massive context window
- "Reason and Act" loop

### Aider
- Works directly on files + auto-commits to Git
- Test-Driven Development workflow
- Model-agnostic (choose your own LLM)
- Transparent audit trail via Git history

### GitHub Copilot CLI
- Native GitHub ecosystem integration
- Can be assigned issues → produces PRs
- Repository-aware Q&A
- Shell command assistance

---

## Vibe Coding vs. Production Coding

| Aspect | Vibe Coding | Production (Agent Team) |
|--------|-------------|------------------------|
| Purpose | Ideation, prototyping, exploration | Robust, scalable, maintainable code |
| Interaction | Casual conversation | Structured briefs with context |
| Output quality | "Good enough to start" | Reviewed, tested, documented |
| Process | Generate → iterate | Brief → implement → test → review → approve |
| When | Exploring unfamiliar territory | Building real product features |

---

## Key Insight

> "The future is not human vs. machine, but a partnership between human ingenuity and AI, working as a single, seamlessly integrated team."

The developer's role evolves from "writing all the code" to:
- Strategic architecture decisions
- Context curation and briefing
- Quality gate and final judgment
- Creative problem-solving
- Domain expertise application

---

# Interview Depth Extension

> Appended for AI/ML Engineer, Applied AI, and Agent Platform interview prep.
## Interview Answer (60–90s)

Coding agents amplify developers via scaffold/test/doc/optimize loops under human orchestration. Performance is dominated by context quality—repo maps, brief, diffs—not model brand alone. Production patterns: constrained tools (fs, tests, git), sandboxes, review gates, and evals on real tasks. Treat agent code like untrusted PRs.

## Deep Dive

### Architecture
Brief → context pack → edit plan → patch → run tests/linters → reflect → PR.
Human reviews merges; agent may iterate in sandbox.

### Failure modes
Hallucinated APIs, drive-by refactors, secret leaks, flaky test chasing, context misses.

### Metrics
Task success, iterations, human edit distance, test pass, review time, escaped defects.

### When NOT to use
Unknown critical infra without tests; secrets-bearing prod access.

## Common Interview Questions

**Q1. Best context pack?**

A: Relevant files, interfaces, failing tests, style guide, constraints.

**Q2. Agent vs Copilot chat?**

A: Agents plan+tool+iterate; completion is local suggest.

**Q3. Safe tools?**

A: Sandbox FS, allowlisted commands, no prod credentials.

**Q4. Multi-agent coding?**

A: Specialists for implement/test/review with handoff contracts.

**Q5. Eval coding agents?**

A: SWE-bench-like; repo-specific golden tasks.

**Q6. Stop silent breakage?**

A: Mandatory tests + typed checks + human review on risky paths.

**Q7. Staff platform?**

A: Shared agent runtime, policy, eval harness for all eng agents.

## Anti-Patterns

- Unlimited shell as root
- Commit without tests
- Huge unrelated rewrites
- No human review

## Production Checklist

- [ ] Sandbox + allowlist
- [ ] Test gate
- [ ] PR diff review
- [ ] Secret scanning
- [ ] Repo eval suite
- [ ] Context packaging standards
