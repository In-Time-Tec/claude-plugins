# ITT Tailwind CSS Preset

Use this as the base Tailwind configuration for all ITT projects.

## Tailwind v4 (`app/globals.css`)

```css
@import "tailwindcss";

@theme {
  /* Colors */
  --color-itt-orange: #f47e20;
  --color-itt-accent: #FF9902;
  --color-itt-cta: #f06023;
  --color-itt-dark: #211E1E;
  --color-itt-light: #F5F5F5;
  --color-itt-light-alt: #f0efef;
  --color-itt-gray: #6b6b6b;
  --color-itt-border: #e5e5e5;

  /* Fonts */
  --font-heading: "Archivo", sans-serif;
  --font-body: "Montserrat", sans-serif;

  /* Border Radius */
  --radius-card: 30px;
  --radius-button: 15px;
  --radius-input: 40px;

  /* Shadows */
  --shadow-card: 0 0 8px 0 rgba(205, 202, 202, 1);
  --shadow-glow: 0 0 12px 0 rgba(244, 126, 32, 0.3);

  /* Container */
  --container-max-width: 1320px;

  /* Breakpoints */
  --breakpoint-sm: 576px;
  --breakpoint-md: 767px;
  --breakpoint-lg: 991px;
  --breakpoint-xl: 1199px;
}
```

## Tailwind v3 (`tailwind.config.ts`) — Legacy Support

```ts
import type { Config } from "tailwindcss";

const config: Config = {
  content: [
    "./app/**/*.{ts,tsx}",
    "./components/**/*.{ts,tsx}",
  ],
  theme: {
    screens: {
      sm: "576px",
      md: "767px",
      lg: "991px",
      xl: "1199px",
    },
    extend: {
      colors: {
        itt: {
          orange: "#f47e20",
          accent: "#FF9902",
          cta: "#f06023",
          dark: "#211E1E",
          light: "#F5F5F5",
          "light-alt": "#f0efef",
          gray: "#6b6b6b",
          border: "#e5e5e5",
        },
      },
      fontFamily: {
        heading: ["var(--font-archivo)", "Archivo", "sans-serif"],
        body: ["var(--font-montserrat)", "Montserrat", "sans-serif"],
      },
      borderRadius: {
        card: "30px",
        button: "15px",
        input: "40px",
      },
      boxShadow: {
        card: "0 0 8px 0 rgba(205,202,202,1)",
        glow: "0 0 12px 0 rgba(244,126,32,0.3)",
      },
      maxWidth: {
        container: "1320px",
      },
      spacing: {
        "section-sm": "40px",
        "section-md": "80px",
        "section-lg": "120px",
      },
    },
  },
  plugins: [],
};

export default config;
```

## Shadcn Theme Mapping (`app/globals.css` additions)

When using Shadcn, map CSS variables to ITT brand tokens:

```css
@layer base {
  :root {
    --background: 0 0% 96%;          /* #F5F5F5 */
    --foreground: 12 3% 12%;         /* #211E1E */
    --card: 0 0% 100%;               /* #FFFFFF */
    --card-foreground: 12 3% 12%;    /* #211E1E */
    --popover: 0 0% 100%;
    --popover-foreground: 12 3% 12%;
    --primary: 26 91% 54%;           /* #f47e20 */
    --primary-foreground: 0 0% 100%;
    --secondary: 0 0% 94%;           /* #f0efef */
    --secondary-foreground: 12 3% 12%;
    --muted: 0 0% 94%;
    --muted-foreground: 0 0% 42%;    /* #6b6b6b */
    --accent: 35 100% 50%;           /* #FF9902 */
    --accent-foreground: 12 3% 12%;
    --destructive: 0 84% 60%;
    --destructive-foreground: 0 0% 100%;
    --border: 0 0% 90%;              /* #e5e5e5 */
    --input: 0 0% 90%;
    --ring: 26 91% 54%;              /* #f47e20 */
    --radius: 15px;                  /* Button radius as default */
  }

  .dark {
    --background: 12 3% 12%;         /* #211E1E */
    --foreground: 0 0% 96%;          /* #F5F5F5 */
    --card: 0 0% 15%;
    --card-foreground: 0 0% 96%;
    --popover: 0 0% 15%;
    --popover-foreground: 0 0% 96%;
    --primary: 26 91% 54%;           /* #f47e20 */
    --primary-foreground: 0 0% 100%;
    --secondary: 0 0% 20%;
    --secondary-foreground: 0 0% 96%;
    --muted: 0 0% 20%;
    --muted-foreground: 0 0% 60%;
    --accent: 35 100% 50%;           /* #FF9902 */
    --accent-foreground: 0 0% 100%;
    --destructive: 0 84% 60%;
    --destructive-foreground: 0 0% 100%;
    --border: 0 0% 25%;
    --input: 0 0% 25%;
    --ring: 26 91% 54%;
  }
}
```
