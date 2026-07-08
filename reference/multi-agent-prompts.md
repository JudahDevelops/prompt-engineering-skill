# Multi-Agent Prompt Design

Patterns for designing prompts in systems where multiple AI agents collaborate, hand off work, or orchestrate tasks.

---

## Orchestration Patterns

### 1. Coordinator-Worker

One agent routes tasks to specialized workers.

```
┌─────────────┐
│ Coordinator  │  ← receives all user requests
└──────┬──────┘
       │ routes based on intent
  ┌────┼────┐
  ▼    ▼    ▼
[Research] [Code] [Review]   ← specialized workers
```

**Coordinator prompt pattern:**
```
You are a task coordinator. You do NOT perform tasks yourself. You:
1. Analyze the user's request to determine its type
2. Route to the appropriate specialist agent
3. Relay the specialist's response to the user
4. Request clarification if the task is ambiguous

Available specialists:
- research: factual questions, information lookup, summarization
- code: writing, debugging, or reviewing code
- review: critiquing documents, proposals, or designs

Route to exactly ONE specialist per request. If a request requires
multiple specialists, break it into sequential sub-tasks.
```

**Worker prompt pattern:**
```
You are a [specialization] specialist. You receive tasks from a coordinator.

Your input will be a structured task:
- Task: [description of what to do]
- Context: [relevant background]
- Constraints: [any limitations]

Respond with your output only. Do not ask follow-up questions —
if context is insufficient, state your assumptions explicitly.
```

**When to use:** When tasks are clearly categorizable and each category benefits from specialized instructions. Common in support systems, development workflows, and content pipelines.

### 2. Pipeline

Agents process work in sequence, each adding to or transforming the previous output.

```
[Researcher] → [Drafter] → [Reviewer] → [Editor] → Final Output
```

**Pipeline stage prompt pattern:**
```
You are the [stage name] in a content pipeline.

Your input: [describe what you receive from the previous stage]
Your output: [describe what you produce for the next stage]

Process:
1. [step 1]
2. [step 2]
3. [step 3]

CRITICAL: Do not modify aspects outside your scope. Your job is
[specific scope]. Leave [other aspects] for the [next stage name].
```

**When to use:** When quality benefits from multiple specialized passes. Common in writing, code generation, and data processing.

### 3. Debate / Adversarial

Two agents argue different positions; a judge agent synthesizes.

```
[Advocate A] ──┐
               ├──→ [Judge] → Decision
[Advocate B] ──┘
```

**Advocate prompt pattern:**
```
You are an advocate for [position/approach A].

Given the topic: {{topic}}

Make the strongest possible case for [position A]. Include:
- Key arguments with evidence
- Anticipated counterarguments and rebuttals
- Practical benefits and tradeoffs

Be rigorous and honest. Acknowledge genuine weaknesses of your
position, but frame them constructively. Your goal is to give the
judge the best possible understanding of this approach.
```

**Judge prompt pattern:**
```
You are a decision judge. You will receive arguments from two advocates.

Evaluate both positions on:
1. Strength of evidence
2. Practical feasibility
3. Risk profile
4. Alignment with [stated criteria]

Produce a decision with:
- Recommendation (which approach, or a synthesis)
- Reasoning (key factors that drove the decision)
- Caveats (conditions under which the other approach would be better)
```

**When to use:** When decisions benefit from structured consideration of alternatives. Common in architecture decisions, strategy, and risk assessment.

---

## Handoff Protocols

When one agent passes work to another, the handoff must be structured.

### Structured Handoff Format

```
<handoff>
<from>agent-name</from>
<to>next-agent-name</to>
<task>What the next agent should do</task>
<context>
Relevant information the next agent needs:
- [fact 1]
- [fact 2]
- [decision already made]
</context>
<constraints>
- [limitation 1]
- [limitation 2]
</constraints>
<completed>
What has already been done:
- [completed step 1]
- [completed step 2]
</completed>
</handoff>
```

### Handoff Rules

1. **Include all context** — the receiving agent has no memory of prior steps
2. **State what's done AND what's left** — prevent duplication or gaps
3. **Specify constraints** — the receiving agent inherits scope boundaries
4. **Keep handoffs structured** — free-text handoffs lose information

---

## Shared Context Strategies

### Strategy 1: Full Context Pass

Every agent receives the complete conversation/document. Simple but token-expensive.

**Use when:** Context is small (<2000 tokens) and all agents need the full picture.

### Strategy 2: Summarized Context

A summary agent compresses context between stages.

**Summary agent prompt:**
```
Summarize the following for the next agent. Include:
- Key decisions made
- Current state of the deliverable
- Open questions
- Constraints still in effect

Keep under 200 words. Preserve specific details (numbers, names, decisions).
Do NOT include: reasoning history, alternatives considered, rejected approaches.
```

**Use when:** Context is large and only some details matter for the next stage.

### Strategy 3: Structured State Object

Pass a JSON or structured document between agents as the "state."

```json
{
  "task": "Write a blog post about prompt engineering",
  "status": "draft_complete",
  "decisions": {
    "tone": "technical but approachable",
    "length": "1500 words",
    "audience": "intermediate developers"
  },
  "current_draft": "...",
  "feedback": ["needs more examples in section 3", "intro is too long"],
  "next_step": "revision"
}
```

**Use when:** The pipeline is long or agents are stateless between invocations.

---

## Specialization Boundaries

### Defining Agent Scope

Each agent's prompt should clearly define:

1. **What it does** — its primary responsibility
2. **What it does NOT do** — tasks that belong to other agents
3. **When to escalate** — conditions that require handoff

```
You are a code review agent.

You DO:
- Check code for bugs, logic errors, and potential issues
- Verify edge case handling
- Assess readability and maintainability

You DO NOT:
- Refactor code (that's the refactoring agent's job)
- Write tests (that's the testing agent's job)
- Approve for merge (that's the team lead's decision)

Escalate to the security agent if you find:
- SQL injection, XSS, or other OWASP top 10 vulnerabilities
- Hardcoded credentials or secrets
- Insecure authentication patterns
```

### Avoiding Scope Creep

Multi-agent systems fail when agents overlap:

| Problem | Symptom | Fix |
|---|---|---|
| Overlapping scope | Two agents both edit the same section | Define exclusive ownership per agent |
| Scope gaps | No agent handles a certain input type | Add routing rules to coordinator, or expand an agent's scope |
| Agent overreach | Agent performs tasks outside its scope | Add explicit "Do NOT" boundaries |
| Circular handoffs | Agent A hands to B, B hands back to A | Add maximum handoff depth, define termination conditions |

---

## Design Checklist

Before deploying a multi-agent system:

- [ ] Each agent has a single, clear responsibility
- [ ] Agent scopes are non-overlapping (or overlaps are intentional and managed)
- [ ] Handoff format is structured and consistent
- [ ] Shared context strategy is chosen and documented
- [ ] Termination conditions are defined (when does the pipeline end?)
- [ ] Error handling: what happens if one agent fails?
- [ ] Escalation paths: what happens if an agent encounters something outside its scope?
- [ ] Maximum depth/iterations: is there a limit to prevent infinite loops?
