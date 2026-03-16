# Career Sites Renderer - Components Reference

## Architecture Overview

### Rendering Pipeline

1. **Server** renders the page using a template engine (`Rengine`). Templates contain `{{component:guid}}` placeholders.
2. Each placeholder is replaced with a `<div>` wrapper that includes:
   - `data-react-component="<name>"` — identifies the component type
   - `data-guid="<guid>"` — unique instance ID
   - `data-variant="<variant>"` — variant name (used for CSS scoping)
   - `data-react-prop-<key>="<value>"` — one attribute per component prop
3. The component is **server-side rendered** (SSR) inside the wrapper using `renderToString()` with a Redux store.
4. Redux preload state is injected as `window.__PRELOAD_STATE__` via a `<script>` tag.
5. On the **client**, individual loader scripts query for `[data-react-component="<name>"]` elements, extract props from `data-react-prop-*` attributes, and call `hydrateRoot()`.

### Prop Passing Convention

Props are passed as HTML data attributes on the wrapper `<div>`:

```html
<div data-react-component="jobs-list-only"
     data-guid="abc-123"
     data-variant="jobs-list-only"
     data-react-prop-classname="my-class"
     data-react-prop-show_brand="true"
     data-react-prop-apply_button_text="Apply Now">
  <!-- SSR content -->
</div>
```

- String values are passed directly.
- Booleans are passed as `"true"` / `"false"` strings (parsed on extraction).
- Objects/arrays are JSON-stringified with quotes escaped as `&quot;`.

### Component-to-SSR Mapping

| `data-react-component` | SSR Placeholder | React Component |
|---|---|---|
| `search-box` | `SSR_SearchBox` | `JobsSearch` |
| `jobs-list-only` | `SSR_JobsListOnly` | `JobsListOnly` |
| `jobs-list-header` | `SSR_JobsListHeader` | `JobsListHeader` |
| `jobs-filter` | `SSR_JobsFilter` | `JobsFilter` |
| `jobs-pagination` | `SSR_JobsPagination` | `JobsPagination` |
| `jobs-sort-by` | `SSR_JobsSortBy` | `JobsSortBy` |
| `jobs-radius` | `SSR_JobsRadius` | `JobsRadius` |
| `jobs-current-searches` | `SSR_JobsCurrentSearches` | `JobsCurrentSearches` |
| `jobs-current-location` | `SSR_JobsCurrentLocation` | `JobsCurrentLocation` |
| `jobs-commute-time-modal` | `SSR_JobsCommuteTimeModal` | `JobsCommuteTimeModal` |
| `similar-jobs` | `SSR_SimilarJobs` | `SimilarJobs` |

### CSS Scoping

Styles are scoped using `[data-react-component="<name>"]` attribute selectors in SCSS files. All SCSS is bundled through `src/client/scss/main.scss`.

---

## Components

---

### JobsSearch

**`data-react-component="search-box"`**

The main search form with keyword input, location input, and search button.

#### Props

| Prop | Type | Default | Description |
|---|---|---|---|
| `classname` | `string` | — | Custom CSS class on the wrapper |
| `horizontal` | `boolean` | `false` | If `true`, adds `{classname}__horizontal` class for horizontal layout |
| `key_search_label` | `string` | — | Label text above the keyword input |
| `key_search_placeholder` | `string` | `"Keyword"` | Placeholder for keyword input |
| `search_button_label` | `string` | `"Search"` | Text on the search button |
| `location_search_label` | `string` | — | Label text above the location input |
| `location_search_placeholder` | `string` | `"Location (enter state, city, or zip)"` | Placeholder for location input |
| `locate_me` | `boolean` | `false` | Show a "locate me" geolocation button |
| `ask_for_location_permission` | `boolean` | `false` | Show a permission dialog before using geolocation |
| `location_permission_modal_header` | `string` | — | Title of the location permission modal |
| `location_permission_modal_text` | `string` | — | Body text of the location permission modal |
| `disable_location_type_ahead_location_address` | `boolean` | `false` | Disable address suggestions in location typeahead |
| `disable_location_type_ahead_google_search` | `boolean` | `true` | Disable Google Maps location suggestions |
| `location_type_ahead_source` | `string[]` | — | Sources for location autocomplete: `"job_locations"`, `"google_search"` |
| `enable_hint_text` | `boolean` | `false` | Show hint text below inputs |
| `enable_auto_complete_job_titles` | `boolean` | `true` | Enable keyword autocomplete from job titles |
| `enable_commute_time` | `boolean` | `false` | Show "Commute Time" button next to location input |
| `advanced_commute_time_settings` | `object` | — | Text overrides for the commute time modal (see JobsCommuteTimeModal) |
| `href` | `string` | — | If set, navigates to this URL on search instead of in-page search |
| `variant_name` | `string` | — | Variant identifier |
| `variant_id` | `string` | — | Variant ID |

#### Compiled HTML

```html
<div class="{classname} custom-search-box {classname}__horizontal?"
     data-testid="jobs-search-container">

  <!-- Keyword Search -->
  <div class="keyword-search">
    <label>{key_search_label}</label>
    <div class="keyword-search__input">
      <input type="text" placeholder="{key_search_placeholder}" />
      <div class="keyword-search__icon-container">
        <!-- Clear icon (shown when input has value) -->
        <!-- Search icon -->
      </div>
      <!-- Autocomplete dropdown (shown when typing, if enable_auto_complete_job_titles) -->
      <div class="autocomplete">
        <div class="autocomplete__suggestion">
          <div class="keyword__suggestion__item">...</div>
        </div>
      </div>
    </div>
  </div>

  <!-- Location Search -->
  <div class="location-search">
    <label>{location_search_label}</label>
    <div class="location-search__input">
      <input type="text" placeholder="{location_search_placeholder}" />
      <div class="icon-container">
        <!-- If enable_commute_time -->
        <div class="location-search__commute-time">
          <span class="location-search__commute-time-icon"><svg.../></span>
          <span class="location-search__commute-time-label">Commute Time</span>
        </div>
        <!-- Locate me button (if locate_me is true) -->
        <!-- Clear icon -->
      </div>
      <!-- Location autocomplete dropdown -->
      <div class="autocomplete">
        <div class="autocomplete__suggestion">
          <div class="location-item">...</div>
        </div>
      </div>
    </div>
    <!-- Commute time location overlay (shown when commute time is active) -->
    <div class="location-search__commute-time-location">
      <div class="location-search__commute-time-location-inner">
        <svg.../> <!-- Clock icon -->
        <span class="location-search__commute-time-location-label">
          {duration} min {method} from {address}
        </span>
      </div>
      <span class="location-search__commute-time-location-clear">Clear</span>
    </div>
  </div>

  <!-- Search Button -->
  <div class="{classname}__button">
    <button class="{classname}__button-search">{search_button_label}</button>
  </div>

  <!-- Location Permission Alert (if ask_for_location_permission and denied) -->
  <div class="location-denied-alert">
    <h4>{location_permission_modal_header}</h4>
    <p>{location_permission_modal_text}</p>
  </div>

  <!-- Screen reader announcement -->
  <div id="ada-visually-hidden" role="alert" aria-live="assertive"></div>
</div>
```

#### Conditional Rendering

- `horizontal` adds `{classname}__horizontal` class to the wrapper.
- `enable_auto_complete_job_titles` toggles the keyword autocomplete dropdown.
- `enable_commute_time` shows the "Commute Time" button inside the location input.
- When commute time is active, a `.location-search__commute-time-location` overlay replaces the location input.
- `locate_me` shows a geolocation icon button.
- `ask_for_location_permission` enables the permission denied alert.
- `href` changes behavior from in-page search to URL navigation.

---

### JobsListOnly

**`data-react-component="jobs-list-only"`**

Renders the list of job results with structured data (JSON-LD).

#### Props

| Prop | Type | Default | Description |
|---|---|---|---|
| `classname` | `string` | — | Custom CSS class |
| `apply_button_text` | `string` | `"Apply Now"` | Label on the apply button |
| `street_format` | `string` | `"{street_address}, {city} , {state_abbr}"` | Template for formatting job location. Tokens: `{street_address}`, `{city}`, `{region}`, `{state}`, `{state_abbr}`, `{postal_code}`, `{country}` |
| `show_reference` | `boolean` | `false` | Show the job reference/ID next to the title |
| `show_brand` | `boolean` | `true` | Show the brand name row |
| `show_employment_type` | `boolean` | `true` | Show the employment type row |
| `multi_location_text` | `string` | `"more locations"` | Text after "+N" for jobs with multiple locations |
| `remote_location_text` | `string` | `"Remote"` | Badge text for remote jobs |
| `no_results_line1_text` | `string` | — | Title when no results found |
| `no_results_line2_text` | `string` | — | Subtitle when no results found |
| `clear_result_suggestions_title_text` | `string` | — | Suggestions section title |
| `clear_result_suggestions_line1_text` | `string` | — | First suggestion line |
| `clear_result_suggestions_line2_text` | `string` | — | Second suggestion line |
| `clear_result_suggestions_line3_text` | `string` | — | Third suggestion line |
| `clear_result_suggestions_line4_text` | `string` | — | Fourth suggestion line |

#### Compiled HTML

```html
<div class="{classname}" data-testid="jobs-list-only-container">

  <!-- JSON-LD structured data for SEO -->
  <script type="application/ld+json">
    { "@context": "https://schema.org", "@type": "ItemList", "itemListElement": [...] }
  </script>

  <div class="results-container">

    <!-- Loading spinner (shown during fetch) -->
    <div class="loader loader-spinner-3"></div>

    <!-- Job results (shown when totalJob > 0) -->
    <div class="card">
      <ul class="results-list front">

        <!-- Repeated for each job -->
        <li class="results-list__item">
          <div class="results-list__item-header">

            <!-- Commute time badge (shown when commute search is active) -->
            <span class="results-list__item-commute-time">
              <span class="results-list__item-commute-time-inner">
                <span class="results-list__item-commute-time--icon"><svg.../></span>
                {duration} min
              </span>
            </span>

            <h3 class="results-list__item-title">
              <a href="{job_url}">{job.title}</a>
              <!-- If show_reference -->
              <span class="reference">{job.reference}</span>
              <!-- If job is remote -->
              <span class="remote">{remote_location_text}</span>
              <!-- or if not remote -->
              <span class="remote remote--empty"></span>
            </h3>

            <!-- Distance badge (shown when location search has distance) -->
            <div class="results-list__item-distance">
              <svg.../> {distance} mi/km
            </div>
          </div>

          <div class="results-list__item-content">
            <div class="results-list__item-info">

              <!-- Location -->
              <div class="results-list__item-street">
                <svg.../> <!-- Pin icon -->
                {formatted_location}
                <!-- If multiple locations -->
                <span>+{count} {multi_location_text}</span>
              </div>

              <!-- Brand (if show_brand) -->
              <div class="results-list__item-brand">
                <svg.../> {brand_name}
              </div>

              <!-- Employment type (if show_employment_type) -->
              <div class="results-list__item-employment-type">
                <svg.../> {employment_type}
              </div>

              <!-- Additional custom fields from job data -->
              <div class="results-list__item-{field_key}">
                <svg.../> {field_value}
              </div>
            </div>

            <!-- Apply link -->
            <a class="results-list__item-apply" href="{apply_url}">
              {apply_button_text} <svg.../>
            </a>
          </div>
        </li>
        <!-- /Repeated -->

      </ul>
    </div>

    <!-- No results message (shown when totalJob === 0) -->
    <div class="no-results">
      <h3>{no_results_line1_text}</h3>
      <p>{no_results_line2_text}</p>
    </div>

    <!-- Search suggestions (shown when no results and has keyword/filter) -->
    <div class="card primary-color">
      <h4>{clear_result_suggestions_title_text}</h4>
      <ul class="results-list">
        <li>{clear_result_suggestions_line1_text}</li>
        <li>{clear_result_suggestions_line2_text}</li>
        <li>{clear_result_suggestions_line3_text}</li>
        <li>{clear_result_suggestions_line4_text}</li>
      </ul>
    </div>

  </div>
</div>
```

#### Conditional Rendering

- Loading spinner shown during API fetch.
- Job list shown only when `totalJob > 0`.
- Commute time badge shown only when a commute time search is active.
- `show_reference` toggles the reference span.
- Remote badge uses class `remote` or `remote remote--empty` based on `job.isRemote`.
- Distance badge shown only when location search returns distance data.
- `show_brand` toggles the brand row.
- `show_employment_type` toggles the employment type row.
- "+N more locations" shown only when the job has multiple locations.
- No results / suggestions shown only when `totalJob === 0`.
- `street_format` controls the location display. Tokens are replaced with job location fields.

---

### JobsListHeader

**`data-react-component="jobs-list-header"`**

Displays the results count header (e.g., "Showing 1-10 of 50 jobs").

#### Props

| Prop | Type | Default | Description |
|---|---|---|---|
| `classname` | `string` | — | Custom CSS class |
| `showing_text` | `string` | `"Showing"` | Prefix text |
| `of_text` | `string` | `"of"` | "of" connector |
| `jobs_text` | `string` | `"jobs"` | Plural job label |
| `no_jobs_text` | `string` | `"No open jobs meet your search criteria"` | Message when 0 results |
| `results_header` | `string` | — | Custom template string with tokens: `{from}`, `{to}`, `{total_jobs}`. Overrides the default format. |

#### Compiled HTML

**Default format (V1):**

```html
<div class="results-header {classname}" data-testid="jobs-list-header-container">
  <span class="results-header__showing">{showing_text}</span>
  <span class="results-header__content">
    <!-- Shown only if totalJob > pageSize -->
    <span class="results-header__content-paging">
      {startJob}-{endJob} {of_text}
    </span>
    <span class="results-header__content-total">{totalJob}</span>
    {jobs_text}
  </span>
</div>
```

**Custom template format (V2, when `results_header` is set):**

```html
<div class="results-header {classname}" data-testid="jobs-list-header-container">
  <p class="results-header__content">
    <!-- innerHTML from results_header with tokens replaced -->
    <!-- e.g., "Showing <b>1-10</b> of <b>50</b> jobs" -->
  </p>
</div>
```

#### Conditional Rendering

- Hidden completely when `totalJob === 0` and there is no active keyword or filter.
- When `totalJob === 0` but keyword/filter is active, shows `no_jobs_text`.
- Paging range (`1-10 of`) hidden when `totalJob <= pageSize`.
- V2 (`results_header` prop) overrides V1 format entirely.

---

### JobsFilter

**`data-react-component="jobs-filter"`**

Accordion-style filter panel with checkboxes for each facet category.

#### Props

| Prop | Type | Default | Description |
|---|---|---|---|
| `classname` | `string` | — | Custom CSS class |
| `clear_filter_text` | `string` | `"Clear Filters"` | Text on the clear all button |

#### Compiled HTML

```html
<div class="advanced-search-container {classname}" data-testid="jobs-filter-container">
  <div class="filter-group advanced-search">
    <h3>Filter Results</h3>

    <!-- Repeated for each filter category -->
    <div class="filter-category">
      <button class="filter-category__header" aria-expanded="{isExpanded}">
        <span class="filter-category__name">{filter.alias}</span>
        <svg.../> <!-- Chevron icon -->
      </button>

      <!-- Shown only when expanded -->
      <div class="filter-options-list-container">

        <!-- If <= 10 options: simple list -->
        <div>
          <label class="filter-option">
            <input type="checkbox" checked="{isChecked}" />
            <span class="option-value text">{option.custom_value}</span>
            <span class="option-value count">{option.doc_count}</span>
          </label>
          <!-- ... more options -->
        </div>

        <!-- If > 10 options: virtualized list (react-window) -->
        <div style="height: {calculated}; overflow: auto;">
          <!-- VariableSizeList renders visible options only -->
          <label class="filter-option">...</label>
        </div>

      </div>
    </div>
    <!-- /Repeated -->

  </div>

  <div class="reset-filters">
    <button>{clear_filter_text}</button>
  </div>
</div>
```

#### Conditional Rendering

- Filter categories come from Redux state (server-configured facets).
- Each category can be expanded/collapsed via accordion toggle.
- When a category has >10 options, a virtualized list (react-window) is used for performance.
- The clear button resets all selected filters.

---

### JobsPagination

**`data-react-component="jobs-pagination"`**

Page navigation with numbered links, first/previous/next/last buttons, and optional page size selector.

#### Props

| Prop | Type | Default | Description |
|---|---|---|---|
| `classname` | `string` | — | Custom CSS class |
| `useicon` | `boolean` | `false` | Use arrow SVG icons instead of text for first/prev/next/last |
| `scrolltowhenchangepage` | `string` | — | CSS selector to scroll to on page change |
| `results_per_page_enabled` | `boolean` | `false` | Show the page size dropdown |
| `results_per_page_text` | `string` | — | Label for the page size dropdown |
| `page_size_options` | `array` | — | Array of `{ value: number, is_default: boolean }` objects |
| `originalUrl` | `string` | — | Base URL for generating `href` attributes on page links |

#### Compiled HTML

```html
<div class="pagination-container {classname}" data-testid="jobs-pagination-container">
  <div class="pagination" role="region" aria-label="Pagination">
    <nav class="page-links" aria-label="Page navigation">
      <ul class="pagination__list">

        <!-- First page -->
        <li>
          <a class="page-link page-link-first" href="{url}/page/1" aria-label="First page">
            <!-- If useicon: <svg.../> else: "First" -->
          </a>
        </li>

        <!-- Previous page -->
        <li>
          <a class="page-link page-link-previous" href="{url}/page/{prev}" aria-label="Previous page">
            <!-- If useicon: <svg.../> else: "Previous" -->
          </a>
        </li>

        <!-- Page numbers (up to 10 visible) -->
        <li>
          <a class="page-link current-page"
             href="{url}/page/{n}"
             aria-current="page"
             aria-label="Page {n}">
            {n}
          </a>
        </li>
        <li>
          <a class="page-link" href="{url}/page/{n}" aria-label="Page {n}">
            {n}
          </a>
        </li>
        <!-- ... more pages -->

        <!-- Next page -->
        <li>
          <a class="page-link page-link-next" href="{url}/page/{next}" aria-label="Next page">
            <!-- If useicon: <svg.../> else: "Next" -->
          </a>
        </li>

        <!-- Last page -->
        <li>
          <a class="page-link page-link-last" href="{url}/page/{last}" aria-label="Last page">
            <!-- If useicon: <svg.../> else: "Last" -->
          </a>
        </li>

      </ul>
    </nav>

    <!-- Page size selector (if results_per_page_enabled) -->
    <label for="page-size-select">{results_per_page_text}</label>
    <select id="page-size-select">
      <option value="{n}" selected="{is_default}">{n}</option>
      <!-- ... more options -->
    </select>
  </div>
</div>
```

#### Conditional Rendering

- Hidden entirely when `totalJob === 0` or only 1 page of results.
- `useicon` switches first/prev/next/last between SVG arrows and text labels.
- `results_per_page_enabled` toggles the page size `<select>`.
- Current page link gets `class="current-page"` and `aria-current="page"`.
- Page number range slides as current page changes (windowed display of up to 10 pages).

---

### JobsSortBy

**`data-react-component="jobs-sort-by"`**

Dropdown to sort job results by relevance, distance, date, or title.

#### Props

| Prop | Type | Default | Description |
|---|---|---|---|
| `classname` | `string` | — | Custom CSS class |
| `sort_by_text` | `string` | `"Sort by"` | Label text |
| `headline_text` | `string` | — | Label for the "Title" sort option |
| `distance_text` | `string` | — | Label for the "Distance" sort option |
| `score_text` | `string` | — | Label for the "Relevance" sort option |
| `start_date_text` | `string` | — | Label for the "Date" sort option |
| `sort_by_field` | `string` | — | Field name that maps to `start_date_text` |
| `default_sort_option` | `string` | — | Initially selected sort value |

#### Compiled HTML

```html
<div class="{classname} jobs-sort-by_container" data-testid="jobs-sort-by-container">
  <span id="jobs-sort-by-label" class="jobs-sort-by_label">{sort_by_text}</span>
  <div class="jobs-sort-by_select_wrapper">

    <button id="jobs-sort-by-button"
            aria-labelledby="jobs-sort-by-label jobs-sort-by-current"
            aria-haspopup="listbox"
            aria-expanded="{isOpen}"
            aria-controls="jobs-sort-by-options">
      <span id="jobs-sort-by-current">{selected_label}</span>
      <span class="jobs-sort-by_select_display_icon"><svg.../></span>
    </button>

    <ul id="jobs-sort-by-options"
        role="listbox"
        hidden="{!isOpen}"
        aria-labelledby="jobs-sort-by-label">
      <li id="jobs-sort-by-option-0"
          role="option"
          data-value="{value}"
          aria-selected="{isFocused}"
          aria-label="{label}">
        {label}
      </li>
      <!-- ... more options -->
    </ul>

  </div>
</div>
```

#### Conditional Rendering / Dynamic Options

Available options change based on search state:

| Condition | Available Options |
|---|---|
| Commute time search active | Relevance only |
| Keyword entered | Date, Title, Relevance |
| Location selected | Date, Title, Distance |
| Keyword + Location | Date, Title, Relevance, Distance |
| No keyword, no location | Date, Title |

---

### JobsRadius

**`data-react-component="jobs-radius"`**

Dropdown to select the search radius distance from the searched location.

#### Props

| Prop | Type | Default | Description |
|---|---|---|---|
| `classname` | `string` | — | Custom CSS class |
| `enable_kilometers` | `boolean` | `false` | Use "km" instead of "mi" units |
| `radius_text` | `string` | `"Radius"` | Label text |
| `exact_text` | `string` | `"Exact"` | Label for the exact match option (radius = 0) |
| `radius_items` | `string[]` | `["5","10","15","20","25","50","100","Exact"]` | List of radius values to display |
| `default_radius_item` | `string` | `"15"` | Default selected radius |

#### Compiled HTML

```html
<div class="{classname} jobs-radius_container" data-testid="jobs-radius-container">
  <span id="jobs-radius-label" class="jobs-radius_label">{radius_text}</span>
  <div class="jobs-radius_select_wrapper">

    <button id="jobs-radius-button"
            class="jobs-radius_select_display {disabled ? 'disabled' : ''}"
            aria-labelledby="jobs-radius-label jobs-radius-current"
            aria-haspopup="listbox"
            aria-expanded="{isOpen}"
            aria-controls="jobs-radius-options"
            disabled="{isExactOnly}">
      <span id="jobs-radius-current">{selected_label} {unit}</span>
      <span class="jobs-radius_select_display_icon"><svg.../></span>
    </button>

    <ul id="jobs-radius-options"
        role="listbox"
        hidden="{!isOpen}"
        aria-labelledby="jobs-radius-label">
      <li id="jobs-radius-option-0"
          role="option"
          data-value="{value}"
          aria-selected="{isFocused}"
          aria-label="{label}">
        {label}
      </li>
      <!-- ... more options -->
    </ul>

  </div>
</div>
```

#### Conditional Rendering

- **Hidden entirely** when no location is searched or when commute time is active.
- Options filtered by location type:
  - **STATE / COUNTRY**: Only "Exact" (value = 0).
  - **FREE_TEXT**: Only radius values (exclude "Exact").
  - **CITY**: All options shown.
- Button gets `disabled` + class `disabled` when only "Exact" is available.
- Unit label is "km" if `enable_kilometers`, otherwise "mi".

---

### JobsCurrentSearches

**`data-react-component="jobs-current-searches"`**

Displays active search filters as removable tags/chips.

#### Props

| Prop | Type | Default | Description |
|---|---|---|---|
| `classname` | `string` | — | Custom CSS class |
| `show_title` | `boolean` | `true` | Show the title heading |
| `title` | `string` | `"Search Filters"` | Title text |

#### Compiled HTML

```html
<div class="{classname}" data-testid="jobs-current-searches-container">

  <!-- If show_title -->
  <h3>{title}</h3>

  <ul class="jobs-current-searches__tag-list">

    <!-- Repeated for each active filter (keyword, location, facets) -->
    <li data-type="{filter_type}">
      <span class="jobs-current-searches__tag-label">{filter_value}</span>
      <button class="jobs-current-searches__tag-close" aria-label="Remove {filter_value}">
        <svg.../> <!-- Close/X icon -->
      </button>
    </li>
    <!-- /Repeated -->

  </ul>

  <!-- Screen reader announcement -->
  <div role="status" aria-live="polite"></div>
</div>
```

#### Conditional Rendering

- Tags are dynamically generated from Redux state: keyword, location name, and selected facet filters.
- Each tag has a `data-type` attribute indicating the filter type (e.g., `"keyword"`, `"location"`, facet field name).
- Clicking the close button removes that filter and triggers a new search.
- `show_title` toggles the `<h3>` heading.

---

### JobsCurrentLocation

**`data-react-component="jobs-current-location"`**

Displays a contextual message about the user's current location-based search.

#### Props

| Prop | Type | Default | Description |
|---|---|---|---|
| `classname` | `string` | — | Custom CSS class |
| `text_results` | `string` | `"These results are close to {location}"` | Message for city/radius searches. Token: `{location}` |
| `text_area_city` | `string` | `"Here are results in {location}"` | Message for state/country/exact searches. Token: `{location}` |

#### Compiled HTML

```html
<div class="{classname}" data-testid="jobs-current-location-container">
  <!-- One of the following, rendered as innerHTML -->
  <div>These results are close to <b>San Francisco, CA</b></div>
  <!-- OR -->
  <div>Here are results in <b>California</b></div>
</div>
```

#### Conditional Rendering

- **Hidden** (empty `<div>`) when no location is selected or location type is `FREE_TEXT`.
- Shows `text_area_city` when location type is `STATE`, `COUNTRY`, or `radius === 0` (exact).
- Shows `text_results` when location type is `CITY` with `radius > 0`.
- The `{location}` token is replaced with the actual location name.

---

### JobsCommuteTimeModal

**`data-react-component="jobs-commute-time-modal"`**

Modal dialog for configuring commute time search parameters.

#### Props

| Prop | Type | Default | Description |
|---|---|---|---|
| `control_id` | `string` | — | Unique ID for the modal instance |
| `ask_for_location_permission` | `boolean` | `false` | Request browser geolocation permission |
| `advanced_commute_time_settings` | `object` | — | Text overrides (see below) |

**`advanced_commute_time_settings` fields:**

| Field | Type | Description |
|---|---|---|
| `model_header_label` | `string` | Modal title |
| `location_search_label` | `string` | Location input label |
| `location_search_placeholder` | `string` | Location input placeholder |
| `duration_header` | `string` | Duration section label |
| `transportation_type_header` | `string` | Transportation section label |
| `car_transportation_label` | `string` | Car option label |
| `train_transportation_label` | `string` | Transit option label |
| `person_transportation_label` | `string` | Walking option label |
| `bicycle_transportation_label` | `string` | Bicycle option label |
| `traffic_estimation_toggle_label` | `string` | Traffic toggle label |
| `confirm_button_text` | `string` | Confirm button text |
| `cancel_button_text` | `string` | Cancel button text |
| `location_not_exist_message` | `string` | Error when location is invalid |

#### Compiled HTML

```html
<!-- Modal overlay -->
<div class="modal__overlay"></div>
<div class="modal__container">
  <form>
    <div class="commute-time-modal" data-testid="commute-time-modal-container">

      <!-- Header -->
      <div class="commute-time-modal__header">
        <div class="commute-time-modal__title">
          <span class="commute-time-modal__title__icon"><svg.../></span>
          <span>{model_header_label}</span>
        </div>
        <button class="commute-time-modal__close"><svg.../></button>
      </div>

      <!-- Body -->
      <!-- Location input -->
      <div class="commute-time-modal__form-group">
        <label class="commute-time-modal__form-group__label">{location_search_label}</label>
        <div class="commute-time-modal__location {hasError ? 'has-error' : ''}">
          <svg.../> <!-- Pin icon -->
          <div class="commute-time-modal__location__input">
            <div class="autocomplete">
              <input class="autocomplete__input" placeholder="{location_search_placeholder}" />
              <div class="autocomplete__suggestion">
                <div class="location-item">...</div>
              </div>
            </div>
          </div>
          <!-- Clear and Locate Me buttons -->
        </div>
        <div class="commute-time-modal__error">{location_not_exist_message}</div>
      </div>

      <!-- Duration options -->
      <div class="commute-time-modal__form-group">
        <label>{duration_header}</label>
        <div class="commute-time-modal__commute-method-options">
          <ul role="listbox">
            <li role="option" class="{active}">15</li>
            <li role="option" class="{active}">30</li>
            <li role="option" class="{active}">45</li>
            <li role="option" class="{active}">60</li>
          </ul>
        </div>
      </div>

      <!-- Transportation type -->
      <div class="commute-time-modal__form-group">
        <label>{transportation_type_header}</label>
        <div class="commute-time-modal__transportation-options">
          <ul role="listbox">
            <li role="option" class="{active}">
              <span class="commute-time-modal__transportation-options__icon"><svg.../></span>
              <span class="commute-time-modal__transportation-options__label">{car_transportation_label}</span>
            </li>
            <li role="option">...{train_transportation_label}</li>
            <li role="option">...{person_transportation_label}</li>
            <li role="option">...{bicycle_transportation_label}</li>
          </ul>
        </div>
      </div>

      <!-- Traffic estimation toggle -->
      <div class="commute-time-modal__form-group inline">
        <span>{traffic_estimation_toggle_label}</span>
        <label class="commute-time-modal__estimation__switch">
          <input type="checkbox" class="commute-time-modal__estimation__input" />
          <span class="commute-time-modal__estimation__slider"></span>
        </label>
      </div>

      <!-- Footer -->
      <div class="commute-time-modal__footer">
        <button class="commute-time-modal__button">{cancel_button_text}</button>
        <button class="commute-time-modal__button btn-brand {disabled}">{confirm_button_text}</button>
      </div>

    </div>
  </form>
</div>
```

#### Conditional Rendering

- Entire modal is hidden/shown based on `open` state (controlled by parent).
- Location error message shown only when location validation fails.
- Confirm button gets class `disabled` when no location is entered.
- Duration and transportation options get class `active` when selected.
- `has-error` class added to location input wrapper when validation fails.

---

### SimilarJobs

**`data-react-component="similar-jobs"`**

Displays a row of similar job cards, typically shown on a job details page.

#### Props

| Prop | Type | Default | Description |
|---|---|---|---|
| `classname` | `string` | — | Custom CSS class |
| `number_of_similar_jobs` | `number` | — | Maximum number of similar jobs to display |
| `search_radius_enabled` | `boolean` | — | Include radius in similar jobs search |
| `search_radius_value` | `number` | — | Radius value for search |
| `include_remote_jobs` | `boolean` | — | Include remote jobs in results |
| `apply_button_text` | `string` | `"Apply Now"` | Button label |
| `street_format` | `string` | `"{street_address}, {city} , {state_abbr}"` | Location format template |
| `remote_location_text` | `string` | `"Remote"` | Remote badge text |
| `multi_location_text` | `string` | `"more locations"` | Multi-location text |
| `apply_url_destination` | `string` | `"job_details"` | `"job_details"` or `"ats_apply_url"` |

#### Compiled HTML

```html
<div class="{classname}" role="region" aria-label="Similar Jobs"
     data-testid="similar-jobs-container">
  <div class="similar-jobs__content">

    <!-- Repeated for each similar job -->
    <div class="similar-jobs-item" role="article">

      <h3 class="similar-jobs-item__title">
        <a href="{job_url}">{job.title}</a>
      </h3>

      <!-- Remote badge -->
      <span class="similar-jobs-item__remote">{remote_location_text}</span>
      <!-- OR if not remote: -->
      <span class="similar-jobs-item__remote--empty"></span>

      <!-- Location -->
      <div class="similar-jobs-item__location">
        <svg.../> <!-- Pin icon -->
        <span class="similar-jobs-item__location__label">{formatted_location}</span>
        <!-- If multiple locations -->
        <span class="similar-jobs-item__location__more">
          <span class="similar-jobs-item__location__more__count">+{count}</span>
          <span class="similar-jobs-item__location__more__text">{multi_location_text}</span>
        </span>
      </div>
      <!-- OR if no location: -->
      <div class="similar-jobs-item__location--empty"></div>

      <!-- Apply button -->
      <div class="similar-jobs-item__apply">
        <a class="similar-jobs-item__apply__button" href="{apply_url}">
          {apply_button_text}
        </a>
      </div>

    </div>
    <!-- /Repeated -->

  </div>
</div>
```

#### Conditional Rendering

- **Hidden entirely** when no similar jobs are found (`similarJobs.length === 0`).
- Remote badge uses `similar-jobs-item__remote` or `similar-jobs-item__remote--empty` based on job remote status.
- Location uses `similar-jobs-item__location` or `similar-jobs-item__location--empty` based on presence of location data.
- Multi-location "more" span shown only for jobs with more than one location.
- `apply_url_destination` controls whether the apply link goes to the job details page or directly to the ATS apply URL.

---

## Shared Types

```typescript
enum LocationTypeEnum {
  FREE_TEXT = 1,
  CITY = 2,
  STATE = 3,
  COUNTRY = 4,
  SELECTED_JSS_LOCATION = 5,
}

enum JobApplyUrlDestinationEnum {
  JOB_DETAILS = 'job_details',
  ATS_APPLY_URL = 'ats_apply_url',
}
```

## Accessibility Summary

All components follow ARIA best practices:

- **Dropdowns** (JobsSortBy, JobsRadius): `role="listbox"`, `role="option"`, `aria-expanded`, `aria-selected`, keyboard navigation (Arrow Up/Down, Enter, Escape).
- **Modal** (JobsCommuteTimeModal): Focus trap with Tab/Shift+Tab cycling, Escape to close.
- **Filter** (JobsFilter): `aria-expanded` on accordion headers.
- **Pagination**: `aria-current="page"` on current page, `aria-label` on all links.
- **Screen readers**: `role="alert"` and `aria-live="assertive"` for dynamic search result announcements.
- **Visually hidden**: `#ada-visually-hidden` element for screen-reader-only announcements.
