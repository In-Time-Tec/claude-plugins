# /itt-frontend:scaffold

Scaffold a new Next.js project with ITT brand and stack configuration pre-applied.

## Usage

```
/itt-frontend:scaffold <project-name>
```

## Arguments

- `<project-name>` — Name of the project directory to create. Required.

## What This Command Does

When invoked, perform the following steps in order:

### 1. Create the Next.js project

Run the following command to create a new Next.js project:

```bash
bunx create-next-app@latest <project-name> --typescript --tailwind --eslint --app --src-dir=false --import-alias="@/*" --use-bun
```

If `bun` is not available, fall back to:

```bash
pnpm create next-app@latest <project-name> --typescript --tailwind --eslint --app --src-dir=false --import-alias="@/*" --use-pnpm
```

### 2. Install ITT dependencies

```bash
cd <project-name>
bun add @tanstack/react-form drizzle-orm motion lucide-react
bun add -d drizzle-kit vitest @testing-library/react @testing-library/jest-dom @vitejs/plugin-react
```

Or with pnpm:
```bash
cd <project-name>
pnpm add @tanstack/react-form drizzle-orm motion lucide-react
pnpm add -D drizzle-kit vitest @testing-library/react @testing-library/jest-dom @vitejs/plugin-react
```

### 3. Set up Shadcn

```bash
bunx --bun shadcn@latest init -d
```

Or with pnpm:
```bash
pnpm dlx shadcn@latest init -d
```

### 4. Configure fonts

Update `app/layout.tsx` to import Archivo and Montserrat from `next/font/google`:

```tsx
import { Archivo, Montserrat } from "next/font/google";

const archivo = Archivo({ subsets: ["latin"], variable: "--font-archivo" });
const montserrat = Montserrat({ subsets: ["latin"], variable: "--font-montserrat" });
```

Apply both font variables to the `<html>` or `<body>` tag:

```tsx
<html lang="en" className={`${archivo.variable} ${montserrat.variable}`}>
```

### 5. Apply ITT Tailwind theme

Read the `itt-brand` skill's `references/tailwind-preset.md` for the complete theme configuration. Apply the appropriate version:

- **Tailwind v4** (default with Next.js 15+): Add the `@theme` block to `app/globals.css`
- **Tailwind v3**: Create/update `tailwind.config.ts` with ITT brand tokens

### 6. Apply Shadcn theme

Read the `itt-brand` skill's `references/tailwind-preset.md` for the Shadcn CSS variables. Update `app/globals.css` with the ITT-branded `:root` and `.dark` CSS variables.

### 7. Create directory structure

Create the standard ITT directory structure as defined in the `itt-stack` skill's `references/project-structure.md`:

```bash
mkdir -p components/{ui,layout,forms,shared}
mkdir -p lib/db
mkdir -p hooks
mkdir -p types
mkdir -p tests/{unit,components,e2e}
mkdir -p public/{fonts,images}
```

### 8. Create utility files

Create `lib/utils.ts` with the `cn` helper:

```ts
import { type ClassValue, clsx } from "clsx";
import { twMerge } from "tailwind-merge";

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}
```

### 9. Create `.env.example`

```env
DATABASE_URL=postgresql://user:password@localhost:5432/dbname
BETTER_AUTH_SECRET=
BETTER_AUTH_URL=http://localhost:3000
```

### 10. Summary

After completing all steps, print a summary:

```
ITT project "<project-name>" scaffolded successfully!

Stack: Next.js 15 + React 19 + TypeScript + Tailwind CSS + Shadcn
Fonts: Archivo (headings) + Montserrat (body)
Theme: ITT brand colors, radii, and shadows applied
Directories: Standard ITT layout created

Next steps:
  cd <project-name>
  bun dev
```
