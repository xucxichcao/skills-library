---
name: figma-to-bem-css
description: Implement Figma designs into HTML templates using pure CSS with BEM naming convention. No Tailwind CSS. Framework-agnostic. Scans a user-provided folder for existing CSS classes and HTML patterns to reuse. Use when the user asks to implement a Figma design with pure CSS, BEM, or explicitly says no Tailwind.
---

# Figma to Pure CSS (BEM) Implementation

Implement Figma designs using pure CSS with BEM naming, reusing existing styles and HTML patterns from the codebase. Framework-agnostic - works with any HTML templating system.

## Before Starting

**CRITICAL**: You must scan ONLY the folder the user provides. Do NOT scan code outside that folder.

### Step 1: Scan for Existing CSS

Read all `.css` files in the provided folder. Extract:
- **Block classes** (`.block-name`)
- **Element classes** (`.block-name__element`)
- **Modifier classes** (`.block-name--modifier`)
- **CSS custom properties** (`--color-*`, `--spacing-*`, `--font-*`, etc.)
- **Media queries** / breakpoints
- **Reusable patterns** (typography, colors, spacing, layout)

### Step 2: Scan for Existing HTML Patterns

Read all markup files (`.html`, `.liquid`, `.jsx`, `.vue`, `.hbs`, etc.) in the provided folder. Extract:
- **Section structures** (wrappers, containers, layout patterns)
- **Component patterns** (cards, grids, carousels, accordions)
- **Naming conventions** used in the codebase

### Step 3: Build a Mental Catalog

Before writing any code, build a catalog of:
- Available CSS classes to reuse
- Color values and their class names
- Typography classes and their properties
- Spacing/layout patterns
- Interactive component structures

## BEM Naming Convention

**Always use BEM**:

```
.block {}
.block__element {}
.block--modifier {}
.block__element--modifier {}
```

**Rules**:
- **Block**: Standalone component name (`hero`, `card`, `nav`)
- **Element**: Part of a block, prefixed with `__` (`hero__title`, `card__image`)
- **Modifier**: Variation, prefixed with `--` (`card--featured`, `hero__title--large`)
- Use lowercase with hyphens for multi-word names (`call-to-action`, not `callToAction`)
- Never nest BEM selectors deeper than Block > Element

```css
/* ✅ Correct BEM */
.hero {}
.hero__title {}
.hero__title--accent {}
.hero__description {}
.hero__image-wrapper {}

/* ❌ Wrong - no nested elements */
.hero__content__title {}    /* Use .hero__title instead */
.hero__card__image {}       /* Create a new block: .hero-card__image */
```

## Understanding Responsive Design

You will receive **separate Figma node IDs for each breakpoint**:
- **Mobile**: Default styles
- **Tablet**: `@media (min-width: 768px)` (or as defined in existing CSS)
- **Desktop**: `@media (min-width: 1024px)` (or as defined in existing CSS)

**IMPORTANT**: Check existing CSS for breakpoint values already in use. Use the same breakpoints.

**Implementation approach**:
1. Fetch design context for ALL breakpoints
2. Compare designs to identify what changes
3. Write mobile-first CSS with media query overrides
4. Each breakpoint may differ in: spacing, sizing, layout, typography, visibility

**Text Content Rule**:
If text content differs between breakpoints, **always use the Desktop version text**. Only CSS styling should be responsive, not the actual content.

## Implementation Workflow

### 1. Scan Provided Folder

```
User says: "Implement this Figma design in /path/to/folder"

You must:
1. Read ALL .css files in that folder (and subfolders)
2. Read ALL markup files (.html, .liquid, .jsx, .vue, etc.) in that folder (and subfolders)
3. Build catalog of reusable classes, patterns, variables
4. Do NOT read files outside this folder
```

### 2. Fetch Figma Designs for All Breakpoints

Fetch all three breakpoints:
- Use `user-Figma-get_design_context` for code structure
- Use `user-Figma-get_screenshot` for visual reference
- Compare designs to identify responsive changes

### 3. Match Figma to Existing CSS

Before writing new CSS, check:
1. **Does an existing class match?** → Reuse it
2. **Does a similar pattern exist?** → Extend with a modifier
3. **Is it completely new?** → Create new BEM block

**Priority order**:
1. Reuse existing class as-is
2. Add a BEM modifier to existing block
3. Create a new BEM block following existing naming patterns

### 4. Section Structure

Follow the pattern found in existing HTML. Typical structure:

```html
<section id="section-name" class="section-name">
  <div class="section-name__container">
    <!-- Content here -->
  </div>
</section>
```

**Key points**:
- `<section>` gets the block class
- Inner container handles padding, max-width, layout
- Match padding/layout patterns from existing sections

### 5. Write CSS (Mobile-First)

```css
/* Block */
.core-values {
  background-color: #000;
  color: #fff;
}

/* Container element */
.core-values__container {
  display: flex;
  flex-direction: column;
  gap: 24px;
  padding: 60px 16px;
}

/* Tablet */
@media (min-width: 768px) {
  .core-values__container {
    padding: 60px 48px;
  }
}

/* Desktop */
@media (min-width: 1024px) {
  .core-values__container {
    flex-direction: row;
    align-items: center;
    gap: 40px;
    padding: 60px 80px;
  }
}
```

### 6. Typography

**Check existing CSS** for typography patterns:

```css
/* If existing CSS has: */
.heading-1 { font-size: 34px; font-weight: 500; }
.body-text { font-size: 16px; line-height: 1.5; }

/* Reuse them: */
<h1 class="heading-1">Title</h1>
<p class="body-text">Description</p>
```

**If no match exists**, create BEM element:

```css
.core-values__title {
  font-size: 28px;
  font-weight: 500;
  line-height: normal;
  letter-spacing: 0.5px;
}

@media (min-width: 1024px) {
  .core-values__title {
    font-size: 46px;
  }
}
```

### 7. Colors

**Check existing CSS** for color variables or classes:

```css
/* If existing CSS has variables: */
:root {
  --color-primary: #da291c;
  --color-text-muted: #d9d9d9;
}

/* Use them: */
.hero__accent { color: var(--color-primary); }

/* If existing CSS has utility classes: */
.text-red { color: #da291c; }

/* Reuse them in HTML: */
<span class="hero__accent text-red">Highlighted</span>
```

### 8. Images

```html
<div class="hero__image-wrapper">
  <img 
    src="https://..."
    alt="Descriptive alt text"
    class="hero__image">
</div>
```

```css
.hero__image-wrapper {
  position: relative;
  overflow: hidden;
  border-radius: 40px;
}

.hero__image {
  width: 100%;
  height: 100%;
  object-fit: cover;
}
```

### 9. Grid Layouts

```css
.benefits__grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: 40px;
}

@media (min-width: 768px) {
  .benefits__grid {
    grid-template-columns: repeat(3, 1fr);
  }
}
```

### 10. Interactive Components

**Check existing HTML/CSS** for interactive patterns (carousels, accordions, etc.). Reuse the same markup structure and class names.

If a carousel pattern exists in the scanned folder:
```html
<div class="carousel" id="awards-carousel">
  <div class="carousel__viewport">
    <div class="carousel__track">
      <div class="carousel__slide"><!-- content --></div>
      <div class="carousel__slide"><!-- content --></div>
    </div>
  </div>
  <div class="carousel__controls">
    <button class="carousel__prev" aria-label="Previous">Prev</button>
    <button class="carousel__next" aria-label="Next">Next</button>
  </div>
</div>
```

### 11. Repeated Elements

When Figma shows repeated identical elements (cards, logos, list items):

1. **Identify the repeating unit** from Figma
2. **Write the markup for one item** with proper BEM classes
3. **Duplicate the markup** for each item (or use the project's templating system if available)

```html
<div class="card-grid">
  <!-- Repeat this block for each item -->
  <article class="card-grid__item">
    <img src="..." alt="..." class="card-grid__image">
    <h3 class="card-grid__title">Title</h3>
    <p class="card-grid__description">Description</p>
  </article>

  <article class="card-grid__item">
    <img src="..." alt="..." class="card-grid__image">
    <h3 class="card-grid__title">Title</h3>
    <p class="card-grid__description">Description</p>
  </article>
</div>
```

### 12. Accessibility (WCAG 2.1 AA)

Same requirements regardless of CSS approach:

1. **Semantic HTML**: `<section>`, `<article>`, `<nav>`, `<button>`
2. **Alt text**: Descriptive for meaningful images, empty for decorative
3. **Interactive elements**: `<button>` for actions, `<a>` for navigation
4. **Focus styles**: Always provide visible `:focus` / `:focus-visible` states
5. **Color contrast**: 4.5:1 minimum ratio for text
6. **Keyboard navigation**: All interactive elements reachable via Tab
7. **ARIA**: Use `aria-label`, `aria-expanded`, `aria-hidden` where needed

```css
/* Always include focus styles */
.button:hover,
.button:focus-visible {
  background-color: var(--color-primary);
  outline: 2px solid currentColor;
  outline-offset: 2px;
}
```

## Conversion Checklist

- [ ] **Scan provided folder** for existing CSS and HTML patterns
- [ ] **Build catalog** of reusable classes, variables, patterns
- [ ] Fetch all breakpoint designs (mobile, tablet, desktop)
- [ ] Compare designs to identify responsive changes
- [ ] **Check text content** - if different across breakpoints, use Desktop version
- [ ] **Reuse existing CSS classes** wherever possible
- [ ] **Match existing breakpoint values** from scanned CSS
- [ ] **Identify repeated elements** - write consistent markup for each
- [ ] Use proper BEM naming convention
- [ ] Use proper section structure from existing patterns
- [ ] Write mobile-first CSS with media query overrides
- [ ] Add descriptive alt text to all images
- [ ] Use semantic HTML elements
- [ ] Include `:hover` and `:focus-visible` styles
- [ ] Verify color contrast ratios
- [ ] **Do NOT use any Tailwind CSS classes**

## Anti-Patterns

```html
<!-- ❌ Don't use Tailwind classes -->
<div class="flex flex-col gap-4 px-[1.6rem]">

<!-- ✅ Use BEM classes -->
<div class="hero__container">

<!-- ❌ Don't nest BEM elements -->
<div class="hero__content__title">

<!-- ✅ Flatten to single element level -->
<div class="hero__title">

<!-- ❌ Don't write CSS without scanning first -->
<!-- Always check for existing reusable styles -->

<!-- ❌ Don't invent new patterns when existing ones work -->
<!-- Reuse existing section/container structures -->

<!-- ❌ Don't skip focus styles -->
.button:hover { background: red; }
<!-- ✅ Always pair hover with focus -->
.button:hover,
.button:focus-visible { background: red; }

<!-- ❌ Don't use different text per breakpoint -->
<!-- Use Desktop version text with responsive CSS -->

<!-- ❌ Don't scan outside the provided folder -->
```

## Workflow Summary

1. **Scan folder**: Read ALL CSS and markup files in provided folder only
2. **Build catalog**: Map existing classes, variables, patterns, breakpoints
3. **Fetch designs**: All three Figma breakpoints
4. **Compare designs**: Identify responsive differences
5. **Text content**: If differs, use Desktop version
6. **Match existing**: Reuse CSS classes and HTML patterns first
7. **Create new BEM**: Only for elements with no existing match
8. **Write CSS**: Mobile-first with media queries from existing breakpoints
9. **Repeated elements**: Consistent BEM markup for each item
10. **Accessibility**: Focus states, semantic HTML, alt text, ARIA
11. **Verify**: No Tailwind, proper BEM, all breakpoints covered
