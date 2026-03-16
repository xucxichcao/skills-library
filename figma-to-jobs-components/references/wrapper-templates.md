# Component Wrapper Templates

Copy-paste templates for each component wrapper. Replace `{placeholders}` with values from the Figma design. Generate a unique UUID v4 for each `{uuid}` placeholder.

For the full SSR HTML that goes inside each wrapper, refer to `components.md`.

---

## Search Box

```html
<div class="c-jobs-search-wrap jobs-search"
     data-react-component="search-box"
     data-guid="{uuid}"
     data-variant="jobs-search"
     data-react-prop-classname="c-jobs-search"
     data-react-prop-horizontal="{true|false}"
     data-react-prop-key_search_placeholder="{e.g. Type to Search: e.g Crew Member}"
     data-react-prop-key_search_label="{e.g. I'm Looking For}"
     data-react-prop-add_eyebrow_keyword_field="true"
     data-react-prop-location_search_placeholder="{e.g. Zip Code or City, State}"
     data-react-prop-location_search_label="{e.g. Positions Near}"
     data-react-prop-add_eyebrow_location_field="true"
     data-react-prop-locate_me="{true|false}"
     data-react-prop-ask_for_location_permission="true"
     data-react-prop-location_permission_modal_header="Oops! We couldn't get your location."
     data-react-prop-location_permission_modal_text="Please turn on location services in your browser settings or search by zipcode."
     data-react-prop-disable_location_type_ahead_google_search="false"
     data-react-prop-location_type_ahead_source="[&quot;job_locations&quot;]"
     data-react-prop-disable_location_type_ahead_location_address="undefined"
     data-react-prop-disable_location_type_ahead_city_state_postalcode="undefined"
     data-react-prop-enable_hint_text="undefined"
     data-react-prop-search_button_label="{e.g. Search}"
     data-react-prop-href="/"
     data-react-prop-search_mode="undefined"
     data-react-prop-enable_commute_time="{true|false}"
     data-react-prop-enable_auto_complete_job_titles="false"
     data-react-prop-advanced_commute_time_settings="{&quot;commute_time_button_label&quot;:&quot;Commute&quot;,&quot;selected_commute_time_from_label&quot;:&quot;From&quot;,&quot;selected_commute_time_to_label&quot;:&quot;to jobs within&quot;,&quot;model_header_label&quot;:&quot;Commute Time&quot;,&quot;location_search_label&quot;:&quot;Your Location&quot;,&quot;location_search_placeholder&quot;:&quot;Enter your address&quot;,&quot;duration_header&quot;:&quot;Duration (minutes)&quot;,&quot;transportation_type_header&quot;:&quot;Transportation Type&quot;,&quot;car_transportation_label&quot;:&quot;Car&quot;,&quot;train_transportation_label&quot;:&quot;Transit&quot;,&quot;person_transportation_label&quot;:&quot;Walk&quot;,&quot;bicycle_transportation_label&quot;:&quot;Bicycle&quot;,&quot;traffic_estimation_toggle_label&quot;:&quot;Include traffic estimation&quot;,&quot;confirm_button_text&quot;:&quot;Search&quot;,&quot;cancel_button_text&quot;:&quot;Cancel&quot;,&quot;location_not_exist_message&quot;:&quot;Sorry, we don&#39;t recognize the entered location. Try entering a different location.&quot;}"
     data-react-prop-variant_name="jobs-search"
     data-react-prop-variant_id="{uuid}">

  <!-- SSR content: see components.md JobsSearch compiled HTML -->
  <div class="c-jobs-search custom-search-box" data-testid="jobs-search-container">
    <div class="keyword-search">
      <label>I'm Looking For</label>
      <div class="keyword-search__input">
        <input type="text" placeholder="{placeholder}" />
        <div class="keyword-search__icon-container">
          <span class="keyword-search__icon">
            <svg width="20" height="20" viewBox="0 0 20 20" fill="none" xmlns="http://www.w3.org/2000/svg"><path d="M17.5 17.5L13.875 13.875M15.8333 9.16667C15.8333 12.8486 12.8486 15.8333 9.16667 15.8333C5.48477 15.8333 2.5 12.8486 2.5 9.16667C2.5 5.48477 5.48477 2.5 9.16667 2.5C12.8486 2.5 15.8333 5.48477 15.8333 9.16667Z" stroke="currentColor" stroke-width="1.66667" stroke-linecap="round" stroke-linejoin="round"/></svg>
          </span>
        </div>
      </div>
    </div>
    <div class="location-search">
      <label>Positions Near</label>
      <div class="location-search__input">
        <input type="text" placeholder="{placeholder}" />
        <div class="icon-container">
          <!-- locate me icon if enabled -->
        </div>
      </div>
    </div>
    <div class="c-jobs-search__button">
      <button class="c-jobs-search__button-search">{button_label}</button>
    </div>
    <div id="ada-visually-hidden" role="alert" aria-live="assertive"></div>
  </div>
</div>
```

**Notes:**
- If `horizontal="true"`, the inner div gets class `c-jobs-search__horizontal` added
- `classname` determines the prefix for `__button` and `__button-search` classes
- Adjust the inner `classname` references to match your `data-react-prop-classname` value

---

## Current Searches

```html
<div class="c-jobs-current-searches-wrap jobs-current-searches"
     data-react-component="jobs-current-searches"
     data-guid="{uuid}"
     data-variant="jobs-current-searches"
     data-react-prop-classname="c-jobs-current-searches"
     data-react-prop-show_title="true"
     data-react-prop-title="Search Filters"
     data-react-prop-variant_name="jobs-current-searches"
     data-react-prop-variant_id="{uuid}">

  <div class="c-jobs-current-searches" data-testid="jobs-current-searches-container">
    <h3>Search Filters</h3>
    <ul class="jobs-current-searches__tag-list">
      <!-- Tags rendered dynamically from Redux state -->
    </ul>
    <div role="status" aria-live="polite"></div>
  </div>
</div>
```

---

## Filters

```html
<div class="c-jobs-filter-wrap jobs-filter"
     data-react-component="jobs-filter"
     data-guid="{uuid}"
     data-variant="jobs-filter"
     data-react-prop-classname="c-jobs-filter"
     data-react-prop-filter_options="[{&quot;alias&quot;:&quot;{Category Name}&quot;,&quot;field&quot;:&quot;{field_name}&quot;}]"
     data-react-prop-clear_filter_text="{e.g. Clear Filters}"
     data-react-prop-variant_name="jobs-filter"
     data-react-prop-variant_id="{uuid}">

  <div class="advanced-search-container c-jobs-filter" data-testid="jobs-filter-container">
    <div class="filter-group advanced-search">
      <h3>Filter Results</h3>
      <!-- Filter categories rendered from filter_options prop -->
      <div class="filter-category">
        <button class="filter-category__header" aria-expanded="false">
          <span class="filter-category__name">{Category Name}</span>
          <svg><!-- chevron --></svg>
        </button>
        <div class="filter-options-list-container">
          <!-- Options rendered from Redux state -->
        </div>
      </div>
    </div>
    <div class="reset-filters">
      <button>{clear_filter_text}</button>
    </div>
  </div>
</div>
```

**Common filter_options values** (adjust based on Figma):
```
[
  {"alias": "Category", "field": "category"},
  {"alias": "Location", "field": "location"},
  {"alias": "Employment Type", "field": "employment_type"},
  {"alias": "Brand", "field": "brand"}
]
```

---

## Results Header

```html
<div class="c-jobs-list-header-wrap jobs-list-header"
     data-react-component="jobs-list-header"
     data-guid="{uuid}"
     data-variant="jobs-list-header"
     data-react-prop-classname="c-jobs-list-header"
     data-react-prop-results_header="{e.g. Showing &lt;b&gt;{from}-{to}&lt;/b&gt; of &lt;b&gt;{total_jobs}&lt;/b&gt; jobs}"
     data-react-prop-variant_name="jobs-list-header"
     data-react-prop-variant_id="{uuid}">

  <div class="results-header c-jobs-list-header" data-testid="jobs-list-header-container">
    <p class="results-header__content">
      Showing <b>1-10</b> of <b>50</b> jobs
    </p>
  </div>
</div>
```

---

## Sort By

```html
<div class="c-jobs-sort-by-wrap jobs-sort-by"
     data-react-component="jobs-sort-by"
     data-guid="{uuid}"
     data-variant="jobs-sort-by"
     data-react-prop-classname="c-jobs-sort-by"
     data-react-prop-sort_by_text="{e.g. Sort by}"
     data-react-prop-headline_text="Title"
     data-react-prop-distance_text="Distance"
     data-react-prop-score_text="Relevance"
     data-react-prop-start_date_text="Date"
     data-react-prop-variant_name="jobs-sort-by"
     data-react-prop-variant_id="{uuid}">

  <div class="c-jobs-sort-by jobs-sort-by_container" data-testid="jobs-sort-by-container">
    <span id="jobs-sort-by-label" class="jobs-sort-by_label">{sort_by_text}</span>
    <div class="jobs-sort-by_select_wrapper">
      <button id="jobs-sort-by-button" aria-labelledby="jobs-sort-by-label jobs-sort-by-current" aria-haspopup="listbox" aria-expanded="false" aria-controls="jobs-sort-by-options">
        <span id="jobs-sort-by-current">Date</span>
        <span class="jobs-sort-by_select_display_icon"><svg><!-- chevron --></svg></span>
      </button>
      <ul id="jobs-sort-by-options" role="listbox" hidden aria-labelledby="jobs-sort-by-label">
        <li role="option" data-value="start_date" aria-label="Date">Date</li>
        <li role="option" data-value="headline" aria-label="Title">Title</li>
      </ul>
    </div>
  </div>
</div>
```

---

## Radius

```html
<div class="c-jobs-radius-wrap jobs-radius"
     data-react-component="jobs-radius"
     data-guid="{uuid}"
     data-variant="jobs-radius"
     data-react-prop-classname="c-jobs-radius"
     data-react-prop-radius_text="{e.g. Radius}"
     data-react-prop-exact_text="Exact"
     data-react-prop-enable_kilometers="false"
     data-react-prop-variant_name="jobs-radius"
     data-react-prop-variant_id="{uuid}">

  <div class="c-jobs-radius jobs-radius_container" data-testid="jobs-radius-container">
    <span id="jobs-radius-label" class="jobs-radius_label">{radius_text}</span>
    <div class="jobs-radius_select_wrapper">
      <button id="jobs-radius-button" class="jobs-radius_select_display" aria-labelledby="jobs-radius-label jobs-radius-current" aria-haspopup="listbox" aria-expanded="false" aria-controls="jobs-radius-options">
        <span id="jobs-radius-current">15 mi</span>
        <span class="jobs-radius_select_display_icon"><svg><!-- chevron --></svg></span>
      </button>
      <ul id="jobs-radius-options" role="listbox" hidden aria-labelledby="jobs-radius-label">
        <li role="option" data-value="5">5 mi</li>
        <li role="option" data-value="10">10 mi</li>
        <li role="option" data-value="15">15 mi</li>
        <li role="option" data-value="25">25 mi</li>
        <li role="option" data-value="50">50 mi</li>
        <li role="option" data-value="100">100 mi</li>
        <li role="option" data-value="0">Exact</li>
      </ul>
    </div>
  </div>
</div>
```

---

## Current Location

```html
<div class="c-jobs-current-location-wrap jobs-current-location"
     data-react-component="jobs-current-location"
     data-guid="{uuid}"
     data-variant="jobs-current-location"
     data-react-prop-classname="c-jobs-current-location"
     data-react-prop-text_results="These results are close to {location}"
     data-react-prop-text_area_city="Here are results in {location}"
     data-react-prop-variant_name="jobs-current-location"
     data-react-prop-variant_id="{uuid}">

  <div class="c-jobs-current-location" data-testid="jobs-current-location-container">
    <!-- Content rendered dynamically based on search state -->
  </div>
</div>
```

---

## Jobs List

```html
<div class="c-jobs-list-wrap jobs-list-only"
     data-react-component="jobs-list-only"
     data-guid="{uuid}"
     data-variant="jobs-list-only"
     data-react-prop-classname="c-jobs-list"
     data-react-prop-apply_button_text="{e.g. Apply Now}"
     data-react-prop-show_brand="{true|false}"
     data-react-prop-show_employment_type="{true|false}"
     data-react-prop-show_reference="{false}"
     data-react-prop-street_format="{city}, {state_abbr}"
     data-react-prop-multi_location_text="more locations"
     data-react-prop-remote_location_text="Remote"
     data-react-prop-no_results_line1_text="Sorry, we're not able to load results for your search."
     data-react-prop-no_results_line2_text="Please refine your keywords in the search bar above and try again."
     data-react-prop-clear_result_suggestions_title_text="Suggestions"
     data-react-prop-clear_result_suggestions_line1_text="Try different keywords"
     data-react-prop-clear_result_suggestions_line2_text="Make sure everything is spelled correctly"
     data-react-prop-clear_result_suggestions_line3_text="If possible, consider trying jobs at a different location, perhaps another nearby city."
     data-react-prop-clear_result_suggestions_line4_text="Try clearing your search filters and starting over."
     data-react-prop-variant_name="jobs-list-only"
     data-react-prop-variant_id="{uuid}">

  <div class="c-jobs-list" data-testid="jobs-list-only-container">
    <div class="results-container">
      <div class="card">
        <ul class="results-list front">
          <!-- Repeated for each job (use 3-5 placeholder jobs) -->
          <li class="results-list__item">
            <div class="results-list__item-header">
              <h3 class="results-list__item-title">
                <a href="/jobs/sample-job">Sample Job Title</a>
                <span class="remote remote--empty"></span>
              </h3>
            </div>
            <div class="results-list__item-content">
              <div class="results-list__item-info">
                <div class="results-list__item-street">
                  <svg width="16" height="16" viewBox="0 0 16 16" fill="none"><path d="M8 8.5C9.10457 8.5 10 7.60457 10 6.5C10 5.39543 9.10457 4.5 8 4.5C6.89543 4.5 6 5.39543 6 6.5C6 7.60457 6.89543 8.5 8 8.5Z" stroke="currentColor"/><path d="M8 14.5C8 14.5 13 10.5 13 6.5C13 3.73858 10.7614 1.5 8 1.5C5.23858 1.5 3 3.73858 3 6.5C3 10.5 8 14.5 8 14.5Z" stroke="currentColor"/></svg>
                  San Francisco, CA
                </div>
                <!-- brand row if show_brand -->
                <!-- employment type row if show_employment_type -->
              </div>
              <a class="results-list__item-apply" href="/jobs/sample-job">
                {apply_button_text}
                <svg width="16" height="16" viewBox="0 0 16 16" fill="none"><path d="M3.33337 8H12.6667M12.6667 8L8.00004 3.33337M12.6667 8L8.00004 12.6667" stroke="currentColor" stroke-linecap="round" stroke-linejoin="round"/></svg>
              </a>
            </div>
          </li>
          <!-- /Repeated -->
        </ul>
      </div>
    </div>
  </div>
</div>
```

**Optional props for extra fields:**
```
data-react-prop-job_card_extra_fields_enabled="true"
data-react-prop-job_card_extra_fields="[{&quot;attribute_name&quot;:&quot;{field}&quot;,&quot;classname&quot;:&quot;results-list__custom1&quot;}]"
```

---

## Pagination

```html
<div class="c-jobs-pagination-wrap jobs-pagination"
     data-react-component="jobs-pagination"
     data-guid="{uuid}"
     data-variant="jobs-pagination"
     data-react-prop-classname="c-jobs-pagination"
     data-react-prop-useicon="{true|false}"
     data-react-prop-scrolltowhenchangepage=".c-jobs"
     data-react-prop-results_per_page_enabled="{true|false}"
     data-react-prop-variant_name="jobs-pagination"
     data-react-prop-variant_id="{uuid}">

  <div class="pagination-container c-jobs-pagination" data-testid="jobs-pagination-container">
    <div class="pagination" role="region" aria-label="Pagination">
      <nav class="page-links" aria-label="Page navigation">
        <ul class="pagination__list">
          <li><a class="page-link page-link-first" href="/page/1" aria-label="First page">&laquo;</a></li>
          <li><a class="page-link page-link-previous" href="/page/1" aria-label="Previous page">&lsaquo;</a></li>
          <li><a class="page-link current-page" href="/page/1" aria-current="page" aria-label="Page 1">1</a></li>
          <li><a class="page-link" href="/page/2" aria-label="Page 2">2</a></li>
          <li><a class="page-link" href="/page/3" aria-label="Page 3">3</a></li>
          <li><a class="page-link page-link-next" href="/page/2" aria-label="Next page">&rsaquo;</a></li>
          <li><a class="page-link page-link-last" href="/page/5" aria-label="Last page">&raquo;</a></li>
        </ul>
      </nav>
    </div>
  </div>
</div>
```

---

## Commute Time Modal (if enabled)

```html
<div class="c-jobs-commute-time-modal-wrap jobs-commute-time-modal"
     data-react-component="jobs-commute-time-modal"
     data-guid="{uuid}"
     data-variant="jobs-commute-time-modal"
     data-react-prop-control_id="{uuid}"
     data-react-prop-ask_for_location_permission="true"
     data-react-prop-advanced_commute_time_settings="{same JSON blob as search-box}"
     data-react-prop-variant_name="jobs-commute-time-modal"
     data-react-prop-variant_id="{uuid}">

  <!-- SSR content: see components.md JobsCommuteTimeModal compiled HTML -->
  <!-- Modal is hidden by default, shown when user clicks "Commute Time" in search box -->
</div>
```

---

## Similar Jobs (if used)

```html
<div class="c-similar-jobs-wrap similar-jobs"
     data-react-component="similar-jobs"
     data-guid="{uuid}"
     data-variant="similar-jobs"
     data-react-prop-classname="c-similar-jobs"
     data-react-prop-number_of_similar_jobs="3"
     data-react-prop-apply_button_text="{e.g. Apply Now}"
     data-react-prop-street_format="{city}, {state_abbr}"
     data-react-prop-remote_location_text="Remote"
     data-react-prop-multi_location_text="more locations"
     data-react-prop-variant_name="similar-jobs"
     data-react-prop-variant_id="{uuid}">

  <!-- SSR content: see components.md SimilarJobs compiled HTML -->
</div>
```
