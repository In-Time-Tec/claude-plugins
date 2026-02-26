---
name: itt-stack
description: ITT tech stack preferences and architecture decisions. Activate when setting up projects, choosing libraries, or reviewing project structure for In Time Tec frontend projects.
---

# ITT Tech Stack

Activate this skill when setting up new projects, making architecture decisions, choosing libraries, or reviewing project structure for In Time Tec frontend projects.

## When to Activate

- Creating a new project or initializing a codebase
- Choosing between libraries, frameworks, or tools
- Setting up build tooling, linting, or CI configuration
- Reviewing project structure and dependencies
- Adding new dependencies to an existing project

## Core Stack

### Framework & Language

| Layer | Choice | Notes |
|-------|--------|-------|
| Framework | **Next.js 15+** | App Router only. No Pages Router for new projects. |
| UI Library | **React 19** | Use Server Components by default, Client Components only when needed. |
| Language | **TypeScript (strict)** | Enable `strict: true` in `tsconfig.json`. No `any` types. |

### Package Management & Build

| Context | Tool | Notes |
|---------|------|-------|
| Monorepo | **Bun** | Use Bun workspaces + `bun install`. |
| Monorepo orchestration | **Turborepo** | Use `turbo` for task running, caching, and dependency graph. |
| Standalone project | **pnpm** | Default for single-project repos. |

### Styling & Components

| Layer | Choice | Notes |
|-------|--------|-------|
| CSS Framework | **Tailwind CSS 4.x** | Use `@theme` syntax for v4. Fall back to `tailwind.config.ts` for v3 projects. |
| Component Library | **Shadcn/ui** | Install components into `components/ui/`. Customize to ITT brand. |
| Primitives | **Radix UI** | Used via Shadcn. Import directly for custom components. |
| Icons | **Lucide React** | Consistent icon set. Do not mix with Heroicons or other sets. |

### Data & Forms

| Layer | Choice | Notes |
|-------|--------|-------|
| Forms | **TanStack React Form** | Prefer over React Hook Form for new projects. |
| ORM | **Drizzle ORM** | Schema-first, TypeScript-native. Use with Postgres. |
| Database | **PostgreSQL** | Default database. Use Neon or Supabase for serverless. |
| Server State | **TanStack Query** | Use for fetching, caching, and syncing server-derived data. |
| Validation | **Zod** | Schema validation for forms, API responses, and env vars. |
| Auth | **Better-auth** | Lightweight, self-hosted auth. Prefer over NextAuth for new projects. |

### Quality & Patterns

| Layer | Choice | Notes |
|-------|--------|-------|
| Animation | **Motion** (formerly Framer Motion) | Use for page transitions, micro-interactions, scroll animations. |
| FP Patterns | **Effect-ts** | For complex async flows, error handling, dependency injection. |
| Testing | **Vitest** + **Testing Library** | Unit and component tests. Use Playwright for E2E. |
| Linting (platform) | **Oxlint** + **Oxfmt** | Use for monorepo/platform projects with Bun. Swap to Prettier if formatting stability issues arise. |
| Linting (standalone) | **ESLint** + **Prettier** | Use for standalone pnpm projects. |

## Decision Rules

1. **New project?** Use the full stack above. Run `/itt-frontend:scaffold` to generate.
2. **Adding to existing project?** Match existing choices. Don't introduce competing libraries (e.g., don't add React Hook Form if TanStack Form is already used).
3. **Choosing between two libraries?** Prefer the one listed above unless there's a specific technical reason not to.
4. **Not sure about a dependency?** Check if Shadcn or Radix already provides the functionality before adding a new package.

## Anti-Patterns — Do NOT Use

- **Pages Router** — App Router only for new Next.js projects
- **CSS Modules or styled-components** — Use Tailwind CSS
- **Axios** — Use native `fetch` (built into Next.js with caching support)
- **Moment.js or date-fns** — Use `Intl.DateTimeFormat` or `Temporal` (when stable)
- **Redux or Zustand for server-derived state** — Use React Server Components and TanStack Query for data that originates from the server. Client-only UI state (modals, tabs) is fine in local component state.
- **NextAuth / Auth.js** — Use Better-auth for new projects
- **React Hook Form** — Use TanStack React Form for new projects
- **Prisma** — Use Drizzle ORM
- **Jest** — Use Vitest

## Reference Files

Read this for project structure details:

- `references/project-structure.md` — Standard directory layout for ITT Next.js projects
