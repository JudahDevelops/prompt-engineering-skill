# CLAUDE.md Quality Checklist

Checklist for evaluating and improving CLAUDE.md project instruction files.

---

## Content Quality

- [ ] Every line is project-specific (not generic advice any developer knows)
- [ ] Conventions reflect how the code actually is, not aspirations
- [ ] Pitfalls describe real problems that have actually occurred
- [ ] No duplicated content from README or other docs

## Commands

- [ ] All commands are copy-pasteable (no pseudocode or placeholders)
- [ ] Build, test, lint, and run commands are included
- [ ] Single-file test command is included (most common action)
- [ ] All commands have been verified to work in a fresh checkout

## Architecture

- [ ] Directory structure overview is present
- [ ] Each top-level directory has a one-line purpose description
- [ ] Structure matches the actual codebase (not outdated)

## Conventions

- [ ] Naming conventions are specified with rationale
- [ ] Import/module organization patterns are documented
- [ ] Error handling patterns are documented
- [ ] Conventions that differ from language/framework defaults are called out

## Practicality

- [ ] Under 200 lines total
- [ ] Every section is independently useful (scannable, not sequential)
- [ ] No hardcoded values that will go stale (ports, URLs, versions)
- [ ] A developer seeing this for the first time could start contributing

---

## Quick Fixes

| If this fails... | Quick fix |
|---|---|
| Generic advice | Delete lines an experienced dev already knows |
| Stale commands | Run each command and update |
| Missing architecture | `ls` the top-level dirs, write one line per dir |
| Aspirational conventions | Rewrite to describe current state; note migrations separately |
| Too long | Move detailed guides to separate docs; keep CLAUDE.md as an index |
