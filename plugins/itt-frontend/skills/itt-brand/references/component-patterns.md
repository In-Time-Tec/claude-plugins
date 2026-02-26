# ITT Component Patterns

Reference JSX patterns for common UI components using ITT brand tokens.

## Primary Button

```tsx
<button className="bg-itt-orange hover:bg-itt-accent text-white font-body font-semibold px-6 py-3 rounded-button transition-colors">
  Get Started
</button>
```

With Shadcn:
```tsx
<Button className="bg-itt-orange hover:bg-itt-accent rounded-button font-body">
  Get Started
</Button>
```

## CTA Button

```tsx
<button className="bg-itt-cta hover:bg-itt-orange text-white font-body font-bold px-8 py-4 rounded-button transition-colors">
  Start Free Trial
</button>
```

## Outline Button

```tsx
<button className="border-2 border-itt-orange text-itt-orange hover:bg-itt-orange hover:text-white font-body font-semibold px-6 py-3 rounded-button transition-colors">
  Learn More
</button>
```

## Card

```tsx
<div className="bg-white rounded-card shadow-card p-8 hover:shadow-glow transition-shadow">
  <h3 className="font-heading font-bold text-xl text-itt-dark mb-3">
    Card Title
  </h3>
  <p className="font-body text-itt-gray leading-relaxed">
    Card description text goes here with enough content to demonstrate
    the body font and color usage.
  </p>
</div>
```

## Input Field

```tsx
<input
  type="text"
  placeholder="Enter your email"
  className="w-full px-6 py-3 rounded-input border border-itt-border bg-white font-body text-itt-dark placeholder:text-itt-gray focus:outline-none focus:ring-2 focus:ring-itt-orange/30 focus:border-itt-orange transition-colors"
/>
```

With label:
```tsx
<div className="space-y-2">
  <label className="font-body font-medium text-itt-dark text-sm pl-4">
    Email Address
  </label>
  <input
    type="email"
    placeholder="you@example.com"
    className="w-full px-6 py-3 rounded-input border border-itt-border bg-white font-body text-itt-dark placeholder:text-itt-gray focus:outline-none focus:ring-2 focus:ring-itt-orange/30 focus:border-itt-orange transition-colors"
  />
</div>
```

## Section Layout

```tsx
<section className="py-section-md lg:py-section-lg">
  <div className="max-w-container mx-auto px-5">
    <h2 className="font-heading font-bold text-3xl md:text-4xl text-itt-dark text-center mb-12">
      Section Heading
    </h2>
    <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-5">
      {/* Cards here */}
    </div>
  </div>
</section>
```

## Navigation Bar

```tsx
<nav className="sticky top-0 z-50 bg-white shadow-card">
  <div className="max-w-container mx-auto px-5 flex items-center justify-between h-16">
    <a href="/" className="font-heading font-bold text-xl text-itt-dark">
      Logo
    </a>
    <div className="hidden md:flex items-center gap-8">
      <a href="#" className="font-body text-itt-dark hover:text-itt-orange transition-colors">
        Features
      </a>
      <a href="#" className="font-body text-itt-dark hover:text-itt-orange transition-colors">
        Pricing
      </a>
      <button className="bg-itt-orange hover:bg-itt-accent text-white font-body font-semibold px-5 py-2 rounded-button transition-colors">
        Sign Up
      </button>
    </div>
  </div>
</nav>
```

## Hero Section

```tsx
<section className="bg-itt-light py-section-lg">
  <div className="max-w-container mx-auto px-5 text-center">
    <h1 className="font-heading font-extrabold text-4xl md:text-5xl lg:text-6xl text-itt-dark mb-6">
      Build Something <span className="text-itt-orange">Amazing</span>
    </h1>
    <p className="font-body text-itt-gray text-lg md:text-xl max-w-2xl mx-auto mb-10">
      Description text that explains the value proposition clearly
      and concisely using Montserrat body font.
    </p>
    <div className="flex flex-col sm:flex-row items-center justify-center gap-4">
      <button className="bg-itt-cta hover:bg-itt-orange text-white font-body font-bold px-8 py-4 rounded-button transition-colors">
        Get Started Free
      </button>
      <button className="border-2 border-itt-orange text-itt-orange hover:bg-itt-orange hover:text-white font-body font-semibold px-8 py-4 rounded-button transition-colors">
        Watch Demo
      </button>
    </div>
  </div>
</section>
```

## Badge / Tag

```tsx
<span className="inline-flex items-center px-3 py-1 rounded-full bg-itt-orange/10 text-itt-orange font-body text-sm font-medium">
  New Feature
</span>
```

## Form with Shadcn

```tsx
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { Label } from "@/components/ui/label";

<form className="bg-white rounded-card shadow-card p-8 space-y-6 max-w-md mx-auto">
  <div className="space-y-2">
    <Label className="font-body font-medium text-itt-dark pl-4">Name</Label>
    <Input
      placeholder="Your full name"
      className="rounded-input border-itt-border font-body focus-visible:ring-itt-orange/30"
    />
  </div>
  <div className="space-y-2">
    <Label className="font-body font-medium text-itt-dark pl-4">Email</Label>
    <Input
      type="email"
      placeholder="you@example.com"
      className="rounded-input border-itt-border font-body focus-visible:ring-itt-orange/30"
    />
  </div>
  <Button className="w-full bg-itt-orange hover:bg-itt-accent rounded-button font-body font-semibold">
    Submit
  </Button>
</form>
```
