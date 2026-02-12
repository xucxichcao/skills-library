---
name: tailwind-css-audit
description: Audit Tailwind CSS classes in Liquid/HTML files against a bundled CSS file and add missing utility classes. Use when the user provides a Liquid/HTML file and a bundled TailwindCSS file and wants to find and fix missing CSS classes.
---

# Tailwind CSS Audit

Scan a Liquid/HTML file for Tailwind CSS classes, compare against a bundled CSS file, and add any missing utility rules.

## Inputs

The user provides two files:
1. **HTML/Liquid file** — contains `class="..."` attributes with Tailwind classes
2. **Bundled CSS file** — a pre-compiled Tailwind CSS output (e.g., `src/pages/tailwind.css`)

## Workflow

### Step 1: Read the Tailwind v4 config

Read `src/styles/tailwind.css` to understand:
- **Theme variables** (`@theme` block) — custom colors, spacing, fonts
- **Custom utilities** (`@utility` blocks) — project-specific utilities like `text-shadow-outer-medium`, `header-primary`, etc.
- **Custom variants** (`@custom-variant` blocks) — `hocus`, `current`, etc.

### Step 2: Extract classes from the HTML/Liquid file

Parse all `class="..."` attributes. For each class string, split by whitespace to get individual classes.

Handle these patterns:
- Standard utilities: `flex`, `hidden`, `bg-white`, `text-center`
- Responsive variants: `md:flex`, `lg:px-8` — prefix before `:`
- State variants: `hover:bg-blue-1`, `focus:outline-none`, `group-hover:opacity-100`
- Custom variants: `hocus:text-white`, `current:font-bold`
- Negative values: `-z-20`, `-translate-x-1/2`
- Arbitrary values: `text-[8.8rem]`, `h-[68.2rem]`, `bg-[#003963]`, `from-black/40`
- Fraction values: `w-1/2`, `-translate-y-1/2`
- Compound classes with `/` opacity: `from-black/40`, `to-transparent`

Also check for classes in Liquid conditionals, e.g., `class="{% if condition %}active{% endif %} flex"`.

### Step 3: Convert class names to CSS selectors and search

For each extracted class, convert to a CSS selector by:
1. Escaping special characters: `[`, `]`, `.`, `/`, `%`, `:` → prefix with `\` in CSS
2. For responsive variants like `md:flex`, the selector is `.md\:flex` inside a `@media` block
3. For state variants like `hover:bg-blue-1`, the selector is `.hover\:bg-blue-1:hover`

**Search the bundled CSS** using Grep for the escaped selector. A class is present if its selector appears in the bundled CSS file.

### Step 4: Generate missing CSS rules

For each missing class, generate the correct CSS rule. Use the reference below.

**Order of priority for generating rules:**

1. **Custom utilities from config** — If the class matches a `@utility` in `src/styles/tailwind.css`, copy its definition
2. **Standard Tailwind v4 utilities** — Use the mapping in [reference.md](reference.md)
3. **Arbitrary value utilities** — Parse the bracket value and apply to the correct property

### Step 5: Add missing rules to the bundled CSS — in correct Tailwind order

**Do NOT append to the end of the file.** Insert each missing rule at the correct position following the Tailwind v4 utility ordering used in the bundled CSS.

#### Bundled CSS file structure (top to bottom)

1. `@layer properties` — CSS custom property fallbacks
2. `:root, :host` — Theme variables
3. Preflight/reset — Base element styles (up to `[hidden]` rule)
4. **Utility classes** — in the category order below
5. **State variant utilities** — `group-*`, `visited:`, `hover:`, `focus:`, `disabled:`, `hocus:`, `current:`
6. **Responsive `@media` blocks** — `md:`, `lg:`, etc.
7. `@property` declarations — at the very end

#### Utility category order (insert within this sequence)

When adding a missing utility, find the correct category section and insert alphabetically within it:

| Order | Category | Example selectors |
|-------|----------|-------------------|
| 1 | Visibility | `.invisible`, `.visible` |
| 2 | Position | `.absolute`, `.fixed`, `.relative`, `.sticky` |
| 3 | Inset / TRBL | `.inset-0`, `.top-0`, `.right-0`, `.bottom-0`, `.left-0` |
| 4 | Z-index | `.-z-20`, `.z-1`, `.z-10` |
| 5 | Float | `.float-right` |
| 6 | Container | `.container` |
| 7 | Margin | `.m-*`, `.mx-*`, `.my-*`, `.mt-*`, `.mb-*`, `.ml-*`, `.-ml-*` |
| 8 | Display | `.block`, `.flex`, `.grid`, `.hidden`, `.inline`, `.table` |
| 9 | Aspect ratio | `.aspect-square` |
| 10 | Size | `.size-*` |
| 11 | Height | `.h-*`, `.max-h-*`, `.min-h-*` |
| 12 | Width | `.w-*`, `.max-w-*` |
| 13 | Flex | `.flex-1`, `.shrink-*`, `.grow`, `.basis-*` |
| 14 | Transform | `.translate-*`, `.rotate-*`, `.scale-*`, `.transform` |
| 15 | Cursor | `.cursor-pointer` |
| 16 | Resize | `.resize` |
| 17 | List style | `.list-inside`, `.list-disc`, `.list-none` |
| 18 | Grid | `.grid-flow-*`, `.grid-cols-*`, `.grid-rows-*` |
| 19 | Flex direction | `.flex-col`, `.flex-row` |
| 20 | Align items | `.items-center`, `.items-start` |
| 21 | Justify | `.justify-between`, `.justify-center`, `.justify-start` |
| 22 | Gap | `.gap-*`, `.gap-x-*`, `.gap-y-*` |
| 23 | Self alignment | `.self-center` |
| 24 | Overflow | `.overflow-auto`, `.overflow-hidden` |
| 25 | Border radius | `.rounded-*` |
| 26 | Border | `.border`, `.border-t`, `.border-b-*`, `.border-solid`, `.border-{color}` |
| 27 | Background color | `.bg-*` |
| 28 | Gradient | `.bg-gradient-*`, `.from-*`, `.via-*`, `.to-*` |
| 29 | Object fit/position | `.object-cover`, `.object-center`, `.object-top` |
| 30 | Padding | `.p-*`, `.px-*`, `.py-*`, `.pt-*`, `.pr-*`, `.pb-*`, `.pl-*` |
| 31 | Custom text utilities | `.card-title`, `.header-*`, `.paragraph`, `.new-pg` |
| 32 | Text align | `.text-center`, `.text-left`, `.text-right` |
| 33 | Font size | `.text-4xl`, `.text-lg`, `.text-[*]` |
| 34 | Line height | `.leading-*` |
| 35 | Font weight | `.font-bold`, `.font-semibold`, `.font-extrabold` |
| 36 | Letter spacing | `.tracking-*` |
| 37 | Whitespace | `.whitespace-nowrap` |
| 38 | Text color | `.text-black`, `.text-white`, `.text-blue-*` |
| 39 | Text transform | `.lowercase`, `.uppercase`, `.capitalize` |
| 40 | Font style | `.italic`, `.not-italic` |
| 41 | Text decoration | `.underline`, `.no-underline` |
| 42 | Opacity | `.opacity-*` |
| 43 | Box shadow | `.shadow`, `.shadow-md` |
| 44 | Filter | `.invert`, `.filter`, `.blur-*` |
| 45 | Backdrop filter | `.backdrop-blur-*` |
| 46 | Transition | `.transition`, `.transition-all`, `.transition-colors` |
| 47 | Duration | `.duration-*` |
| 48 | Timing function | `.ease-in-out`, `.ease-out` |
| 49 | Custom utilities | `.text-shadow-*`, `.h-hero`, `.allegheny-title` |

#### How to insert

1. **Read the bundled CSS** to find the neighboring rules in the same category
2. **Insert after** the last existing rule in the same category, or **before** the first rule of the next category if the category doesn't exist yet
3. Within a category, maintain **alphabetical order** by selector name
4. **State variants** (`hover:`, `focus:`, `group-*:`, `hocus:`, `current:`, `visited:`, `disabled:`) go after all plain utilities, before `@media` blocks
5. **Responsive variants** go inside the matching `@media` block, or create one if missing:
   - `sm:` → `@media (min-width: 40rem)`
   - `md:` → `@media (min-width: 48rem)`
   - `lg:` → `@media (min-width: 64rem)`
   - `xl:` → `@media (min-width: 80rem)`
   - `2xl:` → `@media (min-width: 96rem)`
   Inside each `@media` block, follow the same category order as plain utilities

### Step 6: Report

After adding missing rules, output a summary:
- Total classes found in HTML/Liquid
- Classes already present in bundled CSS
- Classes added (list each with its generated CSS)
- Classes that could not be resolved (if any)

## Important Notes

- **Do NOT remove** any existing rules from the bundled CSS
- **Preserve formatting** — match the indentation style of the existing file (2-space indent)
- **Escaped selectors** — Tailwind v4 bundled output escapes special chars: `.text-\[8\.8rem\]`, `.bg-black\/40`
- When a class uses a theme variable (e.g., `bg-blue-2`), reference it as `var(--color-blue-2)` in the generated CSS
- For gradient utilities (`from-`, `via-`, `to-`), use the `--tw-gradient-*` custom properties pattern
- For `size-full`, output both `width: 100%; height: 100%`
- For `group-hover` variants, the selector pattern is `.group:hover .group-hover\:CLASS`
