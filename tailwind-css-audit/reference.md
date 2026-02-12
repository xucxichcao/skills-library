# Tailwind v4 Class → CSS Reference

Common Tailwind utility classes and their CSS output. This is not exhaustive — for unlisted classes, derive the CSS from the class name pattern.

## Layout & Display

| Class | CSS |
|-------|-----|
| `block` | `display: block` |
| `inline-block` | `display: inline-block` |
| `inline` | `display: inline` |
| `flex` | `display: flex` |
| `inline-flex` | `display: inline-flex` |
| `grid` | `display: grid` |
| `inline-grid` | `display: inline-grid` |
| `hidden` | `display: none` |
| `contents` | `display: contents` |
| `table` | `display: table` |

## Position

| Class | CSS |
|-------|-----|
| `static` | `position: static` |
| `fixed` | `position: fixed` |
| `absolute` | `position: absolute` |
| `relative` | `position: relative` |
| `sticky` | `position: sticky` |

## Inset / Top / Right / Bottom / Left

Pattern: `.{property}-{value}` → `{css-property}: {value}`

| Class | CSS |
|-------|-----|
| `inset-0` | `inset: 0` |
| `top-0` | `top: 0` |
| `right-0` | `right: 0` |
| `bottom-0` | `bottom: 0` |
| `left-0` | `left: 0` |
| `inset-x-0` | `left: 0; right: 0` |
| `inset-y-0` | `top: 0; bottom: 0` |

## Z-Index

Pattern: `.z-{n}` → `z-index: {n}`; `.-z-{n}` → `z-index: calc({n} * -1)`

## Flexbox

| Class | CSS |
|-------|-----|
| `flex-row` | `flex-direction: row` |
| `flex-col` | `flex-direction: column` |
| `flex-row-reverse` | `flex-direction: row-reverse` |
| `flex-col-reverse` | `flex-direction: column-reverse` |
| `flex-wrap` | `flex-wrap: wrap` |
| `flex-nowrap` | `flex-wrap: nowrap` |
| `flex-1` | `flex: 1` |
| `flex-auto` | `flex: auto` |
| `flex-initial` | `flex: 0 1 auto` |
| `flex-none` | `flex: none` |
| `flex-shrink-0` / `shrink-0` | `flex-shrink: 0` |
| `flex-grow` / `grow` | `flex-grow: 1` |
| `grow-0` | `flex-grow: 0` |

## Grid

| Class | CSS |
|-------|-----|
| `grid-cols-{n}` | `grid-template-columns: repeat({n}, minmax(0, 1fr))` |
| `grid-rows-{n}` | `grid-template-rows: repeat({n}, minmax(0, 1fr))` |
| `col-span-{n}` | `grid-column: span {n} / span {n}` |
| `col-span-full` | `grid-column: 1 / -1` |
| `row-span-{n}` | `grid-row: span {n} / span {n}` |

## Alignment

| Class | CSS |
|-------|-----|
| `items-start` | `align-items: flex-start` |
| `items-end` | `align-items: flex-end` |
| `items-center` | `align-items: center` |
| `items-baseline` | `align-items: baseline` |
| `items-stretch` | `align-items: stretch` |
| `justify-start` | `justify-content: flex-start` |
| `justify-end` | `justify-content: flex-end` |
| `justify-center` | `justify-content: center` |
| `justify-between` | `justify-content: space-between` |
| `justify-around` | `justify-content: space-around` |
| `justify-evenly` | `justify-content: space-evenly` |
| `self-start` | `align-self: flex-start` |
| `self-end` | `align-self: flex-end` |
| `self-center` | `align-self: center` |
| `self-stretch` | `align-self: stretch` |
| `self-auto` | `align-self: auto` |
| `place-items-center` | `place-items: center` |
| `place-content-center` | `place-content: center` |

## Gap

Pattern: `.gap-{value}` → `gap: calc(var(--spacing) * {value})`

| Class | CSS |
|-------|-----|
| `gap-{n}` | `gap: calc(var(--spacing) * {n})` |
| `gap-x-{n}` | `column-gap: calc(var(--spacing) * {n})` |
| `gap-y-{n}` | `row-gap: calc(var(--spacing) * {n})` |
| `gap-[{v}]` | `gap: {v}` |

## Spacing (Margin & Padding)

**Spacing scale**: Uses `calc(var(--spacing) * {n})` in Tailwind v4.

| Prefix | Properties |
|--------|-----------|
| `p-` | `padding` |
| `px-` | `padding-left` + `padding-right` |
| `py-` | `padding-top` + `padding-bottom` |
| `pt-` | `padding-top` |
| `pr-` | `padding-right` |
| `pb-` | `padding-bottom` |
| `pl-` | `padding-left` |
| `m-` | `margin` |
| `mx-` | `margin-left` + `margin-right` |
| `my-` | `margin-top` + `margin-bottom` |
| `mt-` | `margin-top` |
| `mr-` | `margin-right` |
| `mb-` | `margin-bottom` |
| `ml-` | `margin-left` |

Special values: `auto` → `auto`, `0` → `0`, `px` → `1px`

For arbitrary values: `p-[2rem]` → `padding: 2rem`

## Sizing

| Class | CSS |
|-------|-----|
| `w-full` | `width: 100%` |
| `w-auto` | `width: auto` |
| `w-screen` | `width: 100vw` |
| `w-fit` | `width: fit-content` |
| `w-min` | `width: min-content` |
| `w-max` | `width: max-content` |
| `w-{n}` | `width: calc(var(--spacing) * {n})` |
| `w-1/2` | `width: 50%` |
| `w-1/3` | `width: 33.333%` |
| `w-2/3` | `width: 66.667%` |
| `w-[{v}]` | `width: {v}` |
| `h-full` | `height: 100%` |
| `h-auto` | `height: auto` |
| `h-screen` | `height: 100vh` |
| `h-{n}` | `height: calc(var(--spacing) * {n})` |
| `h-[{v}]` | `height: {v}` |
| `min-h-0` | `min-height: 0` |
| `min-h-full` | `min-height: 100%` |
| `min-h-screen` | `min-height: 100vh` |
| `max-w-{n}` | `max-width: calc(var(--spacing) * {n})` |
| `max-w-[{v}]` | `max-width: {v}` |
| `max-w-none` | `max-width: none` |
| `size-full` | `width: 100%; height: 100%` |
| `size-{n}` | `width: calc(var(--spacing) * {n}); height: calc(var(--spacing) * {n})` |

## Typography

| Class | CSS |
|-------|-----|
| `text-left` | `text-align: left` |
| `text-center` | `text-align: center` |
| `text-right` | `text-align: right` |
| `text-justify` | `text-align: justify` |
| `text-{color}` | `color: var(--color-{color})` |
| `text-[{v}]` | `font-size: {v}` |
| `text-xs` | `font-size: 0.75rem; line-height: calc(1 / 0.75)` |
| `text-sm` | `font-size: 0.875rem; line-height: calc(1.25 / 0.875)` |
| `text-base` | `font-size: 1rem; line-height: calc(1.5 / 1)` |
| `text-lg` | `font-size: var(--text-lg); line-height: var(--text-lg--line-height)` |
| `text-xl` | `font-size: 1.25rem; line-height: calc(1.75 / 1.25)` |
| `text-2xl` | `font-size: 1.5rem; line-height: calc(2 / 1.5)` |
| `text-3xl` | `font-size: 1.875rem; line-height: calc(2.25 / 1.875)` |
| `text-4xl` | `font-size: var(--text-4xl); line-height: var(--text-4xl--line-height)` |
| `font-thin` | `font-weight: 100` |
| `font-light` | `font-weight: 300` |
| `font-normal` | `font-weight: 400` |
| `font-medium` | `font-weight: 500` |
| `font-semibold` | `font-weight: var(--font-weight-semibold)` |
| `font-bold` | `font-weight: var(--font-weight-bold)` |
| `font-extrabold` | `font-weight: var(--font-weight-extrabold)` |
| `leading-none` | `line-height: 1` |
| `leading-tight` | `line-height: 1.25` |
| `leading-normal` | `line-height: 1.5` |
| `leading-relaxed` | `line-height: 1.625` |
| `leading-loose` | `line-height: 2` |
| `leading-[{v}]` | `line-height: {v}` |
| `tracking-tight` | `letter-spacing: -0.025em` |
| `tracking-normal` | `letter-spacing: 0` |
| `tracking-wide` | `letter-spacing: 0.025em` |
| `uppercase` | `text-transform: uppercase` |
| `lowercase` | `text-transform: lowercase` |
| `capitalize` | `text-transform: capitalize` |
| `normal-case` | `text-transform: none` |
| `underline` | `text-decoration-line: underline` |
| `no-underline` | `text-decoration-line: none` |
| `italic` | `font-style: italic` |
| `not-italic` | `font-style: normal` |
| `whitespace-nowrap` | `white-space: nowrap` |
| `whitespace-normal` | `white-space: normal` |
| `break-words` | `overflow-wrap: break-word` |
| `truncate` | `overflow: hidden; text-overflow: ellipsis; white-space: nowrap` |
| `line-clamp-{n}` | `display: -webkit-box; -webkit-box-orient: vertical; -webkit-line-clamp: {n}; overflow: hidden` |

## Backgrounds

| Class | CSS |
|-------|-----|
| `bg-{color}` | `background-color: var(--color-{color})` |
| `bg-transparent` | `background-color: transparent` |
| `bg-current` | `background-color: currentColor` |
| `bg-[{v}]` | `background-color: {v}` |
| `bg-cover` | `background-size: cover` |
| `bg-contain` | `background-size: contain` |
| `bg-center` | `background-position: center` |
| `bg-no-repeat` | `background-repeat: no-repeat` |

## Gradients

| Class | CSS |
|-------|-----|
| `bg-gradient-to-r` | `background-image: linear-gradient(to right, var(--tw-gradient-stops))` |
| `bg-gradient-to-l` | `background-image: linear-gradient(to left, var(--tw-gradient-stops))` |
| `bg-gradient-to-t` | `background-image: linear-gradient(to top, var(--tw-gradient-stops))` |
| `bg-gradient-to-b` | `background-image: linear-gradient(to bottom, var(--tw-gradient-stops))` |
| `bg-gradient-to-br` | `background-image: linear-gradient(to bottom right, var(--tw-gradient-stops))` |
| `from-{color}` | `--tw-gradient-from: var(--color-{color}); --tw-gradient-stops: var(--tw-gradient-from), var(--tw-gradient-to)` |
| `from-{color}/{opacity}` | `--tw-gradient-from: color-mix(in oklab, var(--color-{color}) {opacity}%, transparent)` |
| `to-{color}` | `--tw-gradient-to: var(--color-{color})` |
| `to-transparent` | `--tw-gradient-to: transparent` |

## Borders

| Class | CSS |
|-------|-----|
| `border` | `border-width: 1px` |
| `border-0` | `border-width: 0` |
| `border-2` | `border-width: 2px` |
| `border-t` | `border-top-width: 1px` |
| `border-b` | `border-bottom-width: 1px` |
| `border-{color}` | `border-color: var(--color-{color})` |
| `rounded` | `border-radius: 0.25rem` |
| `rounded-md` | `border-radius: 0.375rem` |
| `rounded-lg` | `border-radius: 0.5rem` |
| `rounded-xl` | `border-radius: 0.75rem` |
| `rounded-2xl` | `border-radius: 1rem` |
| `rounded-3xl` | `border-radius: 1.5rem` |
| `rounded-full` | `border-radius: 3.40282e38px` |
| `rounded-[{v}]` | `border-radius: {v}` |

## Effects

| Class | CSS |
|-------|-----|
| `opacity-{n}` | `opacity: calc({n} / 100)` (e.g., `opacity-50` → `0.5`) |
| `shadow` | `box-shadow: var(--tw-shadow)` with `--tw-shadow: 0 1px 3px ...` |
| `shadow-sm` | `box-shadow: var(--tw-shadow)` with `--tw-shadow: 0 1px 2px ...` |
| `shadow-md` | `box-shadow: var(--tw-shadow)` with `--tw-shadow: 0 4px 6px ...` |
| `shadow-lg` | `box-shadow: var(--tw-shadow)` with `--tw-shadow: 0 10px 15px ...` |
| `shadow-none` | `box-shadow: var(--tw-shadow, 0 0 #0000)` |

## Transforms

| Class | CSS |
|-------|-----|
| `translate-x-{n}` | `--tw-translate-x: calc(var(--spacing) * {n}); transform: translate(var(--tw-translate-x), var(--tw-translate-y))` |
| `translate-y-{n}` | `--tw-translate-y: calc(var(--spacing) * {n}); transform: translate(var(--tw-translate-x), var(--tw-translate-y))` |
| `-translate-x-1/2` | `--tw-translate-x: -50%; transform: translate(var(--tw-translate-x), var(--tw-translate-y))` |
| `-translate-y-1/2` | `--tw-translate-y: -50%; transform: translate(var(--tw-translate-x), var(--tw-translate-y))` |
| `scale-{n}` | `scale: calc({n} / 100)` |
| `rotate-{n}` | `rotate: {n}deg` |

## Transitions & Animation

| Class | CSS |
|-------|-----|
| `transition` | `transition-property: color, background-color, border-color, text-decoration-color, fill, stroke, --tw-gradient-from, --tw-gradient-via, --tw-gradient-to, opacity, box-shadow, transform, translate, scale, rotate, filter, -webkit-backdrop-filter, backdrop-filter; transition-timing-function: var(--default-transition-timing-function); transition-duration: var(--default-transition-duration)` |
| `transition-all` | `transition-property: all; transition-timing-function: var(--default-transition-timing-function); transition-duration: var(--default-transition-duration)` |
| `transition-colors` | `transition-property: color, background-color, border-color, text-decoration-color, fill, stroke, --tw-gradient-from, --tw-gradient-via, --tw-gradient-to; transition-timing-function: var(--default-transition-timing-function); transition-duration: var(--default-transition-duration)` |
| `transition-opacity` | `transition-property: opacity; ...` |
| `transition-transform` | `transition-property: transform, translate, scale, rotate; ...` |
| `duration-{n}` | `--tw-duration: {n}ms; transition-duration: {n}ms` |
| `ease-in` | `transition-timing-function: var(--ease-in, cubic-bezier(0.4, 0, 1, 1))` |
| `ease-out` | `transition-timing-function: var(--ease-out)` |
| `ease-in-out` | `transition-timing-function: var(--ease-in-out)` |

## Overflow

| Class | CSS |
|-------|-----|
| `overflow-hidden` | `overflow: hidden` |
| `overflow-auto` | `overflow: auto` |
| `overflow-scroll` | `overflow: scroll` |
| `overflow-visible` | `overflow: visible` |
| `overflow-x-hidden` | `overflow-x: hidden` |
| `overflow-y-auto` | `overflow-y: auto` |

## Visibility

| Class | CSS |
|-------|-----|
| `visible` | `visibility: visible` |
| `invisible` | `visibility: hidden` |
| `collapse` | `visibility: collapse` |

## Object Fit / Position

| Class | CSS |
|-------|-----|
| `object-cover` | `object-fit: cover` |
| `object-contain` | `object-fit: contain` |
| `object-fill` | `object-fit: fill` |
| `object-center` | `object-position: center` |
| `object-top` | `object-position: top` |

## Pointer & Cursor

| Class | CSS |
|-------|-----|
| `cursor-pointer` | `cursor: pointer` |
| `pointer-events-none` | `pointer-events: none` |
| `pointer-events-auto` | `pointer-events: auto` |
| `select-none` | `user-select: none` |

## SVG

| Class | CSS |
|-------|-----|
| `fill-current` | `fill: currentColor` |
| `stroke-current` | `stroke: currentColor` |

## Responsive Breakpoints (Tailwind v4)

| Prefix | Media Query |
|--------|------------|
| `sm:` | `@media (min-width: 40rem)` |
| `md:` | `@media (min-width: 48rem)` |
| `lg:` | `@media (min-width: 64rem)` |
| `xl:` | `@media (min-width: 80rem)` |
| `2xl:` | `@media (min-width: 96rem)` |

## State Variant Selectors

| Variant | Selector Pattern |
|---------|-----------------|
| `hover:` | `.hover\:CLASS:hover` |
| `focus:` | `.focus\:CLASS:focus` |
| `active:` | `.active\:CLASS:active` |
| `disabled:` | `.disabled\:CLASS:disabled` |
| `first:` | `.first\:CLASS:first-child` |
| `last:` | `.last\:CLASS:last-child` |
| `odd:` | `.odd\:CLASS:nth-child(odd)` |
| `even:` | `.even\:CLASS:nth-child(even)` |
| `group-hover:` | `.group:hover .group-hover\:CLASS` |
| `group-focus:` | `.group:focus .group-focus\:CLASS` |
| `peer-checked:` | `.peer:checked ~ .peer-checked\:CLASS` |

## CSS Selector Escaping Rules

Characters that must be escaped with `\` in CSS selectors:
- `.` → `\.` (e.g., `0.5rem` → `0\.5rem`)
- `[` → `\[`, `]` → `\]`
- `/` → `\/`
- `%` → `\%`
- `#` → `\#`
- `:` in class names → `\:` (e.g., `md:flex` → `md\:flex`)
- `(` → `\(`, `)` → `\)`
- `@` → `\@`
