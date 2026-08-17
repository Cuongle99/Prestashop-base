# Style Guide — PrestaShop Base

## SCSS / CSS rules

### 1. Source of truth

- **SCSS is the only source of truth for styling.**
- Only edit existing **`.scss` files**.
- **Do not edit `.css` files directly.** CSS is generated automatically from SCSS.
- **Do not create new `.css` files** for styling.
- **Do not create new `.scss` files** unless there is a strong architectural reason; first reuse or reorganize an existing SCSS file.
- When DevTools points to a rule in `theme.css`, find and edit the corresponding SCSS source instead of changing the generated CSS.
- After changing SCSS, run the project's normal build process so the generated CSS is updated.

### 2. Avoid duplicated CSS — mandatory

Before adding a new rule, always check whether the component or property already exists elsewhere.

Check:

1. Does this class/component already have the required style?
2. Is the property already declared by another selector?
3. Can an existing BEM modifier be reused?
4. Can an existing variable/design token be reused?
5. Can an existing mixin or utility be reused?
6. Can the existing rule be refactored instead of adding an override?

Do **not** create duplicate rules just to increase specificity.

Bad:

```scss
.thumbnail-container {
  position: relative;
  overflow: hidden;
  border-radius: clamp(24px, 2vw, 32px);
}

.thumbnail-container.product-image {
  position: relative;
}
```

`position: relative` is already defined, so the second rule is unnecessary.

Also avoid:

```scss
.product-style-1 .thumbnail-container {
  position: relative;
}

.product-style-2 .thumbnail-container {
  position: relative;
}
```

Prefer one shared rule:

```scss
.thumbnail-container {
  position: relative;
  overflow: hidden;
  border-radius: clamp(24px, 2vw, 32px);
}
```

If a variant really needs a different value, only define the property that changes:

```scss
.product-style-1 .thumbnail-container {
  border-radius: 16px;
}
```

**Rule:** reuse first, refactor second, add new CSS last.

### 3. BEM naming

Use BEM for custom components:

- Block: `.product-card`
- Element: `.product-card__image`
- Modifier: `.product-card--featured`
- Element modifier: `.product-card__button--primary`

Rules:

- Lowercase class names.
- Separate words with `-`.
- Elements use `__`.
- Modifiers use `--`.
- Do not create deeply nested BEM such as `.product-card__content__title`.
- If an element is actually an independent component, create a new block.
- Prefer semantic names over visual names such as `.red`, `.big`, `.left` or `.box`.

### 4. Selectors and specificity

Prefer simple class selectors:

```scss
.product-card {
  ...
}

.product-card__title {
  ...
}
```

Avoid deep selectors:

```scss
#content .product-list .product-card div span {
  ...
}
```

Rules:

- Prefer class selectors.
- Avoid IDs for styling.
- Avoid unnecessary tag selectors.
- Keep descendant nesting to roughly 2–3 levels maximum.
- Do not create high-specificity selectors merely to override another rule.
- Fix the source rule when possible instead of stacking overrides.
- Avoid `!important` unless required for a genuine third-party/vendor conflict.

### 5. SCSS nesting

Keep nesting shallow and readable.

Good:

```scss
.product-card {
  display: flex;

  &__title {
    margin: 0;
  }

  &:hover {
    ...
  }

  &:focus-within {
    ...
  }
}
```

Avoid deeply nested selectors.

### 6. Existing SCSS architecture

The base already contains `_dev/css` with variables, Bootstrap and application styles.

Prefer the existing architecture instead of creating another styling layer.

Conceptually keep this order:

1. Variables / settings
2. Functions / mixins / tools
3. Generic / reset
4. Elements
5. Layout / objects
6. Components
7. Utilities
8. Overrides / vendor fixes

Do not move a small component into a new file just to avoid editing an existing SCSS file.

### 7. Variables / design tokens

Reuse existing tokens before introducing new values.

Prefer:

```scss
color: var(--color-text);
background: var(--color-surface);
border-color: var(--color-border);
```

Avoid repeating the same raw color, spacing, radius or breakpoint throughout the codebase.

Use `clamp()`, `min()`, `max()` and `minmax()` for fluid values where appropriate.

### 8. Responsive

Use mobile-first CSS.

Prefer fluid layouts over unnecessary breakpoints.

```scss
.product-grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: clamp(16px, 2vw, 32px);

  @media (min-width: 768px) {
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }

  @media (min-width: 1024px) {
    grid-template-columns: repeat(4, minmax(0, 1fr));
  }
}
```

Do not add a breakpoint just to fix a small spacing issue if a fluid value can solve it.

### 9. Layout

Prefer:

1. CSS Grid for page and collection layouts.
2. Flexbox for alignment and component layouts.
3. `gap` for spacing between children.
4. Containers with `max-width` for readable content widths.
5. Logical properties such as `margin-inline` and `padding-inline` where appropriate.

Avoid `float`, negative margins and absolute positioning when Grid/Flexbox can solve the layout cleanly.

### 10. Typography

- Keep typography consistent through existing tokens.
- Use semantic headings (`h1`–`h6`).
- Avoid arbitrary font sizes per component.
- Prefer fluid typography where appropriate.

```scss
.heading-xl {
  font-size: clamp(2rem, 4vw, 4rem);
  line-height: 1.1;
}
```

### 11. States and accessibility

Interactive components should support the states they need:

- default
- hover
- focus-visible
- active
- disabled
- loading
- selected/current
- error

Always keep keyboard focus visible:

```scss
.button:focus-visible {
  outline: 2px solid currentColor;
  outline-offset: 3px;
}
```

Do not remove focus indicators without replacing them.

Support `prefers-reduced-motion` for meaningful animations.

### 12. Animation / performance

- Prefer animating `transform` and `opacity`.
- Avoid unnecessary animation of `width`, `height`, `top` and `left`.
- Keep transitions short and purposeful.
- Avoid expensive effects on large lists.
- Do not add JavaScript when native CSS can solve the interaction.

### 13. Images / media

- Use semantic HTML for meaningful content.
- Use `aspect-ratio` to prevent layout shift.
- Use `object-fit` for controlled media boxes.

```scss
.product-card__media {
  aspect-ratio: 1 / 1;
  overflow: hidden;
}

.product-card__image {
  width: 100%;
  height: 100%;
  object-fit: cover;
}
```

### 14. PrestaShop / Smarty

Keep responsibilities separated:

- Smarty `.tpl`: markup and presentation data.
- SCSS: styling.
- JavaScript: interaction/behavior.

Do not put CSS inline in `.tpl` unless there is a specific technical reason.

When styling third-party modules, scope overrides to the smallest relevant component/page area. Do not modify vendor code when an application-level override is possible.

### 15. Bootstrap / vendor code

The repository contains Bootstrap/vendor SCSS.

- Do not modify vendor code just to customize a project component.
- Override it in the application layer.
- Do not rely excessively on Bootstrap's internal DOM structure.
- Do not introduce another dependency for a small UI that can be implemented with existing CSS/JS.

### 16. Property order

Keep declarations consistent:

1. Positioning / layout
2. Box model
3. Typography
4. Visual properties
5. Effects / transitions

Example:

```scss
.product-card {
  position: relative;
  display: flex;
  gap: 16px;
  width: 100%;
  padding: 16px;

  font-size: 1rem;
  line-height: 1.5;

  color: var(--color-text);
  background: var(--color-surface);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-md);

  transition: transform 180ms ease;
}
```

### 17. Comments

Comments should explain **why**, not what obvious code does.

Good:

```scss
// Keep this offset because the sticky header overlaps the anchor target.
```

Avoid comments such as:

```scss
// Set margin to 20px
margin: 20px;
```

### 18. Utilities

Use utility classes only for genuinely reusable patterns.

Examples:

```scss
.u-visually-hidden { ... }
.u-text-center { ... }
.u-hidden { ... }
```

Do not create one-off utility classes for a single component value.

### 19. Style change workflow

Before modifying a component:

1. Find the HTML/Smarty class being rendered.
2. Search all existing SCSS rules for that class.
3. Check for duplicate selectors and duplicate properties.
4. Identify the original/source rule that should be changed.
5. Reuse existing variables, mixins, utilities and component styles.
6. Refactor an existing rule when possible.
7. Only add a new rule when the existing architecture cannot satisfy the requirement.
8. Edit **SCSS only**.
9. Build SCSS to regenerate CSS.
10. Inspect DevTools and verify that unnecessary duplicate rules were not introduced.
11. Test mobile, tablet and desktop.
12. Check other components using the same class before committing.

### 20. Before commit

- [ ] Styling changes are made only in existing `.scss` files.
- [ ] No new `.css` file was created.
- [ ] No generated `.css` file was edited manually.
- [ ] No new `.scss` file was created without a strong architectural reason.
- [ ] Existing styles were searched before adding new rules.
- [ ] No duplicate selector/property was introduced.
- [ ] Existing BEM blocks/elements/modifiers were reused where possible.
- [ ] Existing variables/tokens/mixins were reused where possible.
- [ ] No unnecessary `!important` was added.
- [ ] Selector specificity remains low and predictable.
- [ ] Responsive behavior was checked.
- [ ] Accessibility/focus states were preserved.
- [ ] SCSS was compiled and generated CSS is up to date.
- [ ] DevTools does not show avoidable duplicate rules.

## Priority

When rules conflict, prioritize:

1. Accessibility
2. Semantic HTML
3. Reuse existing styles
4. Avoid duplication
5. Maintainability
6. Performance
7. Responsive behavior
8. Design consistency
9. Visual preference

**Core principle:** Do not add CSS just because it makes the current screen look correct. First find the existing source rule and reuse or refactor it. Keep SCSS clean and keep generated CSS as a build artifact.