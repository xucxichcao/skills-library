# Figma to Jobs Components

Local simulator for the Paradox platform's Job Component generation. Given Figma design URLs, generates the exact static HTML boilerplate and CSS the platform natively produces — output as a `.liquid` file (pure HTML, no Liquid tags) and a `.css` file. Use this skill whenever the user provides Figma URLs for a career site jobs page, mentions "jobs components", wants to create or implement a new client career site, or asks to build a job listing page from a Figma design.

---

## What This Skill Does

The Paradox platform has a native feature that generates standard HTML/CSS boilerplate for Job Components (search, filters, job list, pagination, etc.). Your local dev environment doesn't have this feature. This skill acts as that simulator — you give it a Figma design, and it produces the exact same HTML structure and CSS the platform would generate, formatted for your local Liquid environment.

The output is **static HTML only** — no `{{ }}` or `{% %}` Liquid tags. The `.liquid` file extension is used because that's what the local template engine expects, but the content is pure HTML with realistic placeholder data. This lets you build, style, and visually test the UI locally before wiring up backend data.

---

## Step-by-step Workflow

### Step 1: Fetch the Figma Designs

Use the Figma MCP `get_design_context` tool to fetch each provided Figma URL. Extract `fileKey` and `nodeId`:
- `figma.com/design/:fileKey/:fileName?node-id=:nodeId` → convert `-` to `:` in nodeId

Fetch **all provided breakpoints** (desktop, tablet, mobile) separately. Each breakpoint drives different CSS media queries in the output. If only some are provided, work with what you have and infer the missing ones.

The Figma MCP response includes:
- **Screenshot** — use to identify layout pattern, component placement, and visual hierarchy
- **Design tokens** (colors, fonts, spacing) — extract these for CSS variables
- **Component code** — reference for understanding the design structure (but generate platform-compliant HTML, not this code)

From each breakpoint, extract:
- **Layout structure**: sidebar vs. inline filters, stacked vs. horizontal search
- **Typography**: font family, size, weight, line-height, letter-spacing, text-transform
- **Colors**: backgrounds, text colors, borders, hover states, accent/brand colors
- **Spacing**: padding, margins, gaps between elements
- **Border treatments**: radius, border-width, border-color
- **Component visibility**: which components appear/disappear at each breakpoint
- **Responsive changes**: how the layout shifts (column→row, width changes, spacing changes)
- **Interactive states**: button hover colors, input focus styles, link underlines
- **Non-component elements**: hero images, headings, decorative separators

**If Figma MCP is unavailable:** If you cannot connect to the Figma MCP tool, ask the user to provide screenshots of each breakpoint instead. You can work from screenshots alone — extract the same information visually (layout, colors, typography, components, spacing). The workflow is identical; you just read the design from images rather than structured Figma data.

### Step 2: Read Reference Files

Read in this order:

1. **`references/components.md`** (read first) — The single source of truth for platform markup. Defines available components, their props, the **exact compiled HTML** each produces, and conditional rendering rules. The platform generates fixed HTML — do not invent markup. You control only:
   - Which components to include and their prop values
   - The layout wrapper HTML around component `<div>` elements
   - Custom static HTML for non-component elements (headings, hero sections)
   - All CSS styling

2. **`references/design-patterns.md`** (read second) — How to map Figma visual elements to component props and CSS. Covers design token extraction, layout recognition, component mapping tables, and common pitfalls.

3. **`references/common.css`** (scan for awareness) — Base styles the platform always loads. Your CSS layers on top. Here's what common.css already handles so you know what NOT to re-declare:
   - Autocomplete dropdown positioning and styling
   - Keyword search icon container positioning (`position: absolute; right: 0`)
   - Location search icon container and commute time button
   - Commute time modal layout, form groups, buttons, toggle switch
   - Sort-by and radius dropdown containers, borders, expand/collapse
   - Similar jobs card layout and styling
   - Remote badge and commute time badge on job items
   - `#ada-visually-hidden` screen reader element
   - Modal overlay backdrop

   Override these only when the Figma design differs from the defaults.

4. **`references/wrapper-templates.md`** (use during generation) — Copy-paste component wrapper templates with standard props pre-filled. Start from these templates rather than building wrappers from scratch.

### Step 3: Build the HTML Template

Create a `jobs.liquid` file containing **pure static HTML** — no Liquid template syntax.

#### Critical rule: no Liquid tags

The output must contain zero Liquid syntax. No `{{ variable }}`, no `{% if %}`, no `{% render %}`. The `.liquid` extension is required by the local template engine, but the file content is plain HTML with hardcoded placeholder data. This allows visual testing without a backend.

#### Root wrapper

Every implementation starts with this root element:
```html
<section data-component="job" class="c-jobs" id="jobs-list-main-content">
  <!-- Layout content -->
</section>
```

#### DOM hierarchy must match the platform

The platform renders components inside a specific DOM structure. Your HTML must replicate this hierarchy exactly so that CSS scoping and component hydration work correctly when deployed. The nesting order comes from the Figma design's visual hierarchy, mapped through the platform's DOM rules.

#### Layout patterns

Determine from the Figma screenshot:

**Pattern A — Two-column body** (most common):
```html
<section data-component="job" class="c-jobs" id="jobs-list-main-content">
  <!-- Optional: headings, hero images -->
  <div class="c-job-main">
    <div class="c-job-main__left">
      <div class="c-job-main__left-inner">
        <!-- search-box, current-searches, filters -->
      </div>
    </div>
    <div class="c-job-main__right">
      <!-- sorting toolbar (results-header + radius + sort-by) -->
      <!-- current-location, jobs-list-only, pagination -->
    </div>
  </div>
</section>
```

The **sorting toolbar** row (results-header + radius + sort-by) needs its own wrapper div for flex layout:
```html
<div class="c-job-main__right">
  <div class="c-job-toolbar">
    <!-- results-header wrapper -->
    <!-- radius wrapper -->
    <!-- sort-by wrapper -->
  </div>
  <!-- current-location, jobs-list-only, pagination -->
</div>
```
Style `c-job-toolbar` as `display: flex; align-items: center; justify-content: space-between;` to place these three components in a row.

**Pattern B — Sectioned layout**:
```html
<div data-component="job" class="c-jobs" id="jobs-list-main-content">
  <div class="c-jobs__header-wrap">
    <div class="c-jobs__header"><!-- heading, search-box --></div>
  </div>
  <div class="c-jobs__subheader-wrap">
    <div class="c-jobs__subheader"><!-- current-searches, inline filters --></div>
  </div>
  <div class="c-jobs__content">
    <div class="c-jobs__main-area"><!-- toolbar, jobs-list --></div>
  </div>
  <div class="c-jobs__footer-wrap">
    <div class="c-jobs__footer"><!-- pagination --></div>
  </div>
</div>
```

#### Component wrapper format

Each platform component is a `<div>` with specific data attributes. Follow this format exactly:

```html
<div class="{outer-class} {variant}"
     data-react-component="{component-name}"
     data-guid="{uuid}"
     data-variant="{variant}"
     data-react-prop-classname="{css-class}"
     {... component-specific props ...}
     data-react-prop-variant_name="{variant}"
     data-react-prop-variant_id="{same-uuid}">
  <!-- Platform SSR content from components.md -->
</div>
```

- `data-guid` — unique UUID v4 per component instance
- `data-react-prop-classname` — the CSS class on the rendered root element (your main styling hook)
- `data-react-prop-variant_name` and `variant_id` — always the last two props

**Inside** each wrapper, include the full SSR HTML the platform produces (documented in `references/components.md`). This is what the developer sees locally before hydration.

Use realistic placeholder data that matches what the Figma design shows. If the Figma shows specific job titles and locations, use those. Otherwise, use plausible defaults:
- Job titles: "Benefits Director", "Store Manager", "Sales Associate", "Wave Planner"
- Locations: "San Francisco, CA", "New York, NY", "Chicago, IL"
- Brands: Use the client brand name if known
- Employment types: "Full-Time", "Part-Time"
- Include 3-5 job card items to represent a realistic list view

See `references/wrapper-templates.md` for ready-to-use templates with all standard props.

#### Prop configuration

Set props based on the Figma design. Key decisions:

| Component | Figma observation | Prop |
|---|---|---|
| search-box | Inputs in one row | `horizontal="true"` |
| search-box | Inputs stacked | `horizontal="false"` |
| search-box | Placeholder text | `key_search_placeholder`, `location_search_placeholder` |
| search-box | Labels above inputs | `key_search_label`, `location_search_label` |
| search-box | Locate-me icon | `locate_me="true"` |
| search-box | Button text | `search_button_label` |
| jobs-filter | Category labels | `filter_options` JSON |
| jobs-filter | Reset text | `clear_filter_text` |
| jobs-list-only | Button text | `apply_button_text` |
| jobs-list-only | Brand row visible | `show_brand="true"` |
| jobs-list-only | Employment type visible | `show_employment_type="true"` |
| jobs-list-only | Custom fields | `job_card_extra_fields_enabled` + JSON |
| jobs-list-header | Results text | `results_header` with `{from}`, `{to}`, `{total_jobs}` |
| jobs-pagination | Arrow icons | `useicon="true"` |

#### Boilerplate props

Always include these standard props — they're identical in every platform output.

**On search-box:**
```
data-react-prop-search_mode="undefined"
data-react-prop-ask_for_location_permission="true"
data-react-prop-location_permission_modal_header="Oops! We couldn't get your location."
data-react-prop-location_permission_modal_text="Please turn on location services in your browser settings or search by zipcode."
data-react-prop-disable_location_type_ahead_google_search="false"
data-react-prop-location_type_ahead_source="[&quot;job_locations&quot;]"
data-react-prop-enable_auto_complete_job_titles="false"
data-react-prop-href="/"
data-react-prop-advanced_commute_time_settings="{&quot;commute_time_button_label&quot;:&quot;Commute&quot;,&quot;selected_commute_time_from_label&quot;:&quot;From&quot;,&quot;selected_commute_time_to_label&quot;:&quot;to jobs within&quot;,&quot;model_header_label&quot;:&quot;Commute Time&quot;,&quot;location_search_label&quot;:&quot;Your Location&quot;,&quot;location_search_placeholder&quot;:&quot;Enter your address&quot;,&quot;duration_header&quot;:&quot;Duration (minutes)&quot;,&quot;transportation_type_header&quot;:&quot;Transportation Type&quot;,&quot;car_transportation_label&quot;:&quot;Car&quot;,&quot;train_transportation_label&quot;:&quot;Transit&quot;,&quot;person_transportation_label&quot;:&quot;Walk&quot;,&quot;bicycle_transportation_label&quot;:&quot;Bicycle&quot;,&quot;traffic_estimation_toggle_label&quot;:&quot;Include traffic estimation&quot;,&quot;confirm_button_text&quot;:&quot;Search&quot;,&quot;cancel_button_text&quot;:&quot;Cancel&quot;,&quot;location_not_exist_message&quot;:&quot;Sorry, we don&#39;t recognize the entered location. Try entering a different location.&quot;}"
```

**On jobs-list-only:**
```
data-react-prop-no_results_line1_text="Sorry, we're not able to load results for your search."
data-react-prop-no_results_line2_text="Please refine your keywords in the search bar above and try again."
data-react-prop-clear_result_suggestions_title_text="Suggestions"
data-react-prop-clear_result_suggestions_line1_text="Try different keywords"
data-react-prop-clear_result_suggestions_line2_text="Make sure everything is spelled correctly"
data-react-prop-clear_result_suggestions_line3_text="If possible, consider trying jobs at a different location, perhaps another nearby city."
data-react-prop-clear_result_suggestions_line4_text="Try clearing your search filters and starting over."
```

### Step 4: Write the CSS

Create a `jobs.css` file. This CSS layers on top of `references/common.css` (always loaded by the platform). Your CSS handles layout, branding, responsive behavior, and interactive states.

#### CSS scoping

All custom styles are scoped under `.c-jobs` to prevent conflicts with the host page when deployed to the platform. Component-specific styles use the `data-react-prop-classname` value as the selector root. This scoping is required by the platform — do not use unscoped selectors.

```css
/* Correct — scoped */
.c-jobs .c-jobs-search .keyword-search__input { ... }
.c-jobs .results-list__item-title a { ... }

/* Wrong — would conflict with host page */
input { ... }
a { ... }
```

#### CSS file structure

```css
/* 1. HIDE DEFAULTS + RESET */
#jobs-list, #footerpowered { display: none; }
.c-jobs { width: 100%; box-sizing: border-box; }
.c-jobs * { box-sizing: border-box; }

/* 2. CSS VARIABLES (from Figma design tokens) */
.c-jobs {
  --color-primary: /* extract from Figma */;
  --color-accent: /* extract from Figma */;
  --color-text: /* extract from Figma */;
  --color-text-secondary: /* extract from Figma */;
  --color-bg: /* extract from Figma */;
  --color-border: /* extract from Figma */;
}

/* 3. ROOT + LAYOUT */
/* 4. NON-COMPONENT ELEMENTS */
/* 5. SEARCH BOX */
/* 6. CURRENT SEARCHES */
/* 7. FILTERS */
/* 8. SORTING TOOLBAR */
/* 9. CURRENT LOCATION */
/* 10. JOBS LIST */
/* 11. PAGINATION */
/* 12. COMMUTE TIME MODAL */
/* 13. NO RESULTS / SUGGESTIONS */
```

#### Responsive breakpoints

Interpret each Figma breakpoint into CSS media queries. Use mobile-first:

- **Mobile** (default): no media query — everything stacked vertically
- **Tablet**: `@media screen and (min-width: 768px)`
- **Desktop**: `@media screen and (min-width: 1024px)` or `(min-width: 1280px)`

Place responsive overrides **immediately after** each section's base styles, not grouped at the bottom. This keeps related styles together and makes debugging easier.

```css
/* === LAYOUT === */
.c-jobs .c-job-main {
  display: flex;
  flex-direction: column;
  gap: 24px;
}

@media screen and (min-width: 768px) {
  .c-jobs .c-job-main {
    flex-direction: row;
    gap: 40px;
  }
  .c-jobs .c-job-main__left { width: 30%; }
  .c-jobs .c-job-main__right { width: 70%; }
}

@media screen and (min-width: 1024px) {
  .c-jobs .c-job-main {
    gap: 60px;
    max-width: 1440px;
    margin: 0 auto;
  }
}
```

Compare each Figma breakpoint screenshot to identify what changes between sizes:
- Column direction changes (stacked → side-by-side)
- Width/max-width changes
- Padding/margin changes
- Font size changes
- Component visibility (e.g., filter toggle button on mobile, accordion on desktop)
- Gap/spacing changes

#### Interactive states

Every interactive element needs CSS pseudo-class styles for local testing. Extract these from the Figma design (look for hover/active/focus states in component variants).

**Buttons** (search, apply, filter reset):
```css
.c-jobs-search__button-search:hover {
  opacity: 0.9;
  /* or background-color change from Figma hover state */
}
.c-jobs-search__button-search:active {
  transform: scale(0.98);
}

.c-jobs .results-list__item-apply:hover {
  background-color: /* darker shade of apply button color */;
}
```

**Inputs** (search keyword, location):
```css
.c-jobs-search .keyword-search__input input:focus {
  outline: none;
  border-color: var(--color-primary);
  box-shadow: 0 0 0 2px rgba(var(--color-primary-rgb), 0.2);
}
.c-jobs-search .location-search__input input:focus {
  outline: none;
  border-color: var(--color-primary);
}
```

**Links** (job titles, pagination, clear filters):
```css
.c-jobs .results-list__item-title a:hover {
  color: var(--color-accent);
  text-decoration: underline;
}
.c-jobs .pagination .page-link:hover {
  background-color: var(--color-bg);
}
```

**Filter accordions**:
```css
.c-jobs-filter .filter-category__header:hover {
  background-color: rgba(0, 0, 0, 0.03);
}
.c-jobs-filter .filter-category__header::after {
  transition: transform 0.3s;
}
.c-jobs-filter .filter-category__header[aria-expanded="true"]::after {
  transform: rotate(180deg);
}
```

**Keyboard accessibility** (always include):
```css
.c-jobs a:focus-visible,
.c-jobs button:focus-visible,
.c-jobs input:focus-visible {
  outline: 3px solid var(--color-primary);
  outline-offset: 2px;
}
```

### Step 5: Verify Completeness

Before delivering, check every acceptance criterion:

**Platform Parity:**
- [ ] Output is pure static HTML — zero `{{ }}` or `{% %}` Liquid tags
- [ ] SSR content inside each wrapper matches `references/components.md` exactly
- [ ] All components visible in the Figma design are present in the HTML
- [ ] Realistic placeholder data is used (job titles, locations, not "Lorem ipsum")

**DOM Hierarchy:**
- [ ] Layout skeleton matches the identified pattern (A or B)
- [ ] Component nesting order follows the Figma visual hierarchy
- [ ] `data-react-component`, `data-guid`, `data-variant` attributes on every component wrapper
- [ ] Boilerplate props included on search-box and jobs-list-only
- [ ] `variant_name` and `variant_id` are the last two props on every wrapper
- [ ] UUIDs are unique across all component instances

**CSS Scoping:**
- [ ] All selectors scoped under `.c-jobs` or component-specific classes
- [ ] `#jobs-list, #footerpowered { display: none }` at top of CSS
- [ ] CSS variables defined for brand colors from the Figma design
- [ ] No unscoped element selectors (`input`, `a`, `button` etc.)

**Responsive Breakpoints:**
- [ ] Mobile styles are the base (no media query)
- [ ] Tablet breakpoint (`min-width: 768px`) addresses layout changes
- [ ] Desktop breakpoint (`min-width: 1024px` or `1280px`) addresses final layout
- [ ] Media queries placed inline with their section, not grouped at bottom
- [ ] Each Figma breakpoint's differences are reflected in CSS

**Interactive States:**
- [ ] `:hover` styles on all buttons (search, apply, filter headers)
- [ ] `:focus` / `:focus-visible` styles on all inputs and buttons
- [ ] `:hover` styles on all links (job titles, pagination, clear filters)
- [ ] `:active` styles on primary action buttons
- [ ] Transition properties for smooth state changes
- [ ] Filter accordion chevron rotation on expand/collapse

---

## Output Files

Deliver exactly two files:

1. **`jobs.liquid`** — Static HTML (no Liquid tags) with platform-compliant component wrappers and SSR content
2. **`jobs.css`** — Scoped CSS with responsive breakpoints and interactive states, layering on top of common.css

---

## Reference Files

Read before generating output:

- **`references/components.md`** — Platform component API (props, compiled HTML, conditional rendering)
- **`references/common.css`** — Platform base styles (always loaded first)
- **`references/wrapper-templates.md`** — Ready-to-use component wrapper templates with standard props
- **`references/design-patterns.md`** — Figma-to-component mapping patterns, design token extraction, and CSS strategies
