---
name: codebase-onboarding
description: Analyze an unfamiliar codebase and generate a shareable architecture overview and onboarding guide with tech stack, architecture map, key entry points, directory map, and conventions. Use when joining a new project or wanting to understand a codebase.
---

# Codebase Onboarding

Systematically analyze an unfamiliar codebase and produce a shareable onboarding guide — a high-level architecture overview suitable for new developers joining the project.

## When to Use

- Joining a new team or repository
- User asks "help me understand this codebase"
- User says "onboard me" or "walk me through this repo"
- User wants a shareable architecture overview or README-style summary

## How It Works

### Phase 1: Reconnaissance

Gather raw signals about the project without reading every file. Run these checks in parallel:

```
1. Package manifest detection
   → package.json, go.mod, Cargo.toml, pyproject.toml, pom.xml, build.gradle,
     Gemfile, composer.json, mix.exs, pubspec.yaml

2. Framework fingerprinting
   → next.config.*, nuxt.config.*, angular.json, vite.config.*,
     django settings, flask app factory, fastapi main, rails config

3. Entry point identification
   → main.*, index.*, app.*, server.*, cmd/, src/main/

4. Directory structure snapshot
   → Top 2 levels of the directory tree, ignoring node_modules, vendor,
     .git, dist, build, __pycache__, .next

5. Config and tooling detection
   → .eslintrc*, .prettierrc*, tsconfig.json, Makefile, Dockerfile,
     docker-compose*, .github/workflows/, .env.example, CI configs

6. Test structure detection
   → tests/, test/, __tests__/, *_test.go, *.spec.ts, *.test.js,
     pytest.ini, jest.config.*, vitest.config.*
```

### Phase 2: Architecture Mapping

From the reconnaissance data, identify:

**Tech Stack**
- Language(s) and version constraints
- Framework(s) and major libraries
- Database(s) and ORMs
- Build tools and bundlers
- CI/CD: note the platform (e.g. GitHub Actions) and describe workflows at a high level (e.g. "runs tests on PR, deploys to staging on merge") — do not include workflow file names, job names, or step-level details

**Architecture Pattern**
- Monolith, monorepo, microservices, or serverless
- Frontend/backend split or full-stack
- API style: REST, GraphQL, gRPC, tRPC

**Key Directories**
Map the top-level directories to their purpose:

<!-- Example for a React project — replace with detected directories -->
```
src/components/  → React UI components
src/api/         → API route handlers
src/lib/         → Shared utilities
src/db/          → Database models and migrations
tests/           → Test suites
scripts/         → Build and deployment scripts
```

**Data Flow**
Trace one request from entry to response:
- Where does a request enter? (router, handler, controller)
- How is it validated? (middleware, schemas, guards)
- Where is business logic? (services, models, use cases)
- How does it reach the database? (ORM, raw queries, repositories)

### Phase 3: Convention Detection

Identify patterns the codebase already follows:

**Naming Conventions**
- File naming: kebab-case, camelCase, PascalCase, snake_case
- Component/class naming patterns
- Test file naming: `*.test.ts`, `*.spec.ts`, `*_test.go`

**Code Patterns**
- Error handling style: try/catch, Result types, error codes
- Dependency injection or direct imports
- State management approach
- Async patterns: callbacks, promises, async/await, channels

**Git Conventions**
- Branch naming from recent branches
- Commit message style from recent commits
- PR workflow (squash, merge, rebase)
- If the repo has no commits yet or only a shallow history (e.g. `git clone --depth 1`), skip this section and note "Git history unavailable or too shallow to detect conventions"

### Phase 4: Generate Onboarding Guide

Produce a single output: a shareable onboarding guide. Do NOT generate or modify a CLAUDE.md file.

```markdown
# Onboarding Guide: [Project Name]

## Overview
[2-3 sentences: what this project does and who it serves]

## Tech Stack
| Layer | Technology | Version |
|-------|-----------|---------|
| ... | ... | ... |

## Architecture
[ASCII diagram or description of how components connect.
Include deployment targets where known (e.g. "Deployed on Vercel", "Runs on Railway").]

## Key Entry Points
- **[Component]**: `path/` — what it does
- ...

## Directory Map
[Top-level directory → purpose mapping]

## Request Lifecycle
[Trace one API request from entry to response]

## Conventions
- [File naming pattern]
- [Error handling approach]
- [Testing patterns]
- [Git workflow]

## Code Style
- [Detected naming conventions]
- [Detected patterns to follow]

## Testing
- [Test runner and framework (e.g. "Bun's native test runner for unit/integration, Playwright for E2E")]
- [Test file conventions (e.g. "*.test.ts, *.spec.ts")]
- [Testing patterns (e.g. "isolated DB containers for integration tests", "fixtures over mocks")]

## Build & Run
- [Build tooling (e.g. "Vite for dev/bundling, Bun for script execution")]
- [Runtime architecture (e.g. "Docker Compose for local dev services", "Electron Forge for desktop builds")]
- [Notable patterns (e.g. "Vite proxies API calls in dev", "monorepo workspace builds")]

## Where to Look
[Architecture-oriented navigation only. Focus on where code for major
concerns lives in the repo (API layer, UI, data model, auth, etc.).
Omit operational entries like CI config paths or deployment config files.]

| I want to understand... | Look at... |
|------------------------|-----------|
| API layer | `src/api/` |
| Data model | `src/db/schema/` |
| Auth | `src/auth/` |
| UI components | `src/components/` |
```

## Best Practices

1. **Don't read everything** — reconnaissance should use Glob and Grep, not Read on every file. Read selectively only for ambiguous signals.
2. **Verify, don't guess** — if a framework is detected from config but the actual code uses something different, trust the code.
3. **Stay high-level** — the guide should be scannable in 2 minutes. Focus on architecture and structure, not operational commands.
4. **Flag unknowns** — if a convention can't be confidently detected, say so rather than guessing. "Could not determine test runner" is better than a wrong answer.
5. **Keep it shareable** — this guide should be appropriate to share with anyone. Avoid exposing CI/CD workflow internals, env var names, or bypass flags.

## Anti-Patterns to Avoid

- Generating or modifying a CLAUDE.md — this skill only produces the onboarding guide
- Including a "Common Tasks" section with specific commands — that's operational detail, not architecture
- Listing CI workflow file names, job names, or step-level details — just describe what CI does at a high level
- Including "Where to Look" entries for operational concerns (CI config, deployment config, build scripts) — keep it architecture-focused
- Listing every dependency — highlight only the ones that shape how you write code
- Describing obvious directory names — `src/` doesn't need an explanation
- Copying the README — the onboarding guide adds structural insight the README lacks

## Examples

### Example 1: First time in a new repo
**User**: "Onboard me to this codebase"
**Action**: Run full 4-phase workflow → produce Onboarding Guide
**Output**: Onboarding Guide printed directly to the conversation

### Example 2: Architecture overview
**User**: "Give me an architecture overview of this project"
**Action**: Run Phases 1-3, produce Onboarding Guide focused on architecture and tech stack
**Output**: High-level onboarding guide emphasizing structure and data flow
