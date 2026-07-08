# Before & After: Task Prompts

Task-level prompts (single-turn instructions, pipeline steps, data processing) — originals with issues and optimized versions.

---

## Example 1: Data Extraction

### Before

```
Extract the relevant information from this document and put it into a
structured format. Make sure to capture all the important details and organize
them in a logical way. The output should be easy to read and understand.
```

### Issues

- "Relevant information" — relevant to what? Undefined.
- "Structured format" — which structure? JSON? Table? Bullet list?
- "Important details" — important by what criteria?
- "Logical way" — whose logic?
- "Easy to read" — subjective.

### After

```
Extract the following fields from the attached invoice:

- vendor_name (string)
- invoice_number (string)
- invoice_date (YYYY-MM-DD)
- line_items (array of {description, quantity, unit_price, total})
- subtotal (number)
- tax (number)
- total (number)

Output as JSON. If a field is missing from the document, set its value to null.
Do not infer or calculate missing values — extract only what is explicitly stated.

Example output:
{
  "vendor_name": "Acme Corp",
  "invoice_number": "INV-2025-001",
  "invoice_date": "2025-03-15",
  "line_items": [
    {"description": "Widget A", "quantity": 10, "unit_price": 5.00, "total": 50.00}
  ],
  "subtotal": 50.00,
  "tax": 4.50,
  "total": 54.50
}
```

**What changed:** Specified exact fields, exact format, exact handling of missing data, and provided an example. Zero ambiguity.

---

## Example 2: Content Summarization

### Before

```
Summarize this article. Keep it short and capture the key points. Make it
suitable for sharing on social media. Use an engaging tone.
```

### Issues

- "Short" — 50 words? 200 words? A tweet?
- "Key points" — how many? Selected by what criteria?
- "Social media" — which platform? Twitter has 280 chars, LinkedIn allows 3000.
- "Engaging tone" — vague.

### After

```
Summarize this article for a LinkedIn post.

Requirements:
- Length: 150-200 words
- Structure: Opening hook (1 sentence) → 3 key takeaways as bullets → Call to action
- Tone: Professional but conversational. No jargon. Write as a peer sharing an insight, not a marketer selling.
- Include 3-5 relevant hashtags at the end

Do NOT:
- Start with "I just read an interesting article..."
- Use clickbait phrasing ("You won't believe...")
- Add information not in the original article
```

**What changed:** Specified platform, word count, structure, tone with anti-patterns, and exact format.

---

## Example 3: Code Review Instruction

### Before

```
Review this code and provide feedback. Check for bugs, performance issues,
and best practices. Be thorough.
```

### Issues

- "Be thorough" — does that mean check every line, or focus on high-impact issues?
- No priority ordering — are bugs and style issues equally important?
- No output format — will feedback be actionable?
- "Best practices" — whose? The team's or the language's?

### After

```
Review this code for issues in this priority order:

1. BUGS: Logic errors, off-by-one, null safety, race conditions
2. SECURITY: Injection, auth bypass, data exposure
3. PERFORMANCE: O(n²) where O(n) is possible, unnecessary allocations, missing indexes
4. READABILITY: Only if something is genuinely confusing — do not nitpick style

For each issue found, provide:
- File and line number
- What's wrong (1 sentence)
- Suggested fix (code snippet)

Ignore: formatting, naming conventions, import ordering — the linter handles those.

If you find no issues, say "No issues found" — do not invent problems to appear thorough.
```

**What changed:** Priority ordering, specific categories, output format per issue, explicit exclusions, and permission to say "no issues."

---

## Example 4: Translation with Context

### Before

```
Translate this text to French. Make sure the translation is accurate and
natural-sounding. Preserve the meaning and tone of the original.
```

### Issues

- No domain context — legal French is different from casual French
- "Accurate and natural-sounding" — these sometimes conflict; which wins?
- No guidance on idioms, cultural references, or formatting
- No indication of audience

### After

```
Translate this marketing email from English to French (France, not Canadian French).

Guidelines:
- Target audience: B2B SaaS decision-makers
- Register: Professional but approachable (vous, not tu)
- Adapt idioms to French equivalents rather than translating literally
- Keep brand names, product names, and technical terms in English
- Preserve paragraph structure and formatting
- If a cultural reference won't land in France, replace with an equivalent that does — note the change in a comment

Output format:
[translated text]

<!-- Translation notes:
- [any adaptations or choices made, with reasoning]
-->
```

**What changed:** Specified French variant, audience, register, handling of idioms and brand names, and a mechanism for surfacing translation decisions.

---

## Key Patterns

### 1. Replace Adjectives with Specifications

| Vague | Specific |
|---|---|
| "short" | "150-200 words" |
| "structured" | "JSON with these fields: ..." |
| "engaging" | "Opening hook → bullets → call to action" |
| "thorough" | "Check for: bugs, security, performance (in that priority)" |
| "appropriate" | "Professional register, vous form, no slang" |

### 2. Define the Output Format Before the Task

When the model knows the exact output shape, it structures its work accordingly. Stating format last often results in the model backfitting its answer into the format — losing information or padding.

### 3. Include Anti-Patterns

Telling the model what NOT to do is often more impactful than telling it what to do, because it prevents the most common default behaviors:

- "Do NOT start with 'I just read...'"
- "Do NOT invent problems to appear thorough"
- "Do NOT infer missing values — use null"

### 4. Permission to Say Nothing

Models default to producing output. Explicitly permitting "no issues found" or "I don't have this information" prevents fabrication.

### 5. One Instruction Per Concern

"Make it short, engaging, and suitable for social media" is three separate requirements that each need their own specification. Separate them.
