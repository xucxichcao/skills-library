---
name: figma-to-liquid
description: Implement Figma designs into Liquid templates using Tailwind CSS v4 with proper responsive behavior, accessibility (WCAG 2.1 AA), and project patterns. Use when implementing designs from Figma, creating new sections, or converting design mockups to code.
---

# Figma to Liquid Implementation

Implement Figma designs into responsive, accessible Liquid templates following project conventions.

## Before Starting

**CRITICAL**: Always read `src/styles/tailwind.css` first to understand:
- Spacing scale (`--spacing` value, typically `1rem = 10px`)
- Available typography utilities (`@utility h1`, `@utility body-s`, etc.)
- Color tokens (`--color-red`, `--color-gray`, etc.)
- Custom variants (`@custom-variant hocus`, `@custom-variant current`)
- Breakpoint structure (check `@media` queries)

## Understanding Responsive Design

You will receive **separate Figma node IDs for each breakpoint**:
- **Mobile**: Base design (no prefix)
- **Tablet**: `md:` prefix design
- **Desktop**: `lg:` prefix design

**Implementation approach**:
1. Fetch design context for ALL breakpoints
2. Compare designs to identify what changes between breakpoints
3. Implement mobile-first, adding `md:` and `lg:` overrides for differences
4. Each breakpoint may have different: spacing, sizing, layout direction, typography, visibility

**Example workflow**:
```
User provides:
- Mobile: node-id=2894-7015
- Tablet: node-id=2894-6594
- Desktop: node-id=2894-6060

You fetch all three, then implement:
<div class="text-[2.8rem] md:text-[3.6rem] lg:text-[4.6rem]">
```

## Implementation Workflow

### 1. Read Theme Configuration

**First step - always read the config**:

```bash
Read src/styles/tailwind.css
```

Extract:
- Spacing scale from `@theme { --spacing: ... }`
- Typography utilities from `@utility h1`, `@utility body-s`, etc.
- Color tokens from `--color-*` variables
- Custom variants from `@custom-variant`
- Breakpoints from `@media (min-width: ...)`

### 2. Fetch Figma Designs for All Breakpoints

User provides node IDs for each breakpoint:

```
Mobile: node-id=XXXX-XXXX
Tablet: node-id=XXXX-XXXX  
Desktop: node-id=XXXX-XXXX
```

**Fetch all three**:
- Use `user-Figma-get_design_context` for code structure
- Use `user-Figma-get_screenshot` for visual reference
- Compare designs to identify responsive changes

### 3. Analyze Responsive Differences

Compare the three designs to identify what changes:

**Common responsive changes**:
- **Layout**: Stack → horizontal, grid columns change
- **Spacing**: Gaps, padding, margins increase
- **Typography**: Font sizes grow, line heights adjust
- **Sizing**: Fixed widths → flexible, heights scale
- **Visibility**: Elements hide/show at breakpoints
- **Positioning**: Absolute → relative, order changes
- **Text content**: Wording, length, or line breaks differ

**IMPORTANT - Text Content Rule**:
If text content differs between breakpoints (different wording, truncation, or line breaks), **always use the Desktop version text**. Only the visual styling (font size, spacing, etc.) should be responsive, not the actual content.

```liquid
<!-- ✅ Correct: Same content, responsive styling -->
<h1 class="text-[3.4rem] lg:text-[6rem]">
  Come for the mission, Stay for the Team.
</h1>

<!-- ❌ Wrong: Different content per breakpoint -->
<h1 class="lg:hidden">Come for the mission</h1>
<h1 class="hidden lg:block">Come for the mission, Stay for the Team.</h1>
```

### 4. Section Structure

**Always use this structure**:

```liquid
<section id="section-name" class="bg-[color] text-[color]">
  <div class="flex flex-col gap-[value] px-[mobile] py-[value] md:px-[tablet] lg:px-[desktop]">
    <!-- Content here -->
  </div>
</section>
```

**Key points**:
- `<section>` has only `id` and color classes (from theme)
- Inner `<div>` contains padding, flex, gap, and max-width
- Padding values come from comparing Figma designs across breakpoints
- Check existing sections for padding patterns

### 5. Responsive Implementation Strategy

**Mobile-first approach** - base styles are mobile, add overrides for larger screens:

```liquid
<!-- Mobile → Tablet → Desktop progression -->
<div class="text-[2.8rem] md:text-[4rem] lg:text-[4.6rem]">
```

**Identify responsive patterns from Figma**:

1. **Layout changes**:
   ```liquid
   <!-- Figma shows: Mobile stacked, Desktop horizontal -->
   <div class="flex flex-col lg:flex-row">
   ```

2. **Spacing changes**:
   ```liquid
   <!-- Figma shows: Mobile 1.6rem gap, Tablet 2.4rem, Desktop 4rem -->
   <div class="gap-[1.6rem] md:gap-[2.4rem] lg:gap-4">
   ```

3. **Sizing changes**:
   ```liquid
   <!-- Figma shows: Mobile full width, Desktop fixed width -->
   <div class="w-full lg:w-[59.6rem]">
   ```

4. **Visibility changes**:
   ```liquid
   <!-- Element only in Desktop Figma -->
   <div class="hidden lg:block">
   
   <!-- Element only in Mobile/Tablet Figma -->
   <div class="lg:hidden">
   ```

5. **Typography changes**:
   ```liquid
   <!-- Figma shows different text sizes per breakpoint -->
   <p class="body-s md:body-m">
   <!-- OR if no utility matches -->
   <p class="text-[1.6rem] leading-[1.5] md:text-[1.8rem] md:leading-[2.4rem]">
   ```

### 6. Typography System

**Check `src/styles/tailwind.css` for available utilities**:

Look for `@utility` declarations like:
```css
@utility h1 { font-size: ...; }
@utility body-s { font-size: ...; }
```

**Usage priority**:

1. **First choice**: Use utility class if it matches Figma
   ```liquid
   <h1 class="h1">Heading</h1>
   <p class="body-s">Text</p>
   ```

2. **If no utility matches**: Create a new utility in `src/styles/tailwind.css`

   When Figma shows typography that doesn't match existing utilities, **create a new `@utility`** rather than using inline classes. This keeps templates clean and typography consistent.

   **Figma shows**: `Font: Futura PT Heavy, 34px, tracking -0.068px`
   
   **Step 1** - Add new utility to `src/styles/tailwind.css`:
   ```css
   @utility mobile-header {
     font-family: var(--font-primary);
     font-weight: 800;
     font-size: 3.4rem;
     line-height: normal;
     letter-spacing: -0.068rem;
   }
   ```

   **Step 2** - Use the utility class:
   ```liquid
   <h2 class="mobile-header">Shaping the future</h2>
   ```

   **Naming conventions** for new utilities:
   - Use descriptive names based on purpose: `card-title`, `section-label`, `stat-number`
   - For responsive variants: `h1-mobile`, `h2-tablet`, `caption-desktop`
   - Check Figma style names for hints (often shown in the output metadata)

   **❌ Avoid** long inline class lists:
   ```liquid
   <!-- Don't do this -->
   <h2 class="font-primary text-[3.4rem] font-extrabold leading-normal tracking-[-0.068rem]">
   ```

   **✅ Instead** create a utility and use it:
   ```liquid
   <!-- Do this -->
   <h2 class="mobile-header">
   ```

3. **Responsive typography** - Two approaches:

   **Option A**: Use separate utilities with breakpoints
   ```liquid
   <h2 class="h2-mobile md:h2">Section Title</h2>
   ```

   **Option B**: Create a responsive utility (if pattern repeats)
   ```css
   @utility h2-responsive {
     font-family: var(--font-secondary);
     font-weight: 700;
     font-size: 3.6rem;
     line-height: normal;
     
     @media (min-width: 768px) {
       font-family: var(--font-primary);
       font-weight: 500;
       font-size: 4.8rem;
     }
   }
   ```

### 7. Color System

**Check `src/styles/tailwind.css` for color tokens**:

Look for `@theme` section with `--color-*` variables:
```css
@theme {
  --color-red: #da291c;
  --color-gray: #383838;
  /* etc. */
}
```

**Usage**:
```liquid
<!-- Use token names from config -->
<div class="text-red bg-black">
<div class="text-light-gray bg-gray">

<!-- If Figma color doesn't match a token, use hex -->
<div class="text-[#da291c] bg-[#f5f5f5]">
```

**Priority**:
1. Use theme color if it matches Figma
2. Use hex value if no theme color matches
3. Never invent color names not in the theme

### 8. Images

**Download, convert to WebP, and save locally** - When fetching designs via the Figma MCP, the response includes image URLs as constants:
```javascript
const imgHeroBackground = "https://www.figma.com/api/mcp/asset/550e8400-e29b-41d4-a716-446655440000";
const imgTeamPhoto = "https://www.figma.com/api/mcp/asset/661f8511-f30c-52e5-b827-557766551111";
```

**ALWAYS use wizard-mcp to download and convert images to WebP**:

**CRITICAL**: Use the **absolute path** for `output_dir`. Relative paths don't work reliably with wizard-mcp. Determine the workspace root from your environment (e.g., from `user_info` or by running `pwd`).

For each image URL from Figma, call the `wizard-mcp` `convert_image` tool:
```
Tool: wizard-mcp → convert_image
Arguments:
  url: "https://www.figma.com/api/mcp/asset/550e8400-e29b-41d4-a716-446655440000"
  output_dir: "/absolute/path/to/workspace/src/assets"
  formats: ["webp"]
```

Example with real path:
```
Tool: wizard-mcp → convert_image
Arguments:
  url: "https://www.figma.com/api/mcp/asset/550e8400-e29b-41d4-a716-446655440000"
  output_dir: "/Users/username/Projects/my-project/src/assets"
  formats: ["webp"]
```

The tool downloads the image, converts it to WebP format, and saves it to `src/assets/`. It returns the filename (e.g., `550e8400-e29b-41d4-a716-446655440000.webp`).

**Naming convention**: Use descriptive names by renaming after download, or use the returned filename directly. The build process copies `src/assets/*` to the root of `dist/`.

**Use local WebP paths in templates**:
```liquid
<!-- ✅ Correct: Use local WebP file from assets -->
<img 
  src="/hero-background.webp"
  alt="WGU campus aerial view"
  class="size-full object-cover">

<!-- ❌ Wrong: Don't use temporary Figma URLs -->
<img 
  src="https://www.figma.com/api/mcp/asset/..."
  alt="...">
```

**Image processing workflow**:

1. **Get workspace absolute path** from your environment (check `user_info` for "Workspace Path" or run `pwd`)
2. **Collect all image URLs** from Figma design context
3. **Call wizard-mcp convert_image** for each URL with absolute `output_dir` path and `formats: ["webp"]`
4. **Rename files** to descriptive names (e.g., `hero-team-photo.webp`, `logo-aseptico.webp`)
5. **Use local paths** in Liquid templates (without `/assets/` prefix)

**Example workflow**:
```
Workspace: /Users/dev/Projects/career-site
Figma returns: const imgTeamPhoto = "https://www.figma.com/api/mcp/asset/661f8511..."

Step 1: Call wizard-mcp convert_image
  → url: "https://www.figma.com/api/mcp/asset/661f8511..."
  → output_dir: "/Users/dev/Projects/career-site/src/assets"
  → formats: ["webp"]
  
Step 2: Rename the output file
  → mv /Users/dev/Projects/career-site/src/assets/661f8511....webp /Users/dev/Projects/career-site/src/assets/team-photo.webp

Step 3: Use in template
  → <img src="/team-photo.webp" alt="Avalara team collaborating">
```

**Local asset paths** - Files in `src/assets/` are served from the root:
```liquid
<!-- ✅ Correct: Assets served from root -->
<img src="/hero-background.webp" alt="Hero background">

<!-- ❌ Wrong: Don't include /assets/ in path -->
<img src="/assets/hero-background.webp" alt="...">
```

**Standard pattern**:
```liquid
<div class="relative overflow-hidden rounded-[value]">
  <img 
    src="/section-image.webp"
    alt="Descriptive alt text"
    class="size-full object-cover">
</div>
```

**Responsive image sizing**:
```liquid
<!-- Compare Figma designs for size changes -->
<div class="h-[15rem] md:h-[32.2rem] lg:h-[48rem]">
  <img src="/hero-image.webp" alt="..." class="size-full object-cover">
</div>
```

**Check for shadow utilities** in `src/styles/tailwind.css`:
```liquid
<!-- If @utility shadow-image exists, use it -->
<div class="shadow-image">

<!-- Otherwise, implement shadow from Figma -->
<div class="shadow-[0.5rem_0.5rem_0_0_rgba(218,41,28,0.6)]">
```

### 7. Interactive Components

#### Carousel (Embla)

**IMPORTANT - Carousel Item Spacing**: For carousel items, use `mx` (margin left-right) on each item instead of `gap` on the container. This works better with Embla carousel's scroll behavior.

**Spacing conversion rule**: Divide the Figma gap value by 2 and apply as `mx` to each item:
- Figma gap `1rem` → use `mx-[0.5rem]` on each item
- Figma gap `2rem` → use `mx-[1rem]` on each item  
- Figma gap `2.4rem` → use `mx-[1.2rem]` on each item
- Figma gap `3rem` → use `mx-[1.5rem]` on each item
- Figma gap `5rem` → use `mx-[2.5rem]` on each item

```liquid
<section 
  data-embla-align="start"
  data-embla-slides-to-scroll="2"
  data-embla-md-slides-to-scroll="4"
  class="carousel bg-black">
  
  <div class="carousel--slide overflow-hidden">
    <!-- ✅ Use flex WITHOUT gap, apply mx to each item -->
    <div class="flex flex-row">
      {% for item in items %}
        <!-- mx-[1.2rem] = half of 2.4rem gap from Figma -->
        <div class="mx-[1.2rem] w-[16.4rem] shrink-0">
          <!-- Slide content -->
        </div>
      {% endfor %}
    </div>
  </div>
  
  <!-- Navigation -->
  <div class="flex items-center justify-center gap-3">
    <button class="carousel--prev">Prev</button>
    <div class="carousel--dots">
      <button class="current:bg-white"></button>
    </div>
    <button class="carousel--next">Next</button>
  </div>
</section>
```

**❌ Don't use gap on carousel item containers**:
```liquid
<!-- Wrong: gap doesn't work well with Embla -->
<div class="flex flex-row gap-[2.4rem]">
  {% for item in items %}
    <div class="w-[16.4rem] shrink-0">...</div>
  {% endfor %}
</div>
```

**✅ Use mx on each carousel item**:
```liquid
<!-- Correct: mx provides proper spacing for Embla -->
<div class="flex flex-row">
  {% for item in items %}
    <div class="mx-[1.2rem] w-[16.4rem] shrink-0">...</div>
  {% endfor %}
</div>
```

**Carousel attributes** (set on `<section>`):
- `data-embla-align`: `start`, `center`, `end`
- `data-embla-loop`: `true`, `false`
- `data-embla-slides-to-scroll`: number
- `data-embla-autoplay`: `true`, `false`
- `data-embla-autoplay-delay`: milliseconds
- Responsive: `data-embla-md-*`, `data-embla-lg-*`

#### Accordion

```liquid
<div class="accordion--group" id="faq-accordion">
  {% for item in items %}
    <div class="accordion--wrapper">
      <button class="accordion--trigger">
        {{ item.question }}
      </button>
      <div class="accordion--content">
        {{ item.answer }}
      </div>
    </div>
  {% endfor %}
</div>
```

#### Spoiler (Expandable Content)

```liquid
<div class="spoiler--wrapper">
  <h3>{{ title }}</h3>
  <div class="spoiler--content hidden">
    <p>{{ description }}</p>
  </div>
  <button class="spoiler--trigger current:rotate-180">
    <svg><use href="#i-chevron-down-bold"></use></svg>
  </button>
</div>
```

### 8. Accessibility (WCAG 2.1 AA)

**Required practices**:

1. **Semantic HTML**: Use proper elements (`<section>`, `<article>`, `<nav>`, `<button>`)

2. **Alt text**: Always provide descriptive alt text for images
   ```liquid
   <img src="..." alt="Team members collaborating at Victra office">
   ```

3. **Interactive elements**: Use `<button>` for actions, `<a>` for navigation
   ```liquid
   <button type="button" aria-label="Next slide">Next</button>
   <a href="/jobs" aria-label="View all job openings">Apply Now</a>
   ```

4. **Focus states**: Use `hocus:` variant for hover + focus
   ```liquid
   <button class="hocus:bg-red hocus:text-white">Click me</button>
   ```

5. **Color contrast**: Ensure text meets 4.5:1 ratio
   - White on black: ✅
   - Light gray (`#d9d9d9`) on black: ✅
   - Red (`#da291c`) on white: ✅

6. **Keyboard navigation**: All interactive elements must be keyboard accessible (handled by scripts)

7. **ARIA attributes**: Scripts automatically add `aria-expanded`, `aria-controls`, `aria-hidden`

### 9. Grid Layouts

**Use CSS Grid for complex layouts**:

```liquid
<!-- 2-column grid -->
<div class="grid grid-cols-2 gap-4">
  <div>Column 1</div>
  <div>Column 2</div>
</div>

<!-- Responsive grid -->
<div class="grid grid-cols-1 gap-4 md:grid-cols-2 lg:grid-cols-3">
  <!-- Items -->
</div>

<!-- Custom grid template -->
<div class="grid grid-cols-[3fr_2fr] gap-[1.86rem]">
  <div>Wide column</div>
  <div>Narrow column</div>
</div>
```

### 10. Custom Variants

**Check `src/styles/tailwind.css` for custom variants**:

Look for `@custom-variant` declarations:
```css
@custom-variant hocus {
  &:hover, &:focus { @slot; }
}
```

**Usage**:
```liquid
<!-- Use discovered variants -->
<button class="hocus:bg-red hocus:text-white">
<div class="current:bg-white">
```

### 11. Repeated Elements Pattern

**When Figma shows repeated identical elements** (cards, logos, testimonials, etc.):

1. **Identify repetition**: Look for multiple identical elements in Figma
2. **Create data structure**: Use paired `.liquid.json` file
3. **Implement loop**: Use `{% for item in items %}` pattern

**Example - Awards carousel with repeated logos**:

**Figma shows**: 14 identical award badge containers

**Implementation**:

`src/partials/index/awards.liquid`:
```liquid
<div class="carousel--slide overflow-hidden">
  <div class="flex flex-row">
    {% for award in awards %}
      <div class="mx-[1.2rem] h-[15rem] w-[16.4rem] shrink-0">
        <img src="{{ award.images.src }}" alt="{{ award.images.alt }}" class="size-full object-contain">
      </div>
    {% endfor %}
  </div>
</div>
```

`src/partials/index/awards.liquid.json`:
```json
{
  "awards": [
    {
      "images": {
        "src": "https://...",
        "alt": "Award description"
      }
    },
    {
      "images": {
        "src": "https://...",
        "alt": "Award description"
      }
    }
  ]
}
```

**Data context system**:
- Each `.liquid` file can have a paired `.liquid.json` file
- JSON data is automatically available in the template
- Use dot notation to access nested data: `{{ award.images.src }}`
- Arrays can be looped with `{% for item in items %}`

### 12. Reusable Components

**Check `src/components/` for reusable components**:

Components are template fragments that accept parameters via `{% render %}`.

**Using a component**:

```liquid
{% render 'button', text: 'Apply Now', link: '/jobs', type: "secondary" %}
```

**Component structure** (`src/components/button.liquid`):
```liquid
{% assign type = type | default: "primary" %}
{% assign text = text | default: "Default Text" %}
{% assign link = link | default: "#" %}

<a href="{{ link }}" class="button button-{{ type }}">
  {{ text }}
</a>
```

**When to create a component**:
- Element appears in multiple sections/pages
- Element has configurable variations (types, sizes, colors)
- Element has complex logic that should be centralized

**When to use inline markup**:
- Element is unique to one section
- Element is simple with no variations
- Element is part of a loop (use data instead)

## Common Patterns

### Hero Section

```liquid
<section class="relative z-1 overflow-hidden bg-black text-white">
  <!-- Background decoration -->
  <div class="absolute -top-[8.9rem] -left-[12.6rem] -z-1 h-[40.8rem] w-[40.5rem] rounded-[75rem]" 
       style="background: radial-gradient(...)"></div>
  
  <div class="flex flex-col gap-[2.4rem] px-[1.6rem] pt-[4.8rem] pb-4">
    <h1 class="h1">Hero Title</h1>
    <p class="body-m">Hero description</p>
  </div>
</section>
```

### Content Section with Image

```liquid
<section class="bg-black text-white">
  <div class="flex flex-col gap-4 px-[1.6rem] py-[6rem] lg:px-8">
    <div class="flex flex-col gap-[2.4rem] lg:flex-row lg:items-center lg:gap-[4rem]">
      <!-- Text -->
      <div class="flex flex-col gap-[1.6rem] lg:w-[59.6rem]">
        <h2 class="h2">Section Title</h2>
        <p class="body-s text-light-gray">Description</p>
      </div>
      
      <!-- Image -->
      <div class="w-full lg:w-[37.3rem]">
        <img src="..." alt="..." class="size-full object-cover">
      </div>
    </div>
  </div>
</section>
```

### Card Grid with Data Loop

**Figma shows**: 6 identical benefit cards

**Implementation**:

`src/partials/index/benefits.liquid`:
```liquid
<div class="grid grid-cols-1 gap-4 md:grid-cols-3">
  {% for benefit in benefits %}
    <article class="flex flex-col gap-[2.4rem]">
      <div class="relative size-7">
        <img src="{{ benefit.image.src }}" alt="{{ benefit.image.alt }}" class="size-full object-contain">
      </div>
      <h3 class="h4 text-red">{{ benefit.title }}</h3>
      <p class="body-m">{{ benefit.description }}</p>
    </article>
  {% endfor %}
</div>
```

`src/partials/index/benefits.liquid.json`:
```json
{
  "benefits": [
    {
      "image": { "src": "...", "alt": "..." },
      "title": "Health Insurance",
      "description": "Comprehensive coverage..."
    },
    {
      "image": { "src": "...", "alt": "..." },
      "title": "401(k) Matching",
      "description": "Generous retirement..."
    }
  ]
}
```

### Carousel with Repeated Items

**Figma shows**: Horizontal scrolling logos/images with 2.4rem gap between items

`src/partials/index/awards.liquid`:
```liquid
<section 
  data-embla-align="start"
  data-embla-slides-to-scroll="2"
  class="carousel bg-black">
  
  <div class="carousel--slide overflow-hidden">
    <!-- No gap on flex container - use mx on items instead -->
    <div class="flex flex-row">
      {% for award in awards %}
        <!-- mx-[1.2rem] = half of 2.4rem gap from Figma design -->
        <div class="mx-[1.2rem] h-[15rem] w-[16.4rem] shrink-0">
          <img src="{{ award.images.src }}" alt="{{ award.images.alt }}" class="size-full object-contain">
        </div>
      {% endfor %}
    </div>
  </div>
  
  <!-- Navigation buttons -->
</section>
```

**Responsive carousel gaps**: If Figma shows different gaps per breakpoint, use responsive mx:
```liquid
<!-- Mobile: 3rem gap, Desktop: 5rem gap -->
<div class="mx-[1.5rem] lg:mx-[2.5rem] shrink-0">
```

### Using Components

```liquid
<!-- Button component -->
{% render 'button', text: 'Apply Now', link: '/jobs', type: "secondary" %}

<!-- Custom component with multiple params -->
{% render 'card', 
  title: "Feature Title",
  description: "Feature description",
  icon: "star",
  link: "/learn-more" %}
```

## Conversion Checklist

When implementing a Figma design:

- [ ] **Read `src/styles/tailwind.css`** for theme config
- [ ] Fetch all breakpoint designs (mobile, tablet, desktop)
- [ ] Compare designs to identify responsive changes
- [ ] **Check text content** - if different across breakpoints, use Desktop version
- [ ] **Identify repeated elements** in Figma
- [ ] **Create data structure** if elements repeat (`.liquid.json` file)
- [ ] **Check `src/components/`** for reusable components
- [ ] **Process images**: Use wizard-mcp to download, convert to WebP, save to `src/assets/`
- [ ] Use proper section structure (`<section>` + inner `<div>`)
- [ ] Apply mobile-first responsive classes
- [ ] Use typography utilities from config (or custom if none match)
- [ ] Use theme colors from config (or hex if none match)
- [ ] Add descriptive alt text to all images
- [ ] Use semantic HTML elements
- [ ] Add focus states with available variants
- [ ] Test keyboard navigation
- [ ] Verify color contrast ratios
- [ ] Use appropriate interactive component (carousel, accordion, spoiler)
- [ ] Match spacing scale from config

## Anti-Patterns

**Avoid**:

```liquid
<!-- ❌ Don't skip reading the config -->
<!-- Always read src/styles/tailwind.css first -->

<!-- ❌ Don't use different text content per breakpoint -->
<p class="lg:hidden">Short text for mobile</p>
<p class="hidden lg:block">Full detailed text for desktop</p>
<!-- Use Desktop text with responsive styling instead -->

<!-- ❌ Don't hardcode repeated elements -->
<div>Item 1</div>
<div>Item 2</div>
<div>Item 3</div>
<!-- Use for-loop with data instead -->

<!-- ❌ Don't duplicate component logic -->
<!-- Check src/components/ for existing components -->

<!-- ❌ Don't add padding/flex to <section> -->
<section class="px-4 flex flex-col">

<!-- ❌ Don't use inline typography classes -->
<h2 class="font-primary text-[3.4rem] font-extrabold leading-normal tracking-[-0.068rem]">
<!-- Create a @utility in tailwind.css instead and use: -->
<h2 class="mobile-header">

<!-- ❌ Don't use hex when theme color exists -->
<div class="text-[#da291c]">  <!-- Check config for color tokens -->

<!-- ❌ Don't implement only one breakpoint -->
<!-- Fetch and compare all three Figma designs -->

<!-- ❌ Don't forget responsive breakpoints -->
<div class="w-[59.6rem]">  <!-- Add mobile/tablet sizes -->

<!-- ❌ Don't use divs for buttons -->
<div onclick="...">Click</div>  <!-- Use <button> -->

<!-- ❌ Don't omit alt text -->
<img src="...">  <!-- Always add alt="" or alt="description" -->

<!-- ❌ Don't use Figma asset URLs directly -->
<img src="https://www.figma.com/api/mcp/asset/...">
<!-- Always download, convert to WebP with wizard-mcp, and use local paths -->

<!-- ❌ Don't use gap on carousel item containers -->
<div class="flex flex-row gap-[3rem]">
  {% for item in items %}<div class="shrink-0">...</div>{% endfor %}
</div>
<!-- Use mx on each item instead: mx-[1.5rem] for 3rem gap -->
```

## Testing

Before considering implementation complete:

1. **Visual**: Compare against Figma at all breakpoints
2. **Responsive**: Test at 375px, 768px, 1024px, 1440px
3. **Accessibility**: Tab through all interactive elements
4. **Screen reader**: Test with VoiceOver/NVDA if available
5. **Color contrast**: Verify with browser DevTools

## Workflow Summary

1. **Read config**: `src/styles/tailwind.css` (spacing, typography, colors, variants)
2. **Check components**: `src/components/` for reusable elements
3. **Fetch designs**: All three breakpoints (mobile, tablet, desktop)
4. **Identify patterns**: Repeated elements, data structures needed
5. **Compare**: What changes between breakpoints (layout, spacing, typography, visibility)
6. **Text content check**: If text differs, use Desktop version
7. **Process images**: Use wizard-mcp `convert_image` with **absolute path** to download all Figma images, convert to WebP, save to `src/assets/`
8. **Create data**: `.liquid.json` file if elements repeat
9. **Implement**: Mobile-first with responsive overrides, use loops for repeated items, use local WebP image paths
10. **Use utilities**: From config when they match Figma
11. **Custom values**: When no utility matches
12. **Test**: All breakpoints and accessibility

## Additional Resources

- **Theme config**: `src/styles/tailwind.css` (read first!)
- **Image processing**: `wizard-mcp` → `convert_image` tool for WebP conversion
- **Assets folder**: `src/assets/` for all images (served from root in build)
- **Carousel script**: `src/scripts/carousel.js`
- **Accordion script**: `src/scripts/accordion.js`
- **Spoiler script**: `src/scripts/spoiler.js`
- **Example sections**: `src/partials/index/`
- **Examples file**: See `EXAMPLES.md` in this skill directory