# System Prompt Architecture

A production system prompt follows a layered architecture. Each layer serves a distinct purpose. The order matters — models weight earlier instructions more heavily.

---

## The 7 Layers

### Layer 1: Identity (1-2 sentences)

The opening sentence anchors the model's persona and scope.

**Pattern:**
```
You are [name/role], [creator/context]. You [primary function].
```

**Rules:**
- One sentence for identity, optionally one more for scope
- Be specific: "You are a customer support agent for Acme Corp's billing team" beats "You are a helpful assistant"
- Include the creator/org if it matters for trust or brand
- Do NOT write a paragraph. The model doesn't need a backstory

**Example:**
```
You are Billie, Acme Corp's billing support agent. You help customers understand their invoices, update payment methods, and resolve billing disputes.
```

### Layer 2: Context (3-10 lines)

Environmental facts the model needs to operate correctly. This is NOT instructions — it's ground truth.

**Include:**
- Knowledge cutoff date
- Current date (if injected dynamically)
- Available tools and capabilities
- Deployment surface (web chat, API, CLI, mobile)
- User context (if known: role, permissions, subscription tier)

**Rules:**
- State facts, not instructions. "Your knowledge cutoff is March 2025" — not "Remember that your knowledge has a cutoff."
- Inject dynamic values at runtime rather than hardcoding: `Current date: {{current_date}}`
- Keep this section factual and scannable — bullet points or key-value pairs

**Example:**
```
- Knowledge cutoff: March 2025
- Current date: {{current_date}}
- Environment: Web chat widget on acme.com/support
- User tier: {{user_tier}} (free, pro, or enterprise)
- Available tools: search_knowledge_base, create_ticket, lookup_invoice
```

### Layer 3: Behavioral Rules (10-30 lines)

The core of the prompt. Defines what the model should and should not do.

**Structure these as:**
1. Primary directives (what to always do)
2. Constraints (what to never do, scoped)
3. Edge case handling (what to do when unsure)

**Rules:**
- One instruction per line or bullet
- Pair every DO with a DON'T where the failure mode is non-obvious
- Scope constraints: "Do not discuss competitors' pricing. You may acknowledge competitors exist if asked directly."
- Use meta-rules for broad categories instead of enumerating every case
- Put the most critical rules first — models pay more attention to early instructions

**Example:**
```
## Rules

- Answer billing questions using the knowledge base. Search before answering — do not guess.
- If a question is outside billing scope, say: "I can help with billing questions. For [topic], please contact [channel]."
- Never share internal pricing formulas, margin data, or cost breakdowns.
- You may offer refunds up to $50 without escalation. Above $50, create a ticket for the billing team.
- If the customer is frustrated, acknowledge their frustration before solving the problem. Do NOT use scripted empathy phrases like "I understand your frustration" — be genuine and specific.
- When uncertain about a policy, say so and offer to escalate. Do NOT make up policies.
```

### Layer 4: Tool Definitions (varies)

How the model should use its available tools/functions.

**Rules:**
- Separate tool schemas from usage guidance
- Include "when to use" and "when NOT to use" for each tool
- Specify required vs optional parameters
- Show example invocations for complex tools
- If tools can be called in parallel, say so explicitly

**Structure:**
```
## Tools

### search_knowledge_base
Search Acme's billing knowledge base for policy information.
- Use BEFORE answering any policy question
- Do NOT use for general conversation or greetings
- Parameters: query (required), category (optional: "refunds", "payments", "invoices")

### create_ticket
Escalate an issue to the human billing team.
- Use when: refund > $50, account access issues, disputed charges
- Do NOT use for: simple questions, password resets (direct to /reset)
- Parameters: subject (required), priority (required: low/medium/high), description (required)
```

### Layer 5: Domain Instructions (varies)

Topic-specific guidance that applies only to certain types of requests. This layer grows as you handle more use cases.

**Rules:**
- Group by topic or request type
- Use XML tags or clear headers to separate domains
- Only include domains relevant to this model's scope
- This is where you put the detailed "how to handle X" instructions

**Example:**
```
<refund_handling>
- Eligible: subscription charges within 30 days, duplicate charges, service outages
- Not eligible: one-time purchases after 14 days, usage-based overages
- Process: verify charge in system → confirm eligibility → apply refund or escalate
- Always confirm the refund amount with the customer before processing
</refund_handling>

<payment_updates>
- Accept: credit card, debit card, ACH (enterprise only)
- Never ask for full card numbers in chat — direct to the secure payment portal
- If payment fails, check: expired card, insufficient funds, bank block
</payment_updates>
```

### Layer 6: Output Format (3-10 lines)

How the model should format its responses.

**Rules:**
- Be explicit about format expectations
- Show an example of the exact output structure if it's non-obvious
- Specify what to include AND what to omit
- If format varies by situation, provide routing rules

**Example:**
```
## Output Format

- Use plain text for conversational responses. No markdown headers in chat.
- Use bullet points only for lists of 3+ items.
- Keep responses under 150 words for simple questions, under 300 for complex ones.
- When providing invoice details, use this format:
  Invoice #[number] | Date: [date] | Amount: $[amount] | Status: [paid/unpaid/refunded]
- Never end with "Is there anything else I can help with?" — only ask follow-up questions if you genuinely need more information.
```

### Layer 7: Examples (optional, 2-5 examples max)

Concrete demonstrations of desired behavior. Use sparingly.

**Rules:**
- Only include examples when the desired behavior is ambiguous or counterintuitive
- Place examples near the rule they demonstrate, OR in a dedicated section at the end
- Show both good and bad examples for maximum clarity
- 1-2 examples is usually sufficient. 5 is the absolute maximum — beyond that, compress into rules
- Use realistic examples, not trivial ones

**Example:**
```
## Examples

<example>
User: I was charged twice for my subscription this month.
Assistant: I can see the duplicate charge. Let me look into that right away.

[searches knowledge base for "duplicate charge resolution"]
[looks up user's recent invoices]

I found two charges of $29.99 on your account from June 1st and June 3rd. The June 3rd charge appears to be a duplicate. I'll process a refund for $29.99 — you should see it within 3-5 business days. Is the card ending in 4242 the right one for the refund?
</example>

<example>
User: Can you give me a discount?
Assistant: I appreciate you asking! I'm not able to offer ad-hoc discounts, but I can share what's available: we have an annual billing option that saves 20%, and enterprise plans with custom pricing for teams of 10+. Would either of those be relevant for you?
</example>
```

---

## Layer Ordering Principles

1. **Critical instructions degrade with distance from the top.** Put your most important rules in Layers 1-3.
2. **Context before rules.** The model needs to know its environment before it can follow instructions correctly.
3. **General before specific.** Broad behavioral rules before domain-specific handling.
4. **Tools near their usage instructions.** If tool definitions are injected separately (as in function calling), keep usage guidance near the behavioral rules.
5. **Examples last.** They're reference material, not instructions. Putting them early pushes real instructions further from the model's attention.

## When to Deviate

- **Short prompts (<50 lines):** Collapse layers. Identity + Rules + Format is sufficient. No need for 7 distinct sections.
- **Pipeline prompts (model-to-model):** Skip Identity and Tone. Focus on Input Format → Processing Rules → Output Format.
- **Single-task prompts:** Skip Domain Instructions. The entire prompt IS the domain instruction.

---

## Assembly Checklist

Before finalizing a system prompt, verify:

- [ ] Identity is one sentence, specific to this use case
- [ ] Context includes knowledge cutoff and relevant environmental facts
- [ ] Behavioral rules are scoped (not absolute bans) and non-conflicting
- [ ] Tools have "when to use" and "when NOT to use" guidance
- [ ] Domain instructions are grouped by topic
- [ ] Output format is explicit with an example
- [ ] Examples (if any) demonstrate ambiguous behaviors, not obvious ones
- [ ] Most critical instructions are in the first 30% of the prompt
- [ ] The prompt reads top-to-bottom without requiring the reader to jump around
