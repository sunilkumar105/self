---
name: frontend-post-run
description: Run AFTER completing frontend code changes. Performs verification, documents changes, and provides a summary. Does NOT run or build the app.
---

## When to Use

Use this skill **after** the `frontend-work` skill has finished making all code changes. This skill covers:
- Verifying changes are complete and consistent
- Checking for regressions and unintended side effects
- Documenting what was changed and why
- Providing the user with a clear summary and any risk callouts

---

## Workflow

### Step 1: Verify All Planned Changes Were Made

Go through the change plan from `frontend-tweak` and confirm:
- [ ] Every file in the "files to modify" list was actually modified
- [ ] Every change described in the plan was implemented
- [ ] No files were accidentally skipped
- [ ] No partial changes remain (e.g., a variable was added but never used)

If any planned change was skipped, either:
- Complete it now, or
- Document why it was intentionally skipped

### Step 2: Run the Regression Checklist

For every file that was modified, check:

#### 2a. Style Regressions
- [ ] No orphaned CSS rules (styles for elements that no longer exist)
- [ ] No duplicate CSS rules introduced (same property declared twice in same selector)
- [ ] No broken CSS variable references (variable used but not defined)
- [ ] No broken class name references (class applied in HTML/JSX but not defined in CSS)
- [ ] Shorthand properties don't accidentally override individual properties

#### 2b. Layout Regressions
- [ ] Parent containers still have correct layout model (flex/grid not broken)
- [ ] No new overflow issues introduced (content wider/taller than container)
- [ ] No z-index conflicts with existing stacking contexts
- [ ] Fixed/sticky elements still positioned correctly
- [ ] Scroll behavior unchanged (unless intentionally modified)

#### 2c. Responsive Regressions
- [ ] Changes work at mobile viewport (320px)
- [ ] Changes work at tablet viewport (768px)
- [ ] Changes work at desktop viewport (1024px+)
- [ ] No horizontal scrollbar introduced at any breakpoint
- [ ] Media queries are ordered correctly (mobile-first: smallest to largest)

#### 2d. Theme Regressions
- [ ] Changes work in light mode
- [ ] Changes work in dark mode (if project supports it)
- [ ] No hardcoded colors that bypass the theme system
- [ ] CSS variable fallbacks provided where necessary

#### 2e. Accessibility Regressions
- [ ] Heading hierarchy not disrupted (h1 → h2 → h3, no skips)
- [ ] Tab order not disrupted by DOM changes
- [ ] Focus styles preserved on interactive elements
- [ ] Color contrast still meets WCAG AA standards
- [ ] ARIA attributes still accurate after DOM changes
- [ ] `prefers-reduced-motion` respected for any new animations

#### 2f. Cross-Component Regressions
- [ ] Shared classes/variables used by OTHER components are unchanged (or all consumers updated)
- [ ] Global stylesheet changes don't adversely affect other pages
- [ ] Component props/interfaces unchanged (unless intentionally modified)

### Step 3: Check for Common Mistakes

Scan all modified files for these frequent errors:

| Mistake | How to detect |
|---------|---------------|
| `!important` added | Search for `!important` in modified files |
| Hardcoded color values | Search for `#` hex codes or `rgb()` not in a variable |
| Hardcoded pixel values for font-size | Look for `font-size: XXpx` instead of rem/em/var |
| Missing semicolons (CSS) | Look for lines without `;` before `}` |
| Unclosed brackets | Count `{` vs `}` in modified CSS files |
| Typo in class names | Cross-reference JSX/HTML class usage with CSS definitions |
| Unused imports | Check if any new imports are actually used |
| Console.log left in | Search for `console.log` in modified JS/TS files |

### Step 4: Generate the Change Summary

After verification, produce a summary for the user using this format:

```
✅ CHANGES COMPLETE
═══════════════════

📝 What changed:
   - [File 1]: [Brief description of what was changed]
   - [File 2]: [Brief description of what was changed]

🔍 Verified:
   - [List each verification that passed, e.g., "Responsive: works at 320px, 768px, 1024px"]
   - [List theme verification if applicable]

⚠️ Things to watch (if any):
   - [Any potential side effects the user should manually verify]
   - [Any change that affects other pages/components]

📋 Files modified:
   - [Full file path 1]
   - [Full file path 2]
```

If there are **no risks or side effects**, omit the "Things to watch" section entirely.

### Step 5: Handle Errors or Incomplete Work

If issues were found during verification:

1. **Minor issues** (typos, missing semicolons, orphaned rules):
   - Fix them immediately without asking
   - Document in the summary: "Fixed: [description]"

2. **Medium issues** (style regressions on other breakpoints, theme inconsistencies):
   - Fix them immediately
   - Document in the summary with more detail

3. **Major issues** (broken layout, accessibility regressions, cross-component breakage):
   - Document the issue clearly
   - Provide a recommended fix
   - Ask the user whether to proceed with the fix

---

## Strict Rules — What NOT to Do

These rules are **non-negotiable**. Violating them is a failure:

1. **DO NOT run the app.**
   The user already has the app running locally. Running `npm start`, `npm run dev`, or any dev server command will cause port conflicts.

2. **DO NOT build the project.**
   Do not run `npm run build`, `next build`, or any production build command unless the user explicitly requests it.

3. **DO NOT install dependencies.**
   Do not run `npm install`, `yarn add`, or any package installation command unless the change specifically requires a new package AND the user approves.

4. **DO NOT run tests.**
   Unless the user explicitly requests test execution, do not run the test suite. The user will handle test runs.

5. **DO NOT delete files.**
   If a file should be deleted (e.g., an unused stylesheet), document the recommendation but do not delete without explicit user approval.

6. **DO NOT modify files outside the original change plan** without documenting the deviation and the reason.

---

## Edge Cases

### Partial Completion
If you were interrupted or could only complete part of the work:
- Document exactly what was completed and what remains
- List the remaining items in order of priority
- Ensure the partial changes don't leave the app in a broken state
- If they would, revert the incomplete changes and document what happened

### Multiple Files Changed
If many files were modified:
- Group changes by feature/component in the summary
- Highlight which changes are "safe" (visual-only) vs "risky" (structural/behavioral)
- Suggest the user check specific pages first (the ones most affected)

### Conflicting Changes Detected
If during verification you notice your changes conflict with existing code:
- Do NOT silently merge — document the conflict
- Present both approaches and let the user decide
- If one approach is clearly better, recommend it with reasoning

### User Might Not See the Changes
If the changes are in a component that requires specific navigation to view:
- Tell the user which page/route to navigate to
- Tell the user which specific UI state to trigger (e.g., "click the dropdown", "submit an empty form to see the error state")

### Dark Mode Not Testable
If you can't verify dark mode (no toggle visible, no `prefers-color-scheme` handling):
- Document that dark mode was not verified
- Note which color/background changes might look wrong in dark mode
- Recommend the user manually toggle and verify

### No Design Tokens Found
If the project doesn't use CSS variables, design tokens, or a theme system:
- Use hardcoded values consistent with the existing style
- Document this as a recommendation: "Consider adding CSS variables for colors/spacing to improve maintainability"

---

## Critical Rules

1. **Always verify before summarizing.** Never skip Steps 2–3.
2. **Always produce a summary.** The user should know exactly what changed without reading diffs.
3. **Never leave dead code.** If you replaced styles, the old styles should be removed.
4. **Never leave the app broken.** If partial changes would break the UI, revert them.
5. **Always be honest about risk.** If a change could affect other components, say so.
6. **Respect the user's workflow.** They will run, test, and deploy — your job is to make changes and verify code quality.