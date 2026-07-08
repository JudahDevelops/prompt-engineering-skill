# Prompt Engineering Skill for Claude Code

A Claude Code skill focused on **optimizing codebases for AI-assisted development**. It analyzes your project and produces optimized CLAUDE.md files with project maps — so Claude can navigate and contribute without wasting thousands of tokens on exploration.

Also covers system prompt creation, prompt optimization, review, debugging, and evaluation.

## Install

Copy the `prompt-engineering-skill` directory into your Claude Code skills folder:

```bash
cp -r prompt-engineering-skill/ ~/.claude/skills/prompt-engineering/
```

The skill activates automatically when you ask Claude Code to optimize a codebase, create a CLAUDE.md, generate a project map, or work with any kind of prompt.

## What It Does

| Capability | Description |
|---|---|
| **Codebase Optimize** | Analyze a project, build a project map, and produce a token-efficient CLAUDE.md so Claude stops wasting tokens exploring. **Primary workflow.** |
| **Create** | Build production-ready system prompts, agent instructions, and AI workflow prompts |
| **Prompt Optimize** | Improve existing prompts — reduce ambiguity, remove redundancy, compress tokens |
| **Review** | Score any prompt across 8 quality dimensions with structured critique and rewrite |
| **Debug** | Diagnose why prompts fail — find ambiguity, conflicts, gaps, and hallucination risks |
| **Templates** | Get reusable prompt skeletons for common use cases |

## Usage Examples

```
Analyze this codebase and create an optimized CLAUDE.md with a project map.
```

```
Optimize my CLAUDE.md — it's too long and Claude keeps exploring the codebase anyway.
```

```
Create a project map for this repo so Claude knows where everything is.
```

```
Review this system prompt and tell me what's wrong:
[paste your prompt]
```

```
My agent keeps hallucinating. Here's the prompt — debug it.
```

## Skill Structure

```
prompt-engineering-skill/
├── SKILL.md              # Core skill — workflows, routing, principles
├── reference/            # Deep-dive guides (loaded on demand)
│   ├── codebase-optimization.md
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
