---
name: frontend-tweak
description: Run BEFORE doing any frontend changes. Analyzes the request, discovers the codebase, classifies the change, and produces a structured change plan.
---

## When to Use

Use this skill **first** whenever the user asks to:
- Tweak frontend / update UI / modify styling
- Adjust layout or spacing
- Change colors, fonts, or typography
- Add or modify animations/transitions
- Fix a responsive or visual bug
- Change frontend component behavior
- Update dark mode or theme styles
- Improve accessibility of UI elements

This skill runs **before** any code is touched. Its job is to understand, classify, and plan.

---

## Workflow

### Step 1: Parse the User's Intent

Read the user's request carefully. Extract these pieces of information:

| What to extract | How to identify |
|-----------------|-----------------|
| **Target element** | "the navbar", "the login button", "the card grid" |
| **Desired change** | "make it bigger", "change color to blue", "center it" |
| **Scope** | Single element, one page, all pages, a component |
| **Reference** | Screenshot, URL, another page in the app, a design system |
| **Constraints** | "don't break mobile", "keep dark mode working", "match the header style" |

**If the request is vague**, ask exactly 1–2 targeted questions:
- "Which specific page or component should I modify?"
- "Should this change apply globally or only on this page?"

**Do NOT ask** if the request is clear enough to act on. Prefer acting over asking.

### Step 2: Parse Structured Content (if provided)

If the user provides content with formatting hints, interpret them as:

| Notation | Meaning | Typical CSS |
|----------|---------|-------------|
| **H1** | Primary heading (largest) | `font-size: 2rem–2.5rem; font-weight: 700` |
| **H2** | Section heading (medium) | `font-size: 1.5rem–1.75rem; font-weight: 600` |
| **H3** | Sub-heading (small) | `font-size: 1.25rem–1.5rem; font-weight: 600` |
| **P** | Body paragraph | `font-size: 1rem; font-weight: 400; line-height: 1.6` |
| **Bold** | Emphasis / key term | `font-weight: 600–700` |
| **Italic** | Secondary emphasis | `font-style: italic` |
| **List** | Enumerated items | `<ul>` / `<ol>` with proper spacing |
| **Link** | Clickable reference | `<a>` with hover/focus states |
| **Image ref** | Visual asset | `<img>` with alt text and responsive sizing |

If the user provides a **screenshot or mockup image**, analyze it for:
- Layout structure (grid, flex, positioning)
- Color palette and contrast ratios
- Typography scale and weights
- Spacing rhythm (padding/margin patterns)
- Interactive states visible (hover, active, disabled)

### Step 3: Classify the Change Type

Every frontend change falls into one or more of these categories. Classify the request to determine the execution approach:

| Category | Examples | Risk Level |
|----------|----------|------------|
| **Visual-only** | Color change, font swap, shadow tweak | 🟢 Low |
| **Spacing/Layout** | Margin/padding adjust, flexbox/grid change | 🟡 Medium |
| **Structural** | Adding/removing DOM elements, changing component hierarchy | 🟠 High |
| **Behavioral** | Click handlers, conditional rendering, state changes | 🟠 High |
| **Responsive** | Breakpoint changes, mobile layout fixes | 🟡 Medium |
| **Theme/Global** | Dark mode, CSS variable changes, global font change | 🔴 Critical |
| **Animated** | Adding transitions, keyframe animations, scroll effects | 🟡 Medium |
| **Accessibility** | ARIA attributes, focus management, keyboard navigation | 🟠 High |

**If a change is 🔴 Critical**, call it out explicitly and list all potentially affected areas before proceeding.

### Step 4: Discover the Project Structure

Before planning any changes, understand the codebase:

1. **Identify the framework:**
   - Look for `package.json` → check `dependencies` and `devDependencies`
   - React: `react`, `react-dom`, `next` (Next.js), `gatsby`
   - Vue: `vue`, `nuxt`
   - Angular: `@angular/core`
   - Vanilla: No framework dependencies, plain HTML/CSS/JS
   - Svelte: `svelte`

2. **Identify the CSS approach:**
   - CSS Modules: Files ending in `.module.css` or `.module.scss`
   - Tailwind: `tailwind.config.js` or `tailwind.config.ts` present
   - Styled-components: `styled-components` in `package.json`
   - Sass/SCSS: `.scss` or `.sass` files
   - CSS-in-JS: `@emotion`, `linaria`, `vanilla-extract`
   - Vanilla CSS: Plain `.css` files

3. **Identify the file structure:**
   - Components directory (e.g., `src/components/`)
   - Styles directory (e.g., `src/styles/`, `src/css/`)
   - Pages directory (e.g., `src/pages/`, `app/`)
   - Shared/global styles (e.g., `globals.css`, `index.css`, `variables.css`)
   - Theme configuration (e.g., `theme.js`, `theme.ts`, CSS custom properties file)

4. **Identify existing design tokens:**
   - CSS custom properties (`--color-primary`, `--spacing-md`, etc.)
   - Tailwind config values
   - JS/TS theme objects
   - SCSS variables

### Step 5: Analyze Existing Styles of the Target

Before modifying anything, inspect the current state of the target element:

1. **Read the component file** — understand the JSX/HTML structure
2. **Read the associated stylesheet** — understand current styles
3. **Check for shared classes** — will changing this class affect other components?
4. **Check for media queries** — does the element have responsive styles?
5. **Check for theme variants** — does it change in dark mode or other themes?
6. **Check for state variants** — hover, focus, active, disabled states
7. **Check for parent constraints** — is it inside a flex/grid container that controls its sizing?

### Step 6: Assess Impact Radius

Determine what else could be affected by this change:

| Scope | What to check |
|-------|---------------|
| **Same component** | Other elements using the same class names |
| **Sibling components** | Components in the same directory or rendered together |
| **Parent components** | Layout containers, page wrappers |
| **Global styles** | If modifying a CSS variable or global class |
| **Shared utilities** | Utility classes used across multiple files |
| **Media queries** | All breakpoints where the element appears |
| **Theme variants** | Dark mode, high-contrast, other themes |

**Document every file that will be touched** before proceeding.

### Step 7: Generate the Change Plan

Before writing any code, produce a mental change plan with this structure:

```
CHANGE PLAN
===========
Request: [one-line summary of what the user wants]
Type: [Visual / Spacing / Structural / Behavioral / Responsive / Theme / Animated / Accessibility]
Risk: [🟢 Low / 🟡 Medium / 🟠 High / 🔴 Critical]

Files to modify:
  1. [filepath] — [what changes]
  2. [filepath] — [what changes]

Files to inspect (read-only):
  1. [filepath] — [why: checking for shared styles / parent layout / etc.]

Potential side effects:
  - [description of what could break]

Breakpoints to verify: [list applicable breakpoints]
Theme variants to verify: [light / dark / etc.]
```

**Only after this plan is clear**, proceed to the `frontend-work` skill for execution.

---

## Edge Cases

### Ambiguous Scope
- User says "make it look better" → Ask: "Which specific element or page should I improve?"
- User says "fix the styling" → Ask: "Can you point me to the specific issue? A screenshot would help."

### Conflicting Requirements
- "Make the font bigger AND fit more content" → Clarify: these may conflict; propose a compromise (e.g., bigger font with scrollable container, or bigger font with condensed content).

### Missing Context
- User references "the card" but there are multiple card components → List the card components found and ask which one.
- User says "make it match the design" but provides no design reference → Ask for a screenshot, URL, or description.

### Global vs Local Confusion
- User asks to "change the primary color" → Clarify: does this mean the CSS variable globally, or just this one element's color?
- If the project uses design tokens, always prefer changing at the token level for global changes.

### Responsive Implications
- Any layout change should be evaluated at all defined breakpoints.
- If the project defines breakpoints (e.g., in Tailwind config or CSS variables), list them.
- If no breakpoints exist, assume: `mobile (≤768px)`, `tablet (769–1024px)`, `desktop (≥1025px)`.

### Accessibility Implications
- Color changes → Check contrast ratio (minimum 4.5:1 for text, 3:1 for large text).
- Layout changes → Ensure tab order is not disrupted.
- Removing elements → Ensure screen readers can still navigate the page.
- Animation → Respect `prefers-reduced-motion`.

### Dark Mode / Theme Awareness
- If the project has dark mode, every color-related change must be verified in both light and dark mode.
- Check for `prefers-color-scheme` media queries or theme toggle logic.

---

## Critical Rules

1. **Never skip discovery.** Always run Steps 4–6 before planning changes.
2. **Never assume the framework.** Always verify from `package.json` or file extensions.
3. **Never modify code in this skill.** This skill only analyzes and plans. Code changes happen in `frontend-work`.
4. **Always document the impact radius.** Even for "trivial" changes.
5. **Ask, don't guess.** If the user's intent is genuinely ambiguous (not just underspecified), ask one precise question.
6. **Prefer tokens over hardcoded values.** If the project uses CSS variables or a theme system, always map changes to existing tokens first.
