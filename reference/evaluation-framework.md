# Prompt Evaluation Framework

A structured rubric for scoring prompt quality across 8 dimensions. Use this during the Review workflow.

---

## Scoring Scale

Each dimension is scored 1-5:

| Score | Label | Meaning |
|---|---|---|
| 1 | Critical Failure | Fundamentally broken — will cause serious issues in production |
| 2 | Significant Issues | Works sometimes but has clear failure modes |
| 3 | Acceptable | Functional but has room for improvement |
| 4 | Good | Solid prompt with minor issues |
| 5 | Excellent | Production-ready, handles edge cases, efficient |

---

## Dimension 1: Clarity (Weight: 15%)

Can the model unambiguously interpret every instruction?

| Score | Anchor |
|---|---|
| 1 | Multiple instructions have 2+ valid interpretations. Model will behave unpredictably. |
| 2 | Some instructions are vague ("be appropriate", "respond accordingly") without definitions. |
| 3 | Most instructions are clear. A few edge cases are undefined. |
| 4 | Instructions are clear and specific. Minor ambiguity in 1-2 areas. |
| 5 | Every instruction has exactly one interpretation. Edge cases are defined. |

**What to check:**
- Are there vague qualifiers? ("appropriate", "relevant", "as needed")
- Are conditional instructions clear about their conditions?
- Could any instruction be misinterpreted by a literal reader?
- Are there undefined terms used as if they have specific meaning?

---

## Dimension 2: Specificity (Weight: 15%)

Are behaviors defined concretely, not vaguely?

| Score | Anchor |
|---|---|
| 1 | Only platitudes ("be helpful", "be accurate"). No concrete behavioral definitions. |
| 2 | Some concrete rules but many rely on model judgment without guidance. |
| 3 | Key behaviors are defined. Secondary behaviors left to model defaults. |
| 4 | Most behaviors are tied to specific conditions and actions. |
| 5 | Every behavioral instruction maps a condition to an action. No vague directives. |

**What to check:**
- Can you predict the model's output for a given input based on the instructions alone?
- Are there instructions that say "be X" without defining what X looks like in practice?
- Are measurable criteria provided where applicable (word count, format, scope)?

---

## Dimension 3: Constraint Quality (Weight: 15%)

Are constraints scoped, non-conflicting, and complete?

| Score | Anchor |
|---|---|
| 1 | No constraints, or constraints that directly conflict with each other. |
| 2 | Constraints exist but are over-broad ("NEVER discuss X") or have obvious gaps. |
| 3 | Constraints are present and mostly scoped. One or two conflicts or gaps. |
| 4 | Constraints are well-scoped with permissions alongside prohibitions. Minor gaps. |
| 5 | Constraints are scoped, non-conflicting, use meta-rules where appropriate, and include conflict resolution ordering. |

**What to check:**
- Are there absolute bans that should be scoped?
- Do any two constraints contradict each other?
- Are prohibitions paired with permissions (what NOT to do AND what to do instead)?
- Is there a priority ordering for conflicting constraints?
- Are there edge cases that fall between constraints?

---

## Dimension 4: Hallucination Resistance (Weight: 10%)

Does the prompt ground the model and handle uncertainty?

| Score | Anchor |
|---|---|
| 1 | No grounding at all. Model is free to fabricate without limits. |
| 2 | Basic "don't make things up" instruction without mechanism. |
| 3 | Knowledge boundaries stated. Basic uncertainty handling ("say you don't know"). |
| 4 | Knowledge boundaries + uncertainty handling + verification steps (search before answering). |
| 5 | Full grounding: boundaries, uncertainty language, verification tools, source citation, and explicit handling of post-cutoff questions. |

**What to check:**
- Is the knowledge cutoff date stated?
- Is there an instruction for handling questions beyond the model's knowledge?
- Does the model have verification tools, and is it instructed to use them?
- Is the model instructed on how to express uncertainty?

---

## Dimension 5: Token Efficiency (Weight: 10%)

Is the prompt as concise as possible without losing meaning?

| Score | Anchor |
|---|---|
| 1 | Massive redundancy. Same instruction stated 3+ times. Paragraph-long preambles. |
| 2 | Noticeable redundancy. Several filler phrases. Could be 30%+ shorter. |
| 3 | Some waste but mostly lean. A few redundant instructions or verbose phrases. |
| 4 | Tight prompt with minor opportunities for compression. |
| 5 | Every token earns its place. No redundancy, no filler, no over-explanation. |

**What to check:**
- Is any instruction repeated in different words?
- Are there preambles that could be cut? ("In your role as...", "It is important that...")
- Are examples necessary, or do the instructions speak for themselves?
- Could any instruction be shorter without losing meaning?

---

## Dimension 6: Maintainability (Weight: 10%)

Can someone update this prompt without breaking it?

| Score | Anchor |
|---|---|
| 1 | Monolithic block. No sections. Changing one thing risks breaking everything. |
| 2 | Some structure but tightly coupled. Adding a new feature requires touching multiple sections. |
| 3 | Organized into sections. Updates are contained but require careful reading. |
| 4 | Modular sections. Each topic is self-contained. Dynamic values are parameterized. |
| 5 | Clean modules, parameterized values ({{variables}}), conditional sections, and clear documentation of what each section controls. |

**What to check:**
- Are there hardcoded values that will need updating (dates, product names, URLs)?
- Can you add a new behavior without reading the entire prompt?
- Are there implicit dependencies between sections?
- Is the prompt structured so that a non-expert could safely modify it?

---

## Dimension 7: Robustness (Weight: 15%)

Does the prompt handle edge cases and adversarial inputs?

| Score | Anchor |
|---|---|
| 1 | Only handles the happy path. Any unexpected input produces unpredictable behavior. |
| 2 | Handles common cases but breaks on edge cases (out-of-scope, ambiguous, empty). |
| 3 | Handles most common cases. Some edge case guidance. No adversarial handling. |
| 4 | Handles edge cases explicitly. Basic injection/override resistance. |
| 5 | Handles edge cases, adversarial inputs, conflicting inputs, and missing context with defined fallbacks. |

**What to check:**
- What happens if the user sends an empty message?
- What happens if the user asks something completely out of scope?
- What happens if the user tries to override the system prompt?
- What happens if the user sends conflicting instructions?
- What happens if required context is missing?

---

## Dimension 8: Structure (Weight: 10%)

Is the prompt well-organized with clear sections?

| Score | Anchor |
|---|---|
| 1 | Stream of consciousness. No sections, no formatting, no hierarchy. |
| 2 | Some grouping but inconsistent. Headers used sporadically. |
| 3 | Organized into sections with headers. Information is grouped logically. |
| 4 | Clear hierarchy. Sections follow a logical order. Easy to navigate. |
| 5 | Layered architecture (Identity → Context → Rules → Tools → Domain → Format). Navigation is instant. |

**What to check:**
- Can you find any instruction within 10 seconds?
- Do sections follow a logical progression?
- Are related instructions grouped together?
- Is the formatting consistent throughout?
- Would a table of contents be possible based on the section structure?

---

## Calculating the Overall Score

```
Overall = (Clarity × 0.15) + (Specificity × 0.15) + (Constraints × 0.15)
        + (Hallucination × 0.10) + (Efficiency × 0.10) + (Maintainability × 0.10)
        + (Robustness × 0.15) + (Structure × 0.10)
```

| Grade | Range | Meaning |
|---|---|---|
| A | 4.5 - 5.0 | Excellent — production-ready |
| B | 3.5 - 4.4 | Good — minor improvements needed |
| C | 2.5 - 3.4 | Acceptable — several issues to address |
| D | 1.5 - 2.4 | Poor — significant rework needed |
| F | 1.0 - 1.4 | Failing — fundamental redesign required |

---

## Review Output Format

Present reviews in this structure:

```
## Prompt Review

**Overall Score**: X.X/5 (Grade X)

### Dimension Scores

| Dimension | Score | Note |
|---|---|---|
| Clarity | X/5 | [one-line observation] |
| Specificity | X/5 | [one-line observation] |
| Constraint Quality | X/5 | [one-line observation] |
| Hallucination Resistance | X/5 | [one-line observation] |
| Token Efficiency | X/5 | [one-line observation] |
| Maintainability | X/5 | [one-line observation] |
| Robustness | X/5 | [one-line observation] |
| Structure | X/5 | [one-line observation] |

### Issues Found

1. **[Dimension]**: "[quote from prompt]" — [what's wrong] — [how to fix]
2. ...

### Rewritten Prompt

[improved version in a code block]
```
