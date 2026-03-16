# Design Patterns Reference

How to map what you see in a Figma design to the correct component configuration and CSS.

## Design Token Extraction

When you receive Figma design context via `get_design_context`, the response includes a **styles metadata** section listing colors, fonts, and spacing tokens. Extract these and map them to CSS variables on `.c-jobs`:

| What to look for | CSS Variable | How to find it |
|---|---|---|
| Primary brand color (buttons, headings, search bg) | `--color-primary` | The dominant non-white, non-black color used for CTAs and accents |
| Accent/CTA color (search button, apply button) | `--color-accent` | The color used on action buttons — sometimes same as primary |
| Page background | `--color-bg` | The outermost container background (often light gray or white) |
| Text primary | `--color-text` | The main body text color |
| Text secondary/muted | `--color-text-secondary` | Used for labels, metadata, less important text |
| Border color | `--color-border` | Used on card borders, input borders, dividers |
| Heading font family | `font-family` on `.c-jobs` | Listed in styles as the heading/display font |
| Body font family | Secondary font | Sometimes a different family for body text vs headings |

## Layout Pattern Recognition

### Pattern A (two-column) — most common
**How to identify from Figma screenshot:**
- Sidebar visible on the left with filter dropdowns or accordion panels
- Job list takes up the right ~70% of the width
- Sort-by and results header appear above the job list (right side)

### Pattern B (sectioned) — less common
**How to identify from Figma screenshot:**
- Full-width horizontal search bar at the top
- Filters in a subheader row (inline dropdowns across the page, not a sidebar)
- No left/right column split — everything stacks vertically in sections

## Component-to-Design Mapping

### Search Box
**What to look for in Figma:**
- Two input fields (keyword + location) with a search button
- May have labels above inputs
- May have a locate-me icon (crosshair/target) in the location field
- May have a colored background bar behind the search inputs

**Key prop decisions from Figma:**
| Figma observation | Prop setting |
|---|---|
| Inputs + button in one horizontal row | `horizontal="true"` |
| Inputs stacked vertically | `horizontal="false"` |
| Crosshair/target icon in location field | `locate_me="true"` |
| Labels above inputs (e.g., "I'm Looking For") | Set `key_search_label`, `location_search_label` |
| Placeholder text visible in inputs | Set `key_search_placeholder`, `location_search_placeholder` |
| Button text visible | Set `search_button_label` |

**CSS decisions from Figma:**
| Figma observation | CSS approach |
|---|---|
| Dark/colored background behind search | Background color on the search wrapper |
| Rounded/pill inputs | `border-radius` on input elements |
| Square/bordered inputs | `border` with small or no radius |
| Custom search/pin icons | Replace default icons via `::before` pseudo-elements with SVG |

### Filters
**What to look for in Figma:**
- Dropdown buttons labeled with category names (Category, Location, State, City, Country, etc.)
- May show checkbox lists when expanded
- "Clear Filters" link/button
- May appear as a left sidebar (Pattern A) or horizontal row (Pattern A/B)

**Key prop decisions:**
| Figma observation | Implementation |
|---|---|
| Vertical filter list in sidebar | Pattern A left column, accordion-style CSS |
| Horizontal filter row above content | Inline dropdown-style CSS |
| Visible category labels | Set `filter_options` JSON array to match |
| "Clear Filters" text | Set `clear_filter_text` |

### Job Cards
**What to look for in Figma:**
- Job title (always present)
- Location with pin icon
- Apply/View button with specific text
- Additional fields: store name, department, employment type, brand, etc.
- Card styling: bordered, shadow, rounded corners, background color

**Key prop decisions:**
| Figma observation | Prop setting |
|---|---|
| Apply button text (e.g., "Apply Now", "VIEW JOB") | `apply_button_text` |
| Brand/company name row visible | `show_brand="true"` |
| Employment type row visible (Full-Time, Part-Time) | `show_employment_type="true"` |
| Job ID/reference visible | `show_reference="true"` |
| Custom fields (Store, Department, Time, Travel) | `job_card_extra_fields_enabled="true"` + JSON |
| Location format visible (e.g., "City, ST") | Set `street_format` accordingly |

**CSS decisions:**
| Figma observation | CSS approach |
|---|---|
| Cards with box shadow | `box-shadow` on `.results-list__item` |
| Cards with border | `border` on `.results-list__item` |
| Cards with rounded corners | `border-radius` on `.results-list__item` |
| Row layout (title, store, location, type in columns) | Flex row with column widths on `.results-list__item-content` |
| Stacked layout (title above location above type) | Default flex column (most common) |
| Two-column card grid | CSS grid `grid-template-columns: 1fr 1fr` on the card container |

### Sort By
**What to look for in Figma:**
- "Sort by" label with a dropdown value (e.g., "Posting date", "Job Title")
- Usually positioned in a toolbar row above the job list
- May have a styled pill/bordered container

### Pagination
**What to look for in Figma:**
- Page numbers (1, 2, 3, 4, 5...)
- Navigation arrows (chevrons) or text (Next/Previous/First/Last)
- Current page highlighted, bold, or colored differently

**Key prop decisions:**
| Figma observation | Prop setting |
|---|---|
| Arrow/chevron icons for navigation | `useicon="true"` |
| Text labels (First/Previous/Next/Last) | `useicon="false"` |

**CSS decisions:**
| Figma observation | CSS approach |
|---|---|
| Current page has colored background | Background + text color on `.current-page` |
| Current page is bold/colored text only | Font weight + color on `.current-page` |
| Navigation arrows in bordered circles | Border + border-radius on `.page-link-next`, `.page-link-last` |

## Common Pitfalls

| Mistake | Why it's wrong | Correct approach |
|---|---|---|
| Inventing HTML elements not in components.md | The platform produces fixed markup — custom elements won't hydrate | Only use wrapper divs + SSR content from components.md |
| Adding Liquid `{{ }}` or `{% %}` tags | The output is a local simulator — no backend data available | Use hardcoded placeholder text |
| Using unscoped CSS selectors like `input {}` | Will conflict with the host page on the platform | Always scope under `.c-jobs` or component class |
| Grouping all media queries at the bottom | Makes it hard to maintain and debug | Place each `@media` right after its section |
| Skipping hover/focus states | The output must be testable locally for interactivity | Add `:hover`, `:focus`, `:active` for all interactive elements |
| Using generic placeholder text like "Lorem ipsum" | Developers need to see realistic content to judge spacing/layout | Use real job titles, city names, employment types |
| Forgetting `variant_name` and `variant_id` as last props | Platform expects these in a specific position | Always place as the final two `data-react-prop-*` attributes |

## Common Visual Patterns

### Search bar on colored background
Many designs place the search inputs on a brand-colored background bar. Implement this by styling the search wrapper div (not the component itself):
```css
.c-job-main__left-inner .c-jobs-search-wrap,
/* or for horizontal layouts: */
.c-jobs .job-search-header {
  background-color: var(--color-primary);
  padding: 20px 30px;
}
```

### Filter tags / current searches
When the design shows removable tag chips for active filters, include the `jobs-current-searches` component. Look for pill-shaped elements with an "X" close button.

### Results header with bold counts
Most designs show "Showing **1-5** out of **100** Careers". Use the `results_header` prop with HTML:
```
data-react-prop-results_header="Showing &lt;b&gt;{from}-{to}&lt;/b&gt; of &lt;b&gt;{total_jobs}&lt;/b&gt; jobs"
```

### Custom field labels via CSS
When the design shows labeled fields on job cards (e.g., "STORE: Main Street Location"), these are implemented with `job_card_extra_fields` and CSS `::before` pseudo-elements for the labels:
```css
.results-list__custom1::before {
  content: "STORE";
  font-size: 11px;
  text-transform: uppercase;
  color: var(--color-text-secondary);
}
```
