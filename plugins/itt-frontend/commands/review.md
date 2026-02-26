# /itt-frontend:review

Review code for ITT brand compliance and tech stack adherence.

## Usage

```
/itt-frontend:review [path]
```

## Arguments

- `[path]` — Optional. File or directory to review. Defaults to the current working directory.

## What This Command Does

When invoked, perform the following checks and report findings grouped by category.

### Brand Compliance Checks

**Colors:**
- Search for hardcoded color values that should use ITT tokens (e.g., `#3b82f6`, `blue-500`, any non-ITT hex colors)
- Flag usage of default Tailwind color utilities (`bg-blue-*`, `text-blue-*`, `border-blue-*`, etc.)
- Verify ITT brand colors are defined in Tailwind config or CSS theme

**Typography:**
- Check that Archivo and Montserrat are imported and configured
- Flag usage of non-ITT fonts (Inter, Arial, system-ui as primary)
- Verify `font-heading` and `font-body` utilities are used in components

**Border Radius:**
- Flag usage of generic Tailwind radius utilities (`rounded-lg`, `rounded-xl`, `rounded-2xl`)
- Verify cards use `rounded-[30px]`, buttons use `rounded-[15px]`, inputs use `rounded-[40px]`

**Shadows:**
- Flag usage of generic Tailwind shadows (`shadow-md`, `shadow-lg`, `shadow-xl`)
- Verify custom ITT shadow utilities (`shadow-card`, `shadow-glow`) are defined and used

### Stack Compliance Checks

**Dependencies:**
- Check `package.json` for non-standard libraries:
  - `axios` instead of native `fetch`
  - `react-hook-form` instead of `@tanstack/react-form`
  - `prisma` instead of `drizzle-orm`
  - `next-auth` instead of `better-auth`
  - `styled-components`, `@emotion/*`, or CSS Modules instead of Tailwind
  - `jest` instead of `vitest`
  - `moment`, `date-fns` instead of native `Intl`
  - `redux`, `zustand`, `mobx` for server state instead of React Server Components
- Flag any library from the "Do NOT Use" list in the `itt-stack` skill

**Project Structure:**
- Verify standard directories exist (`components/ui/`, `lib/`, `hooks/`, `types/`, `tests/`)
- Check for correct file naming conventions (PascalCase components, camelCase utilities)
- Verify `@/` import alias is configured in `tsconfig.json`

**TypeScript:**
- Verify `strict: true` is enabled in `tsconfig.json`
- Flag any usage of `any` type

**Next.js Patterns:**
- Flag unnecessary `"use client"` directives (components that don't use hooks or event handlers)
- Check that data fetching happens in Server Components, not Client Components

### Output Format

Present findings as a structured report:

```
## ITT Frontend Review — <path>

### Brand Compliance

✅ Colors: All using ITT brand tokens
⚠️ Typography: `Inter` font found in app/layout.tsx — replace with Archivo + Montserrat
❌ Border Radius: 5 instances of `rounded-lg` found — use `rounded-[30px]` for cards, `rounded-[15px]` for buttons
✅ Shadows: Using custom ITT shadow utilities

### Stack Compliance

✅ Dependencies: All packages match ITT stack
⚠️ Structure: Missing `tests/` directory
❌ TypeScript: `strict` not enabled in tsconfig.json
✅ Next.js: Server/Client component usage looks correct

### Summary

X issues found (Y critical, Z warnings)

### Recommended Fixes

1. [Most impactful fix first]
2. [Next fix]
...
```

Use ✅ for passing checks, ⚠️ for warnings, and ❌ for violations.
