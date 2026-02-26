# ITT Project Structure

Standard directory layout for ITT Next.js App Router projects.

## Directory Layout

```
<project-root>/
├── app/
│   ├── (auth)/                    # Route group for auth pages
│   │   ├── login/page.tsx
│   │   ├── register/page.tsx
│   │   └── layout.tsx
│   ├── (dashboard)/               # Route group for authenticated pages
│   │   ├── page.tsx               # Dashboard home
│   │   ├── settings/page.tsx
│   │   └── layout.tsx
│   ├── api/                       # Route handlers
│   │   └── [...route]/route.ts    # Catch-all for Better-auth or tRPC
│   ├── globals.css                # Tailwind imports + ITT theme variables
│   ├── layout.tsx                 # Root layout (fonts, providers)
│   └── page.tsx                   # Landing / home page
├── components/
│   ├── ui/                        # Shadcn components (auto-generated)
│   ├── layout/                    # Navbar, Footer, Sidebar, etc.
│   ├── forms/                     # Form components (with TanStack Form)
│   └── shared/                    # Reusable domain components
├── lib/
│   ├── db/
│   │   ├── schema.ts              # Drizzle ORM schema
│   │   ├── index.ts               # DB connection
│   │   └── migrations/            # Drizzle migrations
│   ├── auth.ts                    # Better-auth config
│   ├── utils.ts                   # Shared utilities (cn, formatters, etc.)
│   └── validators.ts              # Zod schemas for shared validation
├── hooks/                         # Custom React hooks
├── types/                         # Shared TypeScript types
├── public/                        # Static assets
│   ├── fonts/                     # Self-hosted fonts (if not using Google Fonts)
│   └── images/
├── tests/
│   ├── unit/                      # Vitest unit tests
│   ├── components/                # Component tests with Testing Library
│   └── e2e/                       # Playwright E2E tests
├── .env.local                     # Local environment variables (git-ignored)
├── .env.example                   # Template for required env vars
├── drizzle.config.ts              # Drizzle ORM config
├── next.config.ts                 # Next.js config
├── tailwind.config.ts             # Tailwind config (v3) or use @theme in globals.css (v4)
├── tsconfig.json
├── vitest.config.ts
└── package.json
```

## Monorepo Layout (Bun + Turborepo)

```
<monorepo-root>/
├── apps/
│   ├── web/                       # Next.js app (structure as above)
│   └── docs/                      # Documentation site (optional)
├── packages/
│   ├── ui/                        # Shared component library
│   │   ├── src/components/
│   │   ├── package.json
│   │   └── tsconfig.json
│   ├── db/                        # Shared database package
│   │   ├── src/schema.ts
│   │   ├── drizzle.config.ts
│   │   └── package.json
│   ├── config-tailwind/           # Shared Tailwind preset
│   │   ├── preset.ts
│   │   └── package.json
│   └── config-typescript/         # Shared tsconfig
│       ├── base.json
│       └── package.json
├── turbo.json
├── bun.lock
└── package.json                   # Workspace root
```

## Key Conventions

### File Naming

- **Components:** PascalCase (`UserCard.tsx`, `LoginForm.tsx`)
- **Utilities / hooks:** camelCase (`useAuth.ts`, `formatDate.ts`)
- **Route files:** lowercase (`page.tsx`, `layout.tsx`, `loading.tsx`, `error.tsx`)
- **Schema / config:** camelCase (`schema.ts`, `drizzle.config.ts`)

### Import Aliases

Configure `@/` to point to the project root in `tsconfig.json`:

```json
{
  "compilerOptions": {
    "paths": {
      "@/*": ["./*"]
    }
  }
}
```

Usage: `import { Button } from "@/components/ui/button"`

### Server vs Client Components

- **Default to Server Components.** Only add `"use client"` when the component needs:
  - Event handlers (`onClick`, `onChange`, etc.)
  - React hooks (`useState`, `useEffect`, `useRef`, etc.)
  - Browser APIs (`window`, `document`, `localStorage`, etc.)
- **Keep Client Components small.** Extract the interactive part into a Client Component and keep the rest as Server Components.
- **Data fetching happens in Server Components.** Pass data down as props to Client Components.

### Route Groups

Use route groups `(name)` to organize layouts without affecting URL structure:
- `(auth)` — Public pages with minimal layout (login, register, forgot password)
- `(dashboard)` — Authenticated pages with sidebar/nav layout
- `(marketing)` — Public marketing pages with full-width layout
