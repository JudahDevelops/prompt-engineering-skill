# Prompt Engineering Skill for Claude Code

A comprehensive Claude Code skill for designing, analyzing, optimizing, debugging, and evaluating prompts — with deep expertise in system prompts, CLAUDE.md files, agent instructions, and AI workflow prompts.

## Install

Copy the `prompt-engineering` directory into your Claude Code skills folder:

```bash
cp -r prompt-engineering/ ~/.claude/skills/prompt-engineering/
```

The skill activates automatically when you ask Claude Code to work with prompts, system instructions, CLAUDE.md files, or agent configurations.

## What It Does

| Capability | Description |
|---|---|
| **Create** | Build production-ready system prompts, agent instructions, and AI workflow prompts from requirements |
| **Optimize** | Improve existing prompts — reduce ambiguity, remove redundancy, compress tokens, increase reliability |
| **Review** | Score any prompt across 8 quality dimensions with structured critique and rewrite |
| **CLAUDE.md** | Write and optimize project instruction files for AI-assisted development |
| **Debug** | Diagnose why prompts fail — find ambiguity, conflicts, gaps, and hallucination risks |
| **Templates** | Get reusable prompt skeletons for common use cases |

## Usage Examples

```
Create a system prompt for a customer support agent that handles billing
questions, can look up invoices, and escalates refunds over $50.
```

```
Review this prompt and tell me what's wrong:
[paste your prompt]
```

```
Optimize my CLAUDE.md — it's too long and has stale commands.
```

```
My research assistant keeps hallucinating citations. Here's the prompt:
[paste your prompt]
```

```
Give me a template for a multi-agent pipeline with a coordinator and 3 workers.
```

## Skill Structure

```
prompt-engineering/
├── SKILL.md              # Core skill — workflows, routing, principles
├── reference/            # Deep-dive guides (loaded on demand)
│   ├── system-prompt-architecture.md
│   ├── xml-structuring.md
│   ├── anti-patterns.md
│   ├── constraint-design.md
│   ├── claude-md-guide.md
│   ├── evaluation-framework.md
│   ├── multi-agent-prompts.md
│   └── token-optimization.md
├── examples/             # Before/after examples and templates
│   ├── before-after-system-prompts.md
│   ├── before-after-task-prompts.md
│   ├── claude-md-examples.md
│   └── templates.md
└── checklists/           # Step-by-step review checklists
    ├── prompt-review-checklist.md
    ├── system-prompt-checklist.md
    └── claude-md-checklist.md
```

The skill uses **progressive loading**: the core SKILL.md stays in context, while reference files, examples, and checklists are loaded on demand when a specific workflow needs them.

## Design Principles

This skill was built by analyzing:

- **289 leaked system prompts** across 13 AI vendors — extracting universal design patterns, constraint techniques, and anti-hallucination methods
- **Anthropic's official skills library** (17 skills) — following their architectural patterns, frontmatter conventions, and progressive loading model
- **Best-in-class community skills** — adopting data-driven documentation, anti-pattern pairing, and machine-readable formatting

Key principles applied:

1. **Meta-rules over enumeration** — one principle that generalizes beats a list of 50 specific rules
2. **Scoped constraints** — "Do not X when Y. You may X when Z." instead of "NEVER X"
3. **Anti-pattern pairing** — every "Do this" is paired with "Don't do this" and "because"
4. **Show, don't tell** — concrete examples over abstract descriptions
5. **Progressive disclosure** — simple tasks short-circuit; complex tasks go deeper

## License

MIT
