# Token Optimization

Techniques for reducing token count while preserving — or improving — prompt quality.

---

## The Core Principle

Token optimization is NOT about making prompts shorter. It's about making every token earn its place. A 500-token prompt that's clear and complete beats a 200-token prompt that's ambiguous.

**Optimize for clarity first, then compress.**

---

## Compression Techniques

### 1. Cut Filler Phrases

These phrases add tokens but zero meaning:

| Filler | Replacement |
|---|---|
| "It is very important that you..." | [just state the instruction] |
| "Please make sure to always..." | "Always..." |
| "In your capacity as an AI assistant..." | [cut entirely] |
| "You should try to..." | "Do..." |
| "When responding to the user, you should..." | [cut — it's implicit] |
| "Please note that..." | [cut — just state the thing] |
| "It's worth mentioning that..." | [cut] |
| "Keep in mind that..." | [cut — state the rule directly] |
| "As mentioned earlier..." | [cut — state the rule again if needed, or don't] |
| "In order to..." | "To..." |

### 2. Eliminate Redundancy

**Before (3 instructions, same meaning):**
```
- Be concise in your responses
- Keep your answers brief and to the point
- Avoid writing unnecessarily long responses
```

**After (1 instruction, precise):**
```
- Keep responses under 150 words unless the user asks for detail.
```

**Technique:** After writing a prompt, search for instructions that overlap. Merge them into the most specific version.

### 3. Replace Descriptions with Examples

**Before (35 tokens):**
```
Format dates in a way that includes the full month name, followed by the day number, followed by a comma, followed by the four-digit year.
```

**After (12 tokens):**
```
Format dates as: January 15, 2025
```

An example is often more precise and more compact than a description.

### 4. Use Tables Instead of Paragraphs

**Before (paragraph):**
```
When the user asks about billing, direct them to the billing page. When they ask about technical support, direct them to the support portal. When they ask about sales, direct them to the sales team. When they ask about partnerships, direct them to the partnerships email.
```

**After (table):**
```
| Topic | Direct to |
|---|---|
| Billing | /billing |
| Technical support | /support |
| Sales | sales@acme.com |
| Partnerships | partners@acme.com |
```

Tables are denser, scannable, and extend easily.

### 5. Consolidate Conditional Logic

**Before (scattered):**
```
If the user is on the free tier, limit responses to 100 words.
[50 lines of other instructions]
If the user is on the free tier, do not access premium features.
[30 more lines]
If the user is on the free tier, suggest upgrading for advanced queries.
```

**After (grouped):**
```
<free-tier-rules>
- Limit responses to 100 words
- Do not access premium features
- Suggest upgrading for advanced queries
</free-tier-rules>
```

Grouping conditional instructions prevents scatter and makes maintenance easier.

### 6. Parameterize Dynamic Values

**Before (hardcoded):**
```
Today is July 8, 2026. Your knowledge was last updated in March 2025.
```

**After (parameterized):**
```
Current date: {{current_date}}
Knowledge cutoff: {{knowledge_cutoff}}
```

Parameterized values don't save tokens at inference time, but they save maintenance effort and reduce staleness risk.

---

## When Compression Hurts

Not all compression is good. These situations justify extra tokens:

| Situation | Why more tokens help |
|---|---|
| Ambiguous behavior | An extra sentence of clarification prevents misinterpretation |
| Safety constraints | Verbose safety rules are cheaper than safety failures |
| Novel/counterintuitive behavior | The model needs explicit instruction to override its defaults |
| Multi-step processes | Compressing steps into one instruction causes skipped steps |
| Examples for complex output formats | One example is worth 50 words of format description |

**Rule of thumb:** If compressing an instruction would require the model to infer your intent, keep the longer version.

---

## Cost/Quality Tradeoff Analysis

| Token Budget | Approach |
|---|---|
| Under 200 tokens | Single-purpose prompt. Identity + 3-5 rules + format. No examples. |
| 200-500 tokens | Standard prompt. Identity, context, rules, format. 1 example max. |
| 500-1000 tokens | Full prompt. All 7 layers. 2-3 examples. Domain-specific sections. |
| 1000-2000 tokens | Complex agent. Multiple domains, tool definitions, extensive edge case handling. |
| Over 2000 tokens | Consider splitting: core prompt + retrieval (RAG) for domain knowledge. |

---

## Optimization Workflow

1. **Write the full prompt first** — don't optimize prematurely
2. **Score readability** — does it make sense to a first-time reader?
3. **Highlight redundancy** — mark instructions that say the same thing
4. **Merge duplicates** — keep the most specific version
5. **Cut filler** — remove every phrase from the filler list above
6. **Replace descriptions with examples** where possible
7. **Group conditionals** — consolidate scattered if/then rules
8. **Verify nothing was lost** — re-read and confirm no instruction was accidentally cut
9. **Compare token counts** — report the reduction

CRITICAL: After optimizing, re-read the prompt as if seeing it for the first time. If any instruction is now unclear, the compression went too far. Restore clarity.
