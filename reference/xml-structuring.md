# XML Structuring for Prompts

XML tags provide machine-parseable section boundaries in prompts. They are especially effective with Claude, which was trained to attend to XML structure.

---

## When to Use XML Tags

| Situation | Recommendation |
|---|---|
| Prompt has 5+ distinct sections | Use XML tags |
| Prompt mixes context, rules, and examples | Use XML tags to separate them |
| Prompt is for Claude specifically | Use XML tags — Claude attends to them natively |
| Prompt is under 20 lines | Use markdown headers or plain text instead |
| Prompt is for a model that ignores XML (rare) | Use markdown headers |
| Prompt has deeply nested structure (3+ levels) | Use XML for outer structure, markdown inside |

## Naming Conventions

**Use lowercase, descriptive, hyphenated tag names:**

```xml
<!-- Good -->
<instructions>
<system-context>
<user-profile>
<tool-definitions>
<output-format>
<few-shot-examples>

<!-- Bad -->
<Instructions>          <!-- inconsistent casing -->
<sys_ctx>               <!-- cryptic abbreviation -->
<IMPORTANT_RULES>       <!-- ALL CAPS is noise -->
<section1>              <!-- meaningless name -->
```

**Tag names should describe content, not importance.** Use CRITICAL/IMPORTANT markers inside sections for priority, not in the tag name.

## Core Patterns

### Pattern 1: Section Wrapper

The most common pattern. Each major section of the prompt gets its own tag.

```xml
<identity>
You are a research assistant specializing in climate science.
</identity>

<context>
- Knowledge cutoff: March 2025
- Available tools: search_papers, summarize_paper
- User: Graduate researcher
</context>

<rules>
- Always cite papers by DOI when available
- Search before answering factual questions
- If a claim is contested in the literature, present both sides
</rules>

<output-format>
Use markdown. Cite sources as footnotes: [^1], [^2], etc.
</output-format>
```

### Pattern 2: Conditional Blocks

Wrap instructions that apply only in certain conditions.

```xml
<when-user-is-free-tier>
- Limit responses to 500 words
- Do not access premium data sources
- Suggest upgrade for advanced queries
</when-user-is-free-tier>

<when-user-is-pro>
- No response length limit
- Access all data sources
- Provide detailed analysis with citations
</when-user-is-pro>
```

### Pattern 3: Example Pairs

Wrap input/output examples for clear delineation.

```xml
<examples>
<example>
<user>What's the current consensus on sea level rise projections?</user>
<assistant>Based on IPCC AR6 (2021), the central estimate for global mean sea level rise by 2100 ranges from 0.28m (SSP1-1.9) to 1.01m (SSP5-8.5) relative to 1995-2014 levels.[^1]

Recent studies suggest these projections may underestimate contributions from ice sheet dynamics.[^2]

[^1]: IPCC AR6 WG1, Chapter 9
[^2]: DeConto et al., 2021, doi:10.1038/s41586-021-03302-y</assistant>
</example>
</examples>
```

### Pattern 4: Tool Description Block

Isolate tool definitions from prose instructions.

```xml
<tools>
<tool name="search_papers">
Search academic databases for papers matching a query.
Parameters:
  - query (string, required): Search terms
  - year_range (string, optional): e.g., "2020-2025"
  - field (string, optional): e.g., "climate science"
Use when: User asks a factual question about research findings.
Do NOT use when: User asks about methodology or opinion.
</tool>
</tools>
```

### Pattern 5: Priority Markers Inside Tags

Use inline markers for emphasis within sections — not in tag names.

```xml
<rules>
CRITICAL: Never fabricate citations. If you cannot find a source, say so explicitly.

- Search the database before making any factual claim
- Present uncertainty ranges when available
- Use hedging language for preliminary findings ("suggests", "indicates")

IMPORTANT: When two sources conflict, present both and note the disagreement. Do not silently pick one.
</rules>
```

## Nesting Guidelines

- **Maximum 2 levels of nesting** for readability: `<rules><refund-policy>...</refund-policy></rules>`
- **Use markdown inside XML** for formatting within sections (lists, bold, code blocks)
- **Don't nest XML for the sake of nesting** — flat structure is almost always better
- **Close all tags** — unclosed tags can confuse models about section boundaries

## XML vs Markdown vs Plain Text

| Feature | XML Tags | Markdown Headers | Plain Text |
|---|---|---|---|
| Section boundaries | Unambiguous (opening + closing tag) | Ambiguous (where does a section end?) | None |
| Model attention | Claude: strong. Others: moderate | All models: moderate | All models: relies on proximity |
| Nesting | Clean nesting support | Nesting via header levels (##, ###) | No nesting |
| Readability | Slightly more verbose | Clean and familiar | Most compact |
| Best for | Complex multi-section prompts | Medium-complexity prompts | Short, single-purpose prompts |

## Common Mistakes

| Mistake | Problem | Fix |
|---|---|---|
| Using XML for a 10-line prompt | Overhead exceeds value | Use plain text or markdown |
| `<VERY_IMPORTANT_SECTION>` | Tag name is screaming, not describing | `<rules>` with CRITICAL marker inside |
| 4+ levels of nesting | Unreadable, models lose track | Flatten to 2 levels max |
| Mixing XML and markdown headers for the same purpose | Inconsistent, confusing | Pick one system and use it throughout |
| Forgetting closing tags | Model treats everything after as part of the section | Always close tags |
| `<instructions>` wrapping the entire prompt | Redundant — the entire prompt IS instructions | Use tags for subsections, not the whole prompt |
