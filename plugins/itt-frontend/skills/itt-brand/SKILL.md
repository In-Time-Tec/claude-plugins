# ITT Brand Guidelines

Activate this skill whenever building, modifying, or reviewing any UI code for In Time Tec projects.

## When to Activate

- Creating or editing React components, pages, or layouts
- Writing or modifying CSS, Tailwind classes, or theme config
- Reviewing UI code for brand compliance
- Generating mockups or design recommendations

## Brand Tokens

### Colors

| Token | Hex | Usage |
|-------|-----|-------|
| Primary Orange | `#f47e20` | Brand primary, nav accents, primary buttons |
| Accent Orange | `#FF9902` | Hover states, highlights, secondary accents |
| Warm CTA | `#f06023` | Call-to-action buttons, urgent actions |
| Dark Text | `#211E1E` | Headings, body text, dark backgrounds |
| Light BG | `#F5F5F5` | Page backgrounds, section alternates |
| Light BG Alt | `#f0efef` | Card backgrounds, subtle contrast areas |
| White | `#FFFFFF` | Cards, content areas, text on dark |
| Medium Gray | `#6b6b6b` | Secondary text, captions |
| Border Gray | `#e5e5e5` | Borders, dividers, input outlines |

### Typography

| Role | Font Family | Weight | Notes |
|------|-------------|--------|-------|
| Display / Headings | **Archivo** | 600–800 | Use for all h1–h4, hero text, section titles |
| Body / UI | **Montserrat** | 400–600 | Use for paragraphs, labels, buttons, nav items |

**Font loading:** Import from Google Fonts. In Next.js, use `next/font/google`:

```tsx
import { Archivo, Montserrat } from "next/font/google";

const archivo = Archivo({ subsets: ["latin"], variable: "--font-archivo" });
const montserrat = Montserrat({ subsets: ["latin"], variable: "--font-montserrat" });
```

### Border Radius

| Element | Radius | Tailwind Class |
|---------|--------|----------------|
| Cards | `30px` | `rounded-card` |
| Buttons | `15px` | `rounded-button` |
| Input fields | `40px` | `rounded-input` |
| Badges / Tags | `999px` | `rounded-full` |

### Shadows

| Token | Value | Usage |
|-------|-------|-------|
| Default | `0 0 8px 0 rgba(205,202,202,1)` | Cards, dropdowns, popovers |
| Orange Glow | `0 0 12px 0 rgba(244,126,32,0.3)` | Hover on branded elements, focus rings |

### Spacing

| Context | Value |
|---------|-------|
| Section vertical padding | `40px` – `120px` (responsive) |
| Card internal padding | `20px` – `40px` |
| Grid gap | `20px` |
| Container max-width | `1240px` – `1320px` |

### Breakpoints

| Name | Value | Use |
|------|-------|-----|
| sm | `576px` | Mobile landscape |
| md | `768px` | Tablet |
| lg | `992px` | Small desktop |
| xl | `1200px` | Standard desktop |

## Anti-Patterns — Do NOT Use

- **Default Tailwind blue** (`blue-500`, `blue-600`, etc.) — use brand oranges
- **Inter, Arial, or system fonts** — use Archivo + Montserrat exclusively
- **`rounded-lg`, `rounded-xl`, `rounded-2xl`** — use semantic brand radii (`rounded-card`, `rounded-button`, `rounded-input`)
- **`shadow-md`, `shadow-lg`** — use exact brand shadows via custom utility or arbitrary value
- **Generic gray palettes** (`gray-100` through `gray-900`) — map to brand grays (`#211E1E`, `#6b6b6b`, `#e5e5e5`, `#F5F5F5`)
- **Hardcoded color values inline** — define in Tailwind theme config and use semantic tokens

## Reference Files

Read these for implementation details:

- `references/tailwind-preset.md` — Complete Tailwind CSS theme configuration with all brand tokens
- `references/component-patterns.md` — JSX examples for common UI patterns (cards, buttons, inputs, nav)
