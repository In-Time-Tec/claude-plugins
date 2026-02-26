# itt-frontend

ITT brand guidelines, tech stack preferences, and frontend tooling for Claude Code.

## What It Does

When installed, Claude automatically applies In Time Tec's brand and stack conventions during frontend development — no manual prompting needed.

### Skills (auto-activated)

| Skill | Triggers On | What It Does |
|-------|-------------|--------------|
| `itt-brand` | Any UI work | Applies exact brand tokens — colors, fonts, radii, shadows, spacing |
| `itt-stack` | Project setup, architecture decisions | Enforces ITT tech stack — Next.js 15, React 19, Tailwind, Shadcn, Drizzle, etc. |

### Commands (user-invoked)

| Command | Description |
|---------|-------------|
| `/itt-frontend:scaffold <name>` | Create a new Next.js project with ITT stack and brand pre-applied |
| `/itt-frontend:review [path]` | Review code for brand and stack compliance |

## Install

```shell
# Add the ITT marketplace (one-time)
/plugin marketplace add in-time-tec/claude-plugins

# Install this plugin
/plugin install itt-frontend@itt-plugins
```

## Brand Quick Reference

| Token | Value |
|-------|-------|
| Primary Orange | `#f47e20` |
| Accent Orange | `#FF9902` |
| Warm CTA | `#f06023` |
| Dark Text | `#211E1E` |
| Heading Font | Archivo |
| Body Font | Montserrat |
| Card Radius | 30px |
| Button Radius | 15px |
| Input Radius | 40px |

## Stack Quick Reference

| Layer | Choice |
|-------|--------|
| Framework | Next.js 15+ (App Router) |
| Language | TypeScript (strict) |
| Styling | Tailwind CSS 4.x + Shadcn + Radix UI |
| Forms | TanStack React Form |
| Database | Drizzle ORM + PostgreSQL |
| Auth | Better-auth |
| Testing | Vitest + Testing Library |
| Icons | Lucide React |
| Animation | Motion |
