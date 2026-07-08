# System Prompt Pre-Deployment Checklist

Go/no-go checklist before putting a system prompt into production.

---

## Identity & Context

- [ ] Identity is defined in 1-2 sentences (not a paragraph)
- [ ] Identity is specific to this use case (not "helpful AI assistant")
- [ ] Knowledge cutoff date is stated
- [ ] Available tools/capabilities are listed
- [ ] Deployment environment is specified (web, API, CLI, mobile)
- [ ] User context is defined or parameterized

## Behavioral Rules

- [ ] Primary directives are clear and actionable
- [ ] Constraints are scoped with permissions alongside prohibitions
- [ ] No conflicting instructions exist
- [ ] Meta-rules cover broad categories (instead of exhaustive lists)
- [ ] Most critical rules appear early in the prompt

## Safety & Guardrails

- [ ] Harmful content generation is addressed
- [ ] Out-of-scope requests have a defined response
- [ ] Prompt injection resistance is considered
- [ ] Escalation paths exist for issues the model cannot handle
- [ ] Safety constraints cannot be overridden by user instructions

## Tools (if applicable)

- [ ] Each tool has "when to use" and "when NOT to use" guidance
- [ ] Required vs optional parameters are clear
- [ ] Error handling for failed tool calls is defined
- [ ] Parallel vs sequential tool usage is specified

## Output

- [ ] Output format is explicitly specified
- [ ] Response length guidance is provided
- [ ] An output example is included for non-trivial formats
- [ ] Formatting rules don't conflict with content requirements

## Edge Cases

- [ ] Simulated: normal input produces correct output
- [ ] Simulated: edge case input is handled gracefully
- [ ] Simulated: adversarial input (override attempt) is rejected
- [ ] Simulated: missing context has a defined fallback
- [ ] Simulated: out-of-scope request is redirected appropriately

## Token Budget

- [ ] Prompt is under 500 lines (strongly recommended)
- [ ] No redundant instructions
- [ ] No filler phrases or preambles
- [ ] Examples are minimal and necessary
- [ ] Reference material is loaded on demand (not embedded if large)

---

## Decision

- **All checked → DEPLOY**
- **Any unchecked in Safety → DO NOT DEPLOY** until fixed
- **Other unchecked items → FIX** before deployment or document as accepted risk
