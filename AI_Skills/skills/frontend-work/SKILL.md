---
name: frontend-work
description: Main execution skill for all frontend changes. Handles code editing for UI tweaks, layout adjustments, styling modifications, component changes, and responsive fixes.
---

## When to Use

Use this skill whenever you are ready to **execute** frontend code changes after the `frontend-pre-work` skill has analyzed and planned the work. This skill covers:
- Tweaking frontend / updating UI / modifying styling
- Adjusting layout or spacing
- Changing colors, fonts, or typography
- Adding or modifying animations/transitions
- Fixing responsive or visual bugs
- Changing frontend component behavior
- Updating dark mode or theme styles
- Improving accessibility of UI elements

---

## Workflow

### Step 1: Confirm Prerequisites

Before writing any code, verify:
- [ ] You have already run the `frontend-pre-work` skill (or have equivalent understanding)
- [ ] You know the **framework** (React / Vue / Angular / Svelte / Vanilla)
- [ ] You know the **CSS approach** (Modules / Tailwind / styled-components / Vanilla / SCSS)
- [ ] You know the **target files** to modify
- [ ] You know the **impact radius** (other files that could be affected)

If any of these are unclear, go back to `frontend-pre-work` before proceeding.

### Step 2: Read Before Writing

Always read the full context before making any edit:

1. **Read the target component file completely** — understand ALL the JSX/HTML
2. **Read the target stylesheet completely** — understand ALL existing styles
3. **Read the parent component** — understand layout constraints
4. **Read global/shared styles** — understand inherited styles and CSS variables
5. **Read the theme config** (if applicable) — understand available design tokens

**Never edit a file you haven't fully read first.**

### Step 3: Choose the Right Editing Strategy

Pick the strategy based on the change type identified in `frontend-pre-work`:

#### 🟢 Visual-Only Changes (colors, shadows, borders, opacity)

1. Locate the exact CSS rule(s) that control the visual property
2. Check if the value comes from a CSS variable or theme token
3. If yes → change the variable/token value (consider impact on other consumers)
4. If no → change the hardcoded value, and consider extracting to a variable if it's used more than once
5. Verify the change across all theme variants (light/dark)

```
Example: Changing a button color
─────────────────────────────────
WRONG:  .btn { background: #3b82f6; }       ← hardcoded, breaks theme
RIGHT:  .btn { background: var(--btn-bg); }  ← uses token
        :root { --btn-bg: #3b82f6; }
        [data-theme="dark"] { --btn-bg: #60a5fa; }
```

#### 🟡 Spacing/Layout Changes (margin, padding, flex, grid)

1. Identify the current layout model (flex, grid, block, inline)
2. Check if spacing values follow a consistent scale (4px, 8px, 16px, etc.)
3. Make the change using the existing scale — do NOT introduce rogue values
4. Test at all breakpoints mentally: does this spacing work on mobile?
5. Check parent container constraints — will the new spacing cause overflow?

```
Example: Adding gap between cards
─────────────────────────────────
WRONG:  .card { margin-right: 13px; }         ← arbitrary value
RIGHT:  .card-grid { gap: var(--spacing-md); } ← consistent scale, on parent
```

#### 🟠 Structural Changes (DOM structure, component hierarchy)

1. Understand the full component tree around the target
2. Plan the new structure by writing the HTML/JSX outline FIRST (as a comment)
3. Implement the structural change
4. Update ALL associated styles — new elements need styles, removed elements' styles should be cleaned up
5. Check for broken references: event handlers, refs, IDs, test selectors
6. Check for broken accessibility: heading hierarchy, landmark regions, tab order

#### 🟠 Behavioral Changes (event handlers, state, conditional rendering)

1. Read all existing state and props in the component
2. Understand the data flow (where does state come from, where does it go)
3. Implement the behavioral change with minimal state additions
4. Ensure UI reflects all possible states: loading, empty, error, success, disabled
5. Test edge cases: rapid clicks, empty inputs, network failures, race conditions

#### 🟡 Responsive Changes (breakpoints, mobile fixes)

1. List all breakpoints used in the project
2. Read existing media queries for the target element
3. Apply mobile-first approach: base styles for mobile, then `min-width` queries for larger screens
4. Handle these common mobile issues:
   - Horizontal overflow (content wider than viewport)
   - Touch targets too small (minimum 44×44px)
   - Text too small (minimum 16px for body text on mobile)
   - Fixed elements covering content
   - Viewport units misbehaving (use `dvh` instead of `vh` where supported)

```
Example: Making a grid responsive
──────────────────────────────────
/* Mobile first */
.grid { display: grid; grid-template-columns: 1fr; gap: 1rem; }

/* Tablet and up */
@media (min-width: 768px) {
  .grid { grid-template-columns: repeat(2, 1fr); }
}

/* Desktop and up */
@media (min-width: 1024px) {
  .grid { grid-template-columns: repeat(3, 1fr); }
}
```

#### 🔴 Theme/Global Changes (CSS variables, global classes, font changes)

1. **Map the full usage** of the variable/class you're changing — grep the entire codebase
2. List every file that references it
3. Make the change at the source (variables file, theme config)
4. Verify that every consumer of that variable/class still looks correct
5. If only some consumers should change, create a NEW variable instead of modifying the shared one

#### 🟡 Animation Changes (transitions, keyframes, scroll effects)

1. Check for existing animation/transition patterns in the project
2. Follow the existing easing and duration conventions
3. Always add `prefers-reduced-motion` override:

```css
.element {
  transition: transform 0.3s ease;
}

@media (prefers-reduced-motion: reduce) {
  .element {
    transition: none;
  }
}
```

4. Prefer `transform` and `opacity` for animations (GPU-accelerated, no layout thrashing)
5. Avoid animating `width`, `height`, `top`, `left`, `margin`, `padding` (trigger layout recalculation)

#### 🟠 Accessibility Changes (ARIA, focus, keyboard)

1. Use semantic HTML first — `<button>` not `<div onClick>`
2. Ensure all interactive elements are focusable and have visible focus styles
3. Add ARIA attributes only when semantic HTML is insufficient
4. Test keyboard navigation order: Tab, Shift+Tab, Enter, Escape, Arrow keys
5. Ensure color is not the only indicator of state (add icons, text, or patterns)

### Step 4: Execute the Edits

Apply edits following these rules:

1. **One logical change per edit** — don't mix unrelated changes in a single edit
2. **Preserve existing code style** — match indentation, quote style, semicolons, etc.
3. **Preserve existing patterns** — if the codebase uses BEM, continue using BEM
4. **Comment non-obvious changes** — if a value or approach isn't self-explanatory, add a brief comment
5. **Clean up dead code** — if you replace a style, remove the old one

### Step 5: Self-Review Checklist

After making all edits, verify each item:

#### CSS Quality
- [ ] No hardcoded colors — use CSS variables or theme tokens
- [ ] No hardcoded font sizes — use the existing type scale
- [ ] No `!important` — fix specificity properly instead
- [ ] No magic numbers — every value should follow the spacing/sizing scale
- [ ] No vendor prefixes (unless the project doesn't use autoprefixer)
- [ ] Shorthand properties used consistently (e.g., `margin: 0 auto` not four separate margin rules)

#### Layout Quality
- [ ] No `position: absolute` without a positioned parent
- [ ] No `z-index` without documenting the stacking context
- [ ] No negative margins unless absolutely necessary (and commented)
- [ ] Flexbox/Grid used instead of floats
- [ ] No fixed widths on text containers (text should reflow)

#### Responsive Quality
- [ ] Base styles work on 320px viewport (narrowest supported mobile)
- [ ] No horizontal scroll on any breakpoint
- [ ] Touch targets are at least 44×44px on mobile
- [ ] Font sizes are at least 16px for body text on mobile
- [ ] Images have max-width: 100% to prevent overflow

#### Accessibility Quality
- [ ] Color contrast meets WCAG AA (4.5:1 text, 3:1 large text / UI components)
- [ ] All interactive elements are keyboard-accessible
- [ ] Focus indicators are visible
- [ ] Heading hierarchy is logical (no skipping levels)
- [ ] Alt text on images (if applicable)

#### Theme Quality
- [ ] Changes work in light mode
- [ ] Changes work in dark mode (if supported)
- [ ] No hardcoded background/text color pairs (use theme-aware variables)

---

## Framework-Specific Patterns

### React / Next.js

- Modify styles via CSS Modules (`styles.className`), styled-components, or Tailwind classes
- For conditional styles, use template literals or `classnames` / `clsx` library
- Keep styles co-located with components when using CSS Modules
- For Next.js app router: global styles go in `app/globals.css`, layout styles in `layout.tsx`

```jsx
// Conditional class example
<div className={`${styles.card} ${isActive ? styles.active : ''}`}>
```

### Vue

- Use `<style scoped>` to keep component styles isolated
- Use `:class` binding for dynamic classes
- Global styles go in main app entry or a global stylesheet
- Use CSS variables for theming, access in `<style>` blocks

### Angular

- Use component-scoped styles via `styleUrls` or inline `styles`
- Use `::ng-deep` sparingly (it's deprecated) — prefer CSS variables for cross-component theming
- Global styles go in `styles.css` / `styles.scss`

### Vanilla HTML/CSS/JS

- Organize CSS with clear section comments
- Use CSS custom properties for reusable values
- Use BEM or a consistent naming convention
- Keep specificity low: prefer classes over IDs, avoid nesting deeper than 3 levels

---

## Common Patterns

### Centering an Element
```css
/* Flex center (most common) */
.parent { display: flex; justify-content: center; align-items: center; }

/* Grid center (simpler for single child) */
.parent { display: grid; place-items: center; }

/* Text center only */
.element { text-align: center; }

/* Block center (for block elements with a set width) */
.element { margin-inline: auto; }
```

### Truncating Text
```css
/* Single line */
.truncate {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

/* Multi-line (e.g., 3 lines) */
.truncate-multi {
  display: -webkit-box;
  -webkit-line-clamp: 3;
  -webkit-box-orient: vertical;
  overflow: hidden;
}
```

### Responsive Typography
```css
/* Fluid typography using clamp */
h1 { font-size: clamp(1.5rem, 4vw, 2.5rem); }
h2 { font-size: clamp(1.25rem, 3vw, 2rem); }
p  { font-size: clamp(0.875rem, 2vw, 1rem); }
```

### Overlay / Modal Backdrop
```css
.overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.5);
  backdrop-filter: blur(4px);
  z-index: 100; /* document in z-index map */
  display: flex;
  justify-content: center;
  align-items: center;
}
```

### Smooth Scroll with Accessibility
```css
html {
  scroll-behavior: smooth;
}

@media (prefers-reduced-motion: reduce) {
  html {
    scroll-behavior: auto;
  }
}
```

### Card Component Pattern
```css
.card {
  background: var(--card-bg);
  border: 1px solid var(--card-border);
  border-radius: var(--radius-md);
  padding: var(--spacing-lg);
  box-shadow: var(--shadow-sm);
  transition: box-shadow 0.2s ease, transform 0.2s ease;
}

.card:hover {
  box-shadow: var(--shadow-md);
  transform: translateY(-2px);
}

@media (prefers-reduced-motion: reduce) {
  .card { transition: none; }
  .card:hover { transform: none; }
}
```

---

## Edge Cases

### CSS Specificity Conflicts
- If your change doesn't take effect, inspect the specificity of competing rules
- Never use `!important` as a fix — restructure selectors instead
- If a third-party library has high specificity, wrap your override in a higher-specificity selector or use a CSS layer (`@layer`)

### Z-Index Wars
- Before adding a `z-index`, check the project for existing z-index values
- If a z-index map/scale exists, use it
- If not, document your z-index choice with a comment:
```css
.modal { z-index: 1000; } /* Modal layer — above nav (100) and below toast (1100) */
```

### SVG Styling
- Inline SVGs can be styled with CSS; `<img>` SVGs cannot
- Use `currentColor` for fill/stroke to inherit the parent's text color
- For icon sizing, set `width` and `height` on the SVG element, not via CSS when possible

### Form Element Styling
- Form inputs have browser-default styles that vary wildly — always reset them
- Style all states: default, focus, hover, disabled, invalid, read-only
- Use `outline` for focus indicators (not `border`, which shifts layout)
- Ensure `<label>` is associated via `for` / `htmlFor` attribute

### Font Loading
- If adding a new font, ensure it loads before rendering (use `font-display: swap`)
- Provide a fallback font stack: `font-family: 'Inter', system-ui, -apple-system, sans-serif;`
- If using Google Fonts via `<link>`, add `preconnect` to `fonts.googleapis.com`

### Scroll Container Issues
- `overflow: hidden` on `<body>` breaks scroll for modals — use `overflow: clip` or manage with JS
- Nested scroll containers can cause "scroll trapping" — be intentional about `overflow` values
- On iOS, `-webkit-overflow-scrolling: touch` is no longer needed (but don't remove if already present)

### RTL (Right-to-Left) Support
- If the project supports RTL languages:
  - Use logical properties: `margin-inline-start` not `margin-left`
  - Use `start`/`end` not `left`/`right` in flexbox (`justify-content: flex-start`)
  - Test with `dir="rtl"` on `<html>`

### CSS Variable Fallbacks
- Always provide fallbacks for CSS variables in case they're undefined:
```css
color: var(--text-primary, #1e1e1e);
```

### Image Handling
- Always set `max-width: 100%; height: auto;` on images in fluid layouts
- Use `aspect-ratio` to prevent layout shifts during loading
- Add `loading="lazy"` for below-the-fold images
- Provide meaningful `alt` text (or `alt=""` for decorative images)

### Print Styles
- If the page may be printed, add `@media print` rules:
  - Hide navigation, footers, interactive elements
  - Set `background: white; color: black;`
  - Remove shadows and gradients

---

## Critical Rules

1. **Never edit a file you haven't read.** Always read the full file first.
2. **Never introduce `!important`.** Fix the specificity properly.
3. **Never hardcode colors or sizes.** Use existing design tokens, CSS variables, or the project's scale.
4. **Never break mobile.** Every change must work at 320px minimum viewport width.
5. **Never ignore dark mode.** If the project supports it, verify your change in both themes.
6. **Always match existing patterns.** If the codebase uses BEM, use BEM. If it uses CSS Modules, use CSS Modules.
7. **Always clean up.** Remove dead CSS when you replace styles. Don't leave orphaned rules.
8. **Always add reduced-motion overrides** when adding animations or transitions.
9. **Always check parent constraints.** A child's layout is governed by its parent — read the parent's styles too.
10. **Never rename existing CSS classes** without grepping for all usages across the entire project.