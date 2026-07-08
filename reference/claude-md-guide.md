# CLAUDE.md Guide

How to write effective CLAUDE.md files — project instruction files that give Claude (and other AI coding assistants) the context they need to work effectively in a repository.

---

## What Is a CLAUDE.md?

A CLAUDE.md is a project-level instruction file that lives at the root of a repository. It provides context that a new contributor (human or AI) couldn't figure out from the code alone: conventions, commands, architecture, gotchas, and workflows.

Think of it as onboarding documentation optimized for AI consumption.

---

## What Belongs in a CLAUDE.md

| Include | Why |
|---|---|
| Build/test/run commands | So the AI can verify its work |
| Project structure overview | So the AI knows where things live |
| Coding conventions specific to this project | So the AI matches existing patterns |
| Common pitfalls | Things that have caused bugs before |
| Testing requirements | What tests to run, how to run them |
| Environment setup notes | Non-obvious configuration steps |
| Deployment notes | If the AI might help with deploy-related tasks |

## What Does NOT Belong

| Exclude | Why |
|---|---|
| Generic coding advice | The AI already knows to write clean code |
| Language tutorials | The AI knows the language better than any tutorial |
| Copy of the README | Redundant — the AI can read the README |
| Exhaustive API documentation | Too long — use RAG or link to docs |
| Personal preferences not shared by the team | Confuses the AI about what's convention vs preference |
| Aspirational standards the code doesn't follow | "Use functional programming" when the codebase is OOP causes conflicts |

---

## Recommended Structure

```markdown
# Project Name

## Quick Start

Build: `npm run build`
Test: `npm test`
Lint: `npm run lint`
Dev server: `npm run dev` (runs on port 3000)

## Architecture

- `src/api/` — Express API routes
- `src/services/` — Business logic (no HTTP concerns)
- `src/models/` — Database models (Prisma)
- `src/utils/` — Shared utilities
- `tests/` — Mirrors src/ structure

## Conventions

- Use named exports, not default exports
- Database columns use snake_case (matches PostgreSQL defaults)
- API responses use camelCase (matches frontend expectations)
- Error handling: throw AppError instances, never raw strings
- Imports: external packages first, then internal modules, separated by blank line

## Common Pitfalls

- Never import from `src/legacy/` — it's deprecated and will be removed
- The `user.email` field is nullable in the database despite the TypeScript type saying otherwise (migration pending)
- Tests that touch the database must use the `setupTestDb` helper, not raw Prisma

## Testing

Run all tests: `npm test`
Run single file: `npm test -- path/to/test.ts`
Watch mode: `npm test -- --watch`

Tests require a running PostgreSQL instance. Use `docker compose up db` to start one.

Coverage: aim for 80%+ on new code. Run `npm run coverage` to check.

## Git Workflow

- Branch from `main`
- Prefix branches: `feat/`, `fix/`, `refactor/`, `docs/`
- Squash merge to main
- CI must pass before merge
```

---

## Writing Principles

### 1. Commands Over Descriptions

**Bad:** "You can run the test suite using the testing framework."
**Good:** `npm test`

Commands are unambiguous, copy-pasteable, and verifiable. Descriptions invite interpretation.

### 2. Conventions With Rationale

**Bad:** "Use snake_case for database columns."
**Good:** "Database columns use snake_case (matches PostgreSQL defaults and avoids quoting issues)."

Rationale helps the AI (and humans) make correct judgment calls in situations the convention doesn't explicitly cover.

### 3. Project-Specific Only

Ask yourself: "Would an experienced developer already know this?" If yes, cut it.

- "Functions should have clear names" — cut (obvious)
- "The `calculateTax` function expects prices in cents, not dollars" — keep (project-specific)

### 4. Current State, Not Aspirational State

The CLAUDE.md should describe how the code IS, not how you wish it were. If the codebase uses classes but you want to migrate to functions, don't put "Use functional programming" in the CLAUDE.md. Instead: "New code should prefer pure functions. Existing class-based code is being migrated incrementally — don't refactor classes unless that's the explicit task."

### 5. Scannable Format

Use headers, bullet points, and code blocks. The AI (and humans) will scan this document, not read it linearly. Make every section independently useful.

---

## Hierarchical CLAUDE.md Files

For monorepos or large projects, you can place CLAUDE.md files at multiple levels:

```
/CLAUDE.md                    # Repo-wide conventions
/packages/api/CLAUDE.md       # API-specific instructions
/packages/frontend/CLAUDE.md  # Frontend-specific instructions
```

**Resolution order:** More specific files take precedence over general ones. If the root CLAUDE.md says "Use tabs" but `packages/frontend/CLAUDE.md` says "Use spaces," the frontend file wins when working in that directory.

State this explicitly if using hierarchical files:

```markdown
Note: This file contains conventions specific to the API package.
For repo-wide conventions, see the root CLAUDE.md.
If there's a conflict, this file takes precedence.
```

---

## CLAUDE.md Optimization Checklist

When reviewing or improving a CLAUDE.md:

- [ ] Every command is copy-pasteable (no pseudocode or placeholders)
- [ ] Architecture section matches the actual directory structure
- [ ] Conventions reflect how the code actually is, not aspirations
- [ ] No generic advice an experienced developer would already know
- [ ] Pitfalls are real things that have caused actual problems
- [ ] Testing section includes how to run a single test (not just "all tests")
- [ ] No duplicated information from README or other docs
- [ ] Under 200 lines (longer files lose AI attention)
- [ ] Every section is independently useful (can be scanned, not just read linearly)
- [ ] Hardcoded values (ports, URLs) are accurate and current

---

## Common Mistakes

| Mistake | Impact | Fix |
|---|---|---|
| Too long (500+ lines) | AI loses focus on critical instructions | Cut to essentials, move details to other docs |
| Duplicates README content | Wastes tokens, risks staleness when one is updated | Reference the README, don't copy it |
| Generic advice | Wastes tokens, no behavioral impact | Remove anything an experienced dev already knows |
| Stale commands | AI runs wrong commands, gets errors | Verify every command works in a fresh checkout |
| Missing test commands | AI can't verify its own work | Always include exact test run commands |
| Aspirational conventions | AI writes code that doesn't match existing patterns | Describe current state, note migrations explicitly |
| No architecture section | AI puts files in wrong locations | Brief directory map with purpose of each top-level dir |
