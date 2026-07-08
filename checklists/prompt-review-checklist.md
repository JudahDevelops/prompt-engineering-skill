# Prompt Review Checklist

Step-by-step checklist for reviewing any prompt. Work through each section in order.

---

## 1. Structural Integrity

- [ ] Prompt has clear section boundaries (headers, XML tags, or visual separators)
- [ ] Sections follow a logical order (identity → context → rules → tools → domain → format)
- [ ] Critical instructions are in the first 30% of the prompt
- [ ] No section is orphaned or out of place
- [ ] Related instructions are grouped together, not scattered

## 2. Clarity Audit

- [ ] No vague qualifiers without definitions ("appropriate", "relevant", "as needed")
- [ ] Each instruction has exactly one interpretation
- [ ] Conditional instructions clearly state their conditions
- [ ] No undefined terms used as if they have specific meaning
- [ ] No compound instructions (multiple requirements in one sentence)

## 3. Constraint Audit

- [ ] Constraints are scoped, not absolute (unless safety-critical)
- [ ] Prohibitions are paired with permissions ("Do not X. You may Y.")
- [ ] No two constraints contradict each other
- [ ] Gaps identified: what happens with out-of-scope requests?
- [ ] Priority ordering exists for potentially conflicting constraints

## 4. Hallucination Resistance

- [ ] Knowledge cutoff date is stated (if applicable)
- [ ] Uncertainty handling is defined ("If unsure, say so")
- [ ] Verification steps exist (search before asserting, cite sources)
- [ ] The model is NOT encouraged to "try anyway" when uncertain
- [ ] Factual claims have a grounding mechanism (tools, sources, boundaries)

## 5. Token Efficiency

- [ ] No instruction is repeated in different words
- [ ] No filler phrases ("It is important that...", "Please make sure to...")
- [ ] Examples are necessary (behavior is ambiguous without them)
- [ ] No over-explained concepts the model already understands
- [ ] Could any section be shorter without losing meaning?

## 6. Robustness

- [ ] Out-of-scope requests are handled
- [ ] Ambiguous inputs have a defined response
- [ ] Missing context has a fallback (ask, assume defaults, or decline)
- [ ] Adversarial inputs are considered (prompt injection, jailbreak attempts)
- [ ] Empty or malformed inputs have handling

## 7. Output Format

- [ ] Format is explicitly specified
- [ ] An example of the expected output is provided (if format is non-trivial)
- [ ] Format specification is compatible with content requirements
- [ ] Response length guidance is provided (word count, sentence count, or "concise vs detailed")

## 8. Maintainability

- [ ] No hardcoded values that will need frequent updates (dates, versions, URLs)
- [ ] Dynamic values use placeholders or injection points
- [ ] Sections are modular — adding a new feature doesn't require rewriting existing sections
- [ ] A non-expert could safely modify this prompt

---

## Scoring

After completing the checklist, score the prompt using the evaluation framework in `reference/evaluation-framework.md`. Map checklist failures to the 8 quality dimensions.

## Action Items

For every unchecked box:
1. Identify the specific line or section that fails
2. Classify the severity: CRITICAL (must fix), HIGH (should fix), LOW (nice to fix)
3. Write the specific fix
4. Apply fixes in severity order
