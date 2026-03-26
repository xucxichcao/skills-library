# Implementation Examples

Real examples from the Victra career site project. Reference these when implementing Figma designs.

**Quick Reference**:
- Layout patterns: Examples 1, 2, 5
- Data loops: Examples 3, 8, 10
- Carousels: Examples 4, 8, 12
- Components: Example 9
- Typography: Example 6
- Accessibility: Example 7
- Interactive dialogs: Example 11
- Accordion: Example 13
- Spoiler: Example 14

---

## Example 1: Core Values Section

**Figma Design**: Text content on left, image on right, responsive layout

**Implementation**:

```liquid
<section class="bg-black text-white">
  <div class="flex flex-col gap-4 px-[1.6rem] py-[6rem] md:px-[4.8rem] lg:px-[8rem]">
    <div class="flex flex-col gap-[2.4rem] lg:flex-row lg:items-center lg:justify-between lg:gap-[4rem]">
      <!-- Text Content -->
      <div class="flex flex-col gap-[1.6rem] lg:w-[59.6rem]">
        <h2 class="h2">Our Core Values</h2>
        <p class="body-s text-[#d9d9d9] md:body-m">
          Guided by our Core Values: Performance, Collaboration, Integrity...
        </p>
      </div>

      <!-- Image -->
      <div class="w-full md:w-[30.5rem] md:h-[39.2rem] lg:w-[37.3rem] lg:h-[48rem]">
        <img src="..." alt="Core Values" class="size-full object-cover">
      </div>
    </div>
  </div>
</section>
```

**Key points**:
- Mobile: Stacked vertically, full-width image
- Tablet: Fixed image dimensions `30.5rem × 39.2rem`
- Desktop: Horizontal layout, larger image `37.3rem × 48rem`
- Text switches from `body-s` to `body-m` at tablet breakpoint

## Example 2: Image Grid

**Figma Design**: 2-column grid with red shadow offset

**Implementation**:

```liquid
<div class="grid grid-cols-2 gap-[1.9rem] md:gap-4">
  <!-- Left Column -->
  <div class="flex flex-col gap-[1.9rem] md:gap-4">
    <div class="h-[15rem] md:h-[32.2rem] overflow-hidden rounded-[1.9rem] md:rounded-[4rem] shadow-[0.23rem_0.23rem_0_0_rgba(218,41,28,0.6)] md:shadow-[0.5rem_0.5rem_0_0_rgba(218,41,28,0.6)]">
      <img src="..." alt="..." class="size-full object-cover">
    </div>
    <div class="h-[15rem] md:h-[32.2rem] overflow-hidden rounded-[1.9rem] md:rounded-[4rem] shadow-[0.23rem_0.23rem_0_0_rgba(218,41,28,0.6)] md:shadow-[0.5rem_0.5rem_0_0_rgba(218,41,28,0.6)]">
      <img src="..." alt="..." class="size-full object-cover">
    </div>
  </div>

  <!-- Right Column -->
  <div class="flex flex-col gap-[1.9rem] md:gap-4">
    <div class="h-[15rem] md:h-[32.2rem] overflow-hidden rounded-[1.9rem] md:rounded-[4rem] shadow-[0.23rem_0.23rem_0_0_rgba(218,41,28,0.6)] md:shadow-[0.5rem_0.5rem_0_0_rgba(218,41,28,0.6)]">
      <img src="..." alt="..." class="size-full object-cover">
    </div>
    <div class="h-[15rem] md:h-[32.2rem] w-[66%] overflow-hidden rounded-[1.9rem] md:rounded-[4rem] shadow-[0.23rem_0.23rem_0_0_rgba(218,41,28,0.6)] md:shadow-[0.5rem_0.5rem_0_0_rgba(218,41,28,0.6)]">
      <img src="..." alt="..." class="size-full object-cover">
    </div>
  </div>
</div>
```

**Or using the utility class**:

```liquid
<div class="shadow-image h-[15rem] md:h-[32.2rem] overflow-hidden rounded-[1.9rem] md:rounded-[4rem]">
  <img src="..." alt="..." class="size-full object-cover">
</div>
```

## Example 3: Benefits Grid with Spoilers

**Figma Design**: 3-column grid with expandable descriptions

**Implementation**:

```liquid
<div class="grid grid-cols-1 gap-4 md:grid-cols-3">
  {% for benefit in benefits %}
    <article class="flex flex-col items-center gap-[2.4rem]">
      <div class="relative size-7 overflow-hidden">
        <img src="{{ benefit.image.src }}" alt="{{ benefit.image.alt }}" class="size-full object-contain">
      </div>
      
      <div class="spoiler--wrapper flex flex-col items-center text-center">
        <h3 class="h4 text-red">{{ benefit.title }}</h3>
        
        <div class="spoiler--content hidden">
          <p class="body-m mt-[0.8rem]">{{ benefit.description }}</p>
        </div>
        
        <button class="spoiler--trigger current:rotate-180 mt-[0.8rem] flex size-3 items-center justify-center transition-transform duration-300">
          <svg class="h-[1.2rem] w-[2.1rem]">
            <use href="#i-chevron-down-bold"></use>
          </svg>
        </button>
      </div>
    </article>
  {% endfor %}
</div>
```

**Key points**:
- Mobile: Single column
- Tablet+: 3 columns
- Spoiler script handles expand/collapse
- `current:rotate-180` rotates chevron when expanded

## Example 4: Awards Carousel

**Figma Design**: Horizontal scrolling logos with navigation

**Implementation**:

```liquid
<section 
  data-embla-align="start"
  data-embla-slides-to-scroll="2"
  data-embla-md-slides-to-scroll="4"
  class="carousel bg-black text-white">
  
  <div class="flex flex-col gap-4 px-[1.6rem] py-5">
    <h2 class="h2 text-center">Recognition That Matters</h2>
    
    <div class="carousel--slide overflow-hidden">
      <div class="flex flex-row">
        {% for award in awards %}
          <div class="mx-[1.2rem] h-[15rem] w-[16.4rem] shrink-0 md:w-[calc((100%-9rem)/4)]">
            <img src="{{ award.images.src }}" alt="{{ award.images.alt }}" class="size-full object-contain">
          </div>
        {% endfor %}
      </div>
    </div>
    
    <div class="flex items-center justify-center gap-3">
      <button class="carousel--prev text-dark-gray hocus:text-white flex size-[3.2rem] items-center justify-center rounded-full border-[0.2rem] transition-colors">
        <svg class="h-[1.4rem] w-[0.7rem]">
          <use href="#i-angle-left"></use>
        </svg>
      </button>
      
      <div class="carousel--dots flex items-center gap-1.5">
        <button class="bg-dark-gray current:bg-white hocus:bg-white size-1 rounded-full transition-colors"></button>
      </div>
      
      <button class="carousel--next text-dark-gray hocus:text-white flex size-[3.2rem] items-center justify-center rounded-full border-[0.2rem] transition-colors">
        <svg class="h-[1.4rem] w-[0.7rem]">
          <use href="#i-angle-right"></use>
        </svg>
      </button>
    </div>
  </div>
</section>
```

**Key points**:
- Carousel attributes on `<section>`
- Mobile: Scroll 2 slides at a time
- Tablet: Scroll 4 slides at a time
- Navigation buttons with proper focus states
- Dots automatically generated by carousel script

## Example 5: Hero with Background Decoration

**Figma Design**: Large heading with decorative background blur

**Implementation**:

```liquid
<section class="relative z-1 overflow-hidden bg-black text-white">
  <!-- Background decoration -->
  <div 
    class="absolute -top-[8.9rem] -left-[12.6rem] -z-1 h-[40.8rem] w-[40.5rem] rounded-[75rem] md:-top-[4.2rem] md:-left-[4.3rem] md:h-[39rem] md:w-[38.9rem]"
    style="background: radial-gradient(70.71% 70.71% at 50% 50%, rgba(146, 146, 146, 0.51) 0%, rgba(146, 146, 146, 0.00) 70%, rgba(146, 146, 146, 0.00) 100%)">
  </div>
  
  <div class="flex flex-col gap-[2.4rem] px-[1.6rem] pt-[4.8rem] pb-4">
    <div class="relative">
      <img 
        src="https://d25zu39ynyitwy.cloudfront.net/.../star-decorative.svg"
        alt=""
        class="absolute bottom-1.5 left-[28.4rem]">
      
      <h1 class="h1">
        Come for the mission, <br>
        <span class="text-red">Stay for the Team.</span>
      </h1>
    </div>
    
    <p class="body-m">Description text...</p>
  </div>
</section>
```

**Key points**:
- `relative` on section, `absolute` on decoration
- `-z-1` pushes decoration behind content
- `overflow-hidden` clips decoration outside bounds
- Decorative images use empty `alt=""`
- Responsive positioning for decoration

## Example 6: Responsive Typography

**Figma Design**: Different font sizes per breakpoint

**Implementation**:

```liquid
<!-- Using utility class (recommended) -->
<h2 class="h2">Heading</h2>
<!-- Renders as 2.8rem on mobile, 4.6rem on desktop -->

<!-- Custom sizes when needed -->
<h2 class="text-[2.8rem] md:text-[3.6rem] lg:text-[4.6rem]">Heading</h2>

<!-- Body text -->
<p class="body-s md:body-m">
  Text that's 1.6rem on mobile, 1.8rem on tablet+
</p>
```

## Example 7: Accessible Button with Icon

**Figma Design**: Button with chevron icon

**Implementation**:

```liquid
<a 
  href="/jobs"
  class="hocus:text-red inline-flex items-center gap-[1.6rem] text-[1.6rem] font-medium underline transition-colors">
  View Our Benefits Guide
  <svg class="h-[1.4rem] w-[0.8rem]" aria-hidden="true">
    <use href="#i-chevron-right"></use>
  </svg>
</a>
```

**Key points**:
- Use `<a>` for navigation, `<button>` for actions
- `hocus:` applies styles on hover and focus
- `aria-hidden="true"` on decorative icons
- `inline-flex` with `gap` for icon spacing
- `transition-colors` for smooth hover effect

## Example 8: Repeated Elements with Data Loop

**Figma Design**: Awards carousel with 14 identical badge containers

**Analysis**:
- Each badge has same structure (container + image)
- Only difference is the image source
- Perfect candidate for data-driven loop

**Implementation**:

`src/partials/index/awards.liquid`:
```liquid
<section 
  data-embla-align="start"
  data-embla-slides-to-scroll="2"
  data-embla-md-slides-to-scroll="4"
  class="carousel bg-black text-white">
  
  <div class="flex flex-col gap-4 px-[1.6rem] py-5">
    <div class="flex flex-col gap-[2.2rem] text-center">
      <h2 class="h2">Recognition That Matters</h2>
      <p class="body-s">Recognized by employees and industry leaders alike.</p>
    </div>
    
    <div class="flex flex-col gap-4">
      <div class="carousel--slide overflow-hidden">
        <div class="flex flex-row">
          {% for award in awards %}
            <div class="relative mx-[1.2rem] h-[15rem] w-[16.4rem] shrink-0 overflow-hidden md:mx-[1.5rem] md:w-[calc((100%-9rem)/4)] lg:h-20 lg:w-[21.9rem]">
              <img src="{{ award.images.src }}" alt="{{ award.images.alt }}" class="size-full object-contain">
            </div>
          {% endfor %}
        </div>
      </div>
      
      <div class="flex flex-row items-center justify-center gap-3">
        <button class="carousel--prev text-dark-gray hocus:text-white flex size-[3.2rem] items-center justify-center rounded-full border-[0.2rem] transition-colors">
          <svg class="h-[1.4rem] w-[0.7rem]">
            <use href="#i-angle-left"></use>
          </svg>
        </button>
        
        <div class="carousel--dots flex items-center gap-1.5">
          <button class="bg-dark-gray current:bg-white hocus:bg-white size-1 rounded-full transition-colors"></button>
        </div>
        
        <button class="carousel--next text-dark-gray hocus:text-white flex size-[3.2rem] items-center justify-center rounded-full border-[0.2rem] transition-colors">
          <svg class="h-[1.4rem] w-[0.7rem]">
            <use href="#i-angle-right"></use>
          </svg>
        </button>
      </div>
    </div>
  </div>
</section>
```

`src/partials/index/awards.liquid.json`:
```json
{
  "awards": [
    {
      "images": {
        "src": "https://d25zu39ynyitwy.cloudfront.net/.../best-ceos-for-women.webp",
        "alt": "Best CEOs for Women Award"
      }
    },
    {
      "images": {
        "src": "https://d25zu39ynyitwy.cloudfront.net/.../great-place-to-work-2025.webp",
        "alt": "Great Place to Work 2025"
      }
    },
    {
      "images": {
        "src": "https://d25zu39ynyitwy.cloudfront.net/.../best-company-compensation.webp",
        "alt": "Best Company for Compensation"
      }
    }
  ]
}
```

**Key points**:
- Identified 14 repeated elements in Figma
- Created data structure with array of objects
- Used `{% for award in awards %}` to loop
- Each item accessed via dot notation: `{{ award.images.src }}`
- Carousel script automatically handles duplicated items
- Easy to add/remove awards by editing JSON only

## Example 9: Using Reusable Components

**Figma Design**: Call-to-action button appears in multiple sections

**Instead of duplicating**:
```liquid
<!-- ❌ Don't do this -->
<a href="/jobs" class="button button-font button-secondary">Apply Now</a>
<a href="/jobs" class="button button-font button-secondary">Apply Now</a>
```

**Use the component**:
```liquid
<!-- ✅ Do this -->
{% render 'button', text: 'Apply Now', link: '/jobs', type: "secondary" %}
{% render 'button', text: 'Apply Now', link: '/jobs', type: "secondary" %}
```

**Component definition** (`src/components/button.liquid`):
```liquid
{% assign type = type | default: "primary" %}
{% assign text = text | default: "Default Text" %}
{% assign link = link | default: "#" %}

{% case type %}
  {% when "primary" %}
    {% assign buttonClass = "button button-font button-primary" %}
  {% when "secondary" %}
    {% assign buttonClass = "button button-font button-secondary" %}
{% endcase %}

<a href="{{ link }}" class="{{ buttonClass }}">
  {{ text }}
</a>
```

**Benefits**:
- Single source of truth for button styles
- Easy to update all buttons at once
- Consistent behavior across the site
- Reduces code duplication

## Example 10: Data Context for Complex Structures

**Figma Design**: Benefits section with 6 cards, each with icon, title, description, and expandable content

`src/partials/index/benefits.liquid`:
```liquid
<div class="grid grid-cols-1 gap-4 md:grid-cols-3">
  {% for benefit in benefits %}
    <article class="flex flex-col items-center gap-[2.4rem]">
      <div class="relative size-7 overflow-hidden">
        <img src="{{ benefit.image.src }}" alt="{{ benefit.image.alt }}" class="size-full object-contain">
      </div>
      
      <div class="spoiler--wrapper flex flex-col items-center text-center">
        <h3 class="h4 text-red">{{ benefit.title }}</h3>
        
        <div class="spoiler--content hidden">
          <p class="body-m mt-[0.8rem]">{{ benefit.description }}</p>
        </div>
        
        <button class="spoiler--trigger current:rotate-180 mt-[0.8rem] flex size-3 items-center justify-center transition-transform duration-300">
          <svg class="h-[1.2rem] w-[2.1rem]">
            <use href="#i-chevron-down-bold"></use>
          </svg>
        </button>
      </div>
    </article>
  {% endfor %}
</div>
```

`src/partials/index/benefits.liquid.json`:
```json
{
  "benefits": [
    {
      "image": {
        "src": "https://d25zu39ynyitwy.cloudfront.net/.../health-icon.svg",
        "alt": "Health Insurance Icon"
      },
      "title": "Health & Wellness",
      "description": "Comprehensive medical, dental, and vision coverage for you and your family."
    },
    {
      "image": {
        "src": "https://d25zu39ynyitwy.cloudfront.net/.../retirement-icon.svg",
        "alt": "Retirement Icon"
      },
      "title": "Retirement Planning",
      "description": "401(k) with company match to help you build your financial future."
    }
  ]
}
```

**Key points**:
- Nested data structure: `benefit.image.src`
- Multiple properties per item
- Spoiler script handles expand/collapse automatically
- Clean separation of data and presentation

## Example 11: Share Dialog with Focus-trap

**Figma Design**: Share button that opens a dropdown with social links

**Use case**: Job details pages, any share functionality

**Implementation**:

`src/partials/jobs/share-dialog.liquid`:
```liquid
<div class="relative" data-share-wrapper>
  <button
    type="button"
    data-share-trigger
    aria-haspopup="dialog"
    aria-expanded="false"
    class="button button-text button--secondary flex w-full items-center justify-center gap-[0.8rem]">
    Share
    <svg class="size-[1.25rem]" aria-hidden="true">
      <use href="#i-share"></use>
    </svg>
  </button>

  <!-- Share Dialog (focus-trap) -->
  <div
    data-share-dialog
    role="dialog"
    aria-label="Share this job"
    aria-modal="true"
    class="shadow-resting absolute top-full right-0 left-0 z-10 hidden border border-[#ddd] bg-white">
    <div class="flex flex-col">
      <a
        href="https://www.facebook.com/sharer/sharer.php?u={{ current_url | default: '' | url_encode }}"
        target="_blank"
        rel="noopener noreferrer"
        class="button-text text-darkest-grey hocus:bg-primary-orange hocus:text-white px-[1.6rem] py-[1.6rem] transition-colors duration-300">
        FACEBOOK
      </a>
      <a
        href="https://twitter.com/intent/tweet?url={{ current_url | default: '' | url_encode }}"
        target="_blank"
        rel="noopener noreferrer"
        class="button-text text-darkest-grey hocus:bg-primary-orange hocus:text-white px-[1.6rem] py-[1.6rem] transition-colors duration-300">
        X (TWITTER)
      </a>
      <a
        href="https://www.linkedin.com/sharing/share-offsite?url={{ current_url | default: '' | url_encode }}"
        target="_blank"
        rel="noopener noreferrer"
        class="button-text text-darkest-grey hocus:bg-primary-orange hocus:text-white px-[1.6rem] py-[1.6rem] transition-colors duration-300">
        LINKEDIN
      </a>
      <a
        href="mailto:?subject={{ job_title | default: 'Job Opportunity' | url_encode }}&body=Check out this job opportunity: {{ current_url | default: '' | url_encode }}"
        class="button-text text-darkest-grey hocus:bg-primary-orange hocus:text-white px-[1.6rem] py-[1.6rem] transition-colors duration-300">
        EMAIL
      </a>
    </div>
  </div>
</div>
```

**Required JavaScript** (`src/scripts/share-dialog.js`):
```javascript
(function () {
  const wrappers = document.querySelectorAll('[data-share-wrapper]');

  wrappers.forEach(function (wrapper) {
    const trigger = wrapper.querySelector('[data-share-trigger]');
    const dialog = wrapper.querySelector('[data-share-dialog]');

    if (!trigger || !dialog) return;

    let isOpen = false;

    function getFocusableElements() {
      return dialog.querySelectorAll('a, button, [tabindex]:not([tabindex="-1"])');
    }

    function openDialog() {
      isOpen = true;
      dialog.classList.remove('hidden');
      trigger.setAttribute('aria-expanded', 'true');
      const focusable = getFocusableElements();
      if (focusable.length > 0) focusable[0].focus();
    }

    function closeDialog() {
      isOpen = false;
      dialog.classList.add('hidden');
      trigger.setAttribute('aria-expanded', 'false');
      trigger.focus();
    }

    trigger.addEventListener('click', function (e) {
      e.stopPropagation();
      if (isOpen) {
        closeDialog();
      } else {
        openDialog();
      }
    });

    document.addEventListener('click', function (e) {
      if (isOpen && !dialog.contains(e.target) && e.target !== trigger) {
        closeDialog();
      }
    });

    wrapper.addEventListener('keydown', function (e) {
      if (!isOpen) return;

      if (e.key === 'Escape') {
        e.preventDefault();
        closeDialog();
        return;
      }

      if (e.key === 'Tab') {
        const focusable = getFocusableElements();
        const first = focusable[0];
        const last = focusable[focusable.length - 1];

        if (e.shiftKey) {
          if (document.activeElement === first) {
            e.preventDefault();
            last?.focus();
          }
        } else {
          if (document.activeElement === last) {
            e.preventDefault();
            first?.focus();
          }
        }
      }
    });
  });
})();
```

**Key points**:
- Data attributes: `data-share-wrapper`, `data-share-trigger`, `data-share-dialog`
- Focus moves to first link on open
- Tab cycles within dialog (focus-trap)
- Escape closes and returns focus to trigger
- Click outside closes dialog
- `aria-expanded` updates automatically

## Example 12: Carousel Item Spacing

**Figma Design**: Horizontal scroll with 2.4rem gap between items

**❌ Wrong - using gap**:
```liquid
<div class="flex flex-row gap-[2.4rem]">
  {% for item in items %}
    <div class="w-[16.4rem] shrink-0">...</div>
  {% endfor %}
</div>
```

**✅ Correct - using mx (half of gap)**:
```liquid
<div class="flex flex-row">
  {% for item in items %}
    <div class="mx-[1.2rem] w-[16.4rem] shrink-0">...</div>
  {% endfor %}
</div>
```

**Responsive gaps**:
```liquid
<!-- Mobile: 3rem gap → mx-[1.5rem], Desktop: 5rem gap → mx-[2.5rem] -->
<div class="mx-[1.5rem] lg:mx-[2.5rem] shrink-0">
```

## Example 13: Accordion

**Figma Design**: FAQ section with expandable items

```liquid
<div class="accordion--group" id="faq-accordion">
  {% for item in faqs %}
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

## Example 14: Spoiler (Expandable Content)

**Figma Design**: Content with show/hide toggle

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

**Key points**:
- `spoiler--content hidden` starts collapsed
- `current:rotate-180` rotates chevron when expanded
- Script handles toggle automatically
