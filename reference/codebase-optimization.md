# Codebase Optimization for AI-Assisted Development

How to analyze a codebase and produce artifacts (CLAUDE.md, project maps, directory indexes) that minimize token waste and maximize Claude's effectiveness.

---

## The Problem

Without optimization, Claude wastes tokens on every session:

| Activity | Token cost | Frequency |
|---|---|---|
| Globbing to find file structure | 500-2,000 | Every session |
| Grepping to understand patterns | 1,000-5,000 | Every session |
| Reading wrong files before finding the right ones | 2,000-8,000 | Every session |
| Re-discovering conventions by reading existing code | 1,000-3,000 | Every session |
| Asking clarifying questions about the project | 500-1,000 | Every session |

**Total waste per session: 5,000-19,000 tokens.**

A well-structured CLAUDE.md with a project map eliminates most of this. Claude reads the CLAUDE.md once (~200-500 tokens) and immediately knows where everything is, what patterns to follow, and what commands to run.

---

## The Project Map

The project map is the highest-value artifact. It tells Claude exactly where to look without exploring.

### What to Include

```markdown
## Project Map

### Directory Structure
- `src/api/` — REST API routes (Express). Entry: `src/api/index.ts`
- `src/services/` — Business logic. One service per domain entity
- `src/models/` — Prisma models. Schema: `prisma/schema.prisma`
- `src/utils/` — Shared helpers: `errors.ts`, `validators.ts`, `logger.ts`
- `src/middleware/` — Express middleware: auth, error handling, rate limiting
- `src/jobs/` — Background workers (Bull queues). One file per job type
- `tests/` — Mirrors `src/` structure. Fixtures in `tests/fixtures/`
- `scripts/` — One-off scripts. Not part of the application
- `docs/` — Internal documentation (not user-facing)

### Data Flow
HTTP Request → middleware → route handler → service → model → database

### Key Files (read these first)
- `src/api/index.ts` — All routes registered here, middleware stack visible
- `src/services/OrderService.ts` — Most complex service, payment flow
- `prisma/schema.prisma` — Complete database schema
- `src/config.ts` — All env vars with defaults and validation
- `.github/workflows/ci.yml` — CI pipeline, shows test/lint/build steps
```

### What NOT to Include

| Skip | Why |
|---|---|
| `node_modules/`, `.git/`, `dist/`, `build/` | Obvious, never need mapping |
| Every file in a directory | Map directories, not files. List only key files |
| File contents or implementations | The map is a navigation aid, not documentation |
| Generated or compiled files | Change with every build, always stale |
| Test files individually | "Mirrors `src/` structure" is sufficient |

### Depth Rules

| Project size | Mapping depth |
|---|---|
| Small (<20 files) | Map every file |
| Medium (20-200 files) | Map directories + key files |
| Large (200-2000 files) | Map top 2 levels + entry points + key files per module |
| Monorepo | Map packages/workspaces at top level, then key files within each |

---

## Scanning Strategies

### Strategy 1: Outside-In (Recommended)

Start from the project root and work inward. Best for unfamiliar codebases.

1. **Root config** — Read `package.json` / `pyproject.toml` / `Cargo.toml` / `go.mod`. Identify language, framework, dependencies, and scripts.
2. **Directory listing** — List top-level directories. Categorize each (source, config, tests, docs, scripts, generated).
3. **Entry points** — Find and read `main`, `index`, `app`, `server` files. Trace the startup path.
4. **One representative file per directory** — Read the most important file in each source directory to understand patterns.
5. **Config and CI** — Read `.env.example`, CI config. Understand environment and deployment.

### Strategy 2: Dependency-Driven

Follow the import graph. Best for understanding data flow in complex projects.

1. **Entry point** — Find the main entry point.
2. **First-level imports** — What does the entry point import? Read those files.
3. **Repeat one level deeper** — For each imported module, what does it import?
4. **Stop at utilities** — When you reach utility/helper files, stop. These are leaves.

### Strategy 3: Test-Driven

Read the tests to understand intended behavior. Best for projects with good test coverage.

1. **Test runner config** — How are tests run? What framework?
2. **Integration tests** — Read top-level integration tests to understand the system's public interface.
3. **Service/model tests** — Read unit tests to understand individual component behavior.
4. **Fixtures** — Read test fixtures to understand data shapes.

### When to Use Each

| Situation | Strategy |
|---|---|
| First time seeing the project | Outside-In |
| Understanding a specific feature | Dependency-Driven |
| Understanding expected behavior | Test-Driven |
| Debugging a failure | Dependency-Driven from the failing code |
| Writing a CLAUDE.md from scratch | Outside-In, then verify with Test-Driven |

---

## Token-Saving Techniques for CLAUDE.md

### 1. Directory Purpose Statements

Instead of Claude reading 10 files in `src/services/` to understand the pattern:

```markdown
## Services (`src/services/`)
One service per domain entity. Services contain business logic and call models for data access.
Pattern: `export class XService { constructor(private db: PrismaClient) {} }`
Services never import from routes or middleware — dependencies flow inward only.
```

**Tokens saved:** ~3,000 per session (eliminates reading multiple service files to detect the pattern).

### 2. Convention Declarations

Instead of Claude reading 5 files to notice the error handling pattern:

```markdown
## Error Handling
- Throw `AppError` from `src/utils/errors.ts` (never raw Error or strings)
- AppError takes: `new AppError(message, statusCode, errorCode)`
- Middleware in `src/middleware/errorHandler.ts` catches all AppErrors and formats the response
- For validation errors: `throw new AppError("Invalid email", 400, "VALIDATION_ERROR")`
```

**Tokens saved:** ~2,000 per session.

### 3. Import Path Aliases

```markdown
## Import Aliases
- `@/` maps to `src/` (configured in tsconfig.json)
- Always use `@/services/UserService` not `../../services/UserService`
```

**Tokens saved:** ~500 per session (Claude doesn't need to read tsconfig to discover aliases).

### 4. Environment Documentation

Instead of Claude reading `.env.example` and guessing what each variable does:

```markdown
## Environment Variables
| Variable | Required | Default | Purpose |
|---|---|---|---|
| DATABASE_URL | yes | — | PostgreSQL connection string |
| REDIS_URL | no | localhost:6379 | Cache and job queue |
| STRIPE_SECRET_KEY | yes | — | Payment processing |
| LOG_LEVEL | no | info | debug, info, warn, error |
```

**Tokens saved:** ~1,000 per session.

### 5. Key File Annotations

Instead of Claude reading 20 files to find the important ones:

```markdown
## Key Files
When working on a task, start by reading these files for context:
- **Auth flow**: `src/middleware/auth.ts` → `src/services/AuthService.ts`
- **Database**: `prisma/schema.prisma` (all models), `src/models/index.ts` (exports)
- **API routes**: `src/api/index.ts` (route registration)
- **Config**: `src/config.ts` (all env vars validated here)
```

**Tokens saved:** ~4,000 per session (eliminates "which file should I read?" exploration).

### 6. Dependency Relationship Map

```markdown
## Dependencies Between Modules
Routes → Services → Models → Database
           ↓
         Jobs (async processing)
           ↓
       External APIs (Stripe, SendGrid)

Rule: Dependencies only flow downward. Never import from a higher layer.
```

**Tokens saved:** ~1,500 per session (Claude understands the architecture without tracing imports).

---

## Codebase-Specific CLAUDE.md Patterns

### For Monorepos

```markdown
## Packages
| Package | Path | Purpose | Local deps |
|---|---|---|---|
| @app/api | packages/api | REST API | @app/shared, @app/db |
| @app/web | packages/web | React frontend | @app/shared |
| @app/shared | packages/shared | Types, utils, validation | none |
| @app/db | packages/db | Database client, migrations | @app/shared |

## Cross-Package Rules
- Shared code → `@app/shared`. Never duplicate between packages.
- `@app/shared` must be built before dependent packages.
- Changes to `@app/db` affect all services — coordinate before migrating.
```

### For APIs

```markdown
## API Conventions
- Routes: `src/api/[resource]/` — one directory per resource
- Naming: `GET /api/v1/users/:id` maps to `src/api/users/get.ts`
- Validation: Zod schemas in `src/api/[resource]/schema.ts`
- Response format: `{ data: T, meta?: { pagination } }` for success
- Error format: `{ error: { message, code, details? } }`
```

### For Frontend Projects

```markdown
## Component Patterns
- Components: `src/components/[ComponentName]/index.tsx` + `styles.module.css`
- Pages: `src/app/[route]/page.tsx` (Next.js App Router)
- State: Zustand stores in `src/stores/`. One store per domain.
- API calls: `src/lib/api.ts` (typed fetch wrapper). Never use raw fetch.
- No component should exceed 200 lines. Extract to sub-components.
```

### For Data Pipelines

```markdown
## Pipeline Architecture
- Extractors (`src/extract/`): Pull data from sources. One file per source.
- Transformers (`src/transform/`): Pure functions. Input model → output model.
- Loaders (`src/load/`): Write to destinations. One file per destination.
- Orchestration: `src/pipeline.py` defines the DAG.
- All data schemas: Pydantic models in `src/models.py`

## Running
Full pipeline: `python -m pipeline --config config/prod.yaml`
Single stage: `python -m pipeline --stage extract --source salesforce`
Dry run: `python -m pipeline --dry-run` (logs actions, doesn't write)
```

---

## Optimization Audit

After producing a CLAUDE.md, audit it against these criteria:

| Question | If no... |
|---|---|
| Can Claude identify the right file to edit without globbing? | Add key files section |
| Can Claude run tests without asking? | Add exact test commands |
| Can Claude follow project conventions without reading existing code? | Add conventions with examples |
| Does Claude know the architecture without reading entry points? | Add project map with data flow |
| Can Claude understand errors without reading error handling code? | Add error handling patterns |
| Is every line earning its tokens? | Cut generic advice and obvious statements |

**Target:** A developer (or Claude) reading only the CLAUDE.md should be able to make their first contribution without reading any other documentation.

---

## Measuring Success

Track these metrics before and after optimization:

| Metric | Before | After |
|---|---|---|
| Tokens used for exploration (glob, grep, read) in first task | Measure | Should drop 50-80% |
| Number of "which file should I look at?" questions | Measure | Should drop to 0 |
| Number of convention violations in generated code | Measure | Should drop significantly |
| Time to first useful output | Measure | Should decrease |

The goal is not a perfect CLAUDE.md on day one. It's a CLAUDE.md that improves over time as new pitfalls and conventions are discovered.
