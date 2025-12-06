---
title: CSS Questions - Answers
layout: layouts/page.njk
---

## What is CSS selector specificity and how does it work?

CSS selector specificity determines which CSS rule applies when multiple rules target the same element. It works as a scoring system based on the types of selectors used:

1. **Inline styles** (`style=""`) have the highest specificity.
2. **ID selectors** (`#id`) are next.
3. **Class selectors, attributes, and pseudo-classes** (`.class`, `[type="text"]`, `:hover`) have medium specificity.
4. **Element and pseudo-element selectors** (`div`, `p`, `::before`) have the lowest specificity.

**How it works:**

- The browser calculates specificity for each conflicting rule.
- The rule with the highest specificity wins and is applied to the element.
- If specificity is equal, the last defined rule in the CSS wins.

For example:

```css
p {
  color: blue;
} /* specificity: 0,0,0,1 */
.text {
  color: red;
} /* specificity: 0,0,1,0 */
#intro {
  color: green;
} /* specificity: 0,1,0,0 */
```

Here, an element with `id="intro"` and `class="text"` will have green text because the ID selector has higher specificity.

## What is the difference between "resetting" and "normalizing" CSS? Which would you choose, and why?

The difference between "resetting" and "normalizing" CSS lies in their approach to handling browser default styles:

1. **Reset CSS**:

   - Removes all default browser styles by setting most elements to a baseline, typically zero or none.
   - Example: `margin: 0; padding: 0;` on all elements.
   - Pros: Provides a completely clean slate, reducing cross-browser inconsistencies.
   - Cons: Can remove useful defaults, requiring more work to restyle common elements.

2. **Normalize CSS**:

   - Makes browser default styles consistent but preserves useful defaults (like headings, form elements, and lists).
   - Example: `normalize.css` adjusts styles across browsers without removing them entirely.
   - Pros: Less work to style elements, more semantic defaults preserved.
   - Cons: Might leave minor inconsistencies that require additional tweaks.

**Which I’d choose:**

- I usually prefer **normalize.css** in modern projects because it preserves semantic styles and provides consistency across browsers with minimal extra work. I only use full reset when I need absolute control over every style and want to start completely from scratch.

## Describe Floats and how they work

Floats in CSS are a layout technique that allows an element to be pushed to the left or right of its container, letting inline content wrap around it. They were originally intended for wrapping text around images but have been widely used for multi-column layouts before Flexbox and Grid became standard.

**How they work:**

- An element with `float: left` or `float: right` is taken out of the normal document flow horizontally but still affects vertical flow.
- Sibling content (inline or block) will wrap around the floated element.
- The container does not automatically expand to accommodate floated elements, which can lead to collapsing parent height. To fix this, a clearfix or `overflow: auto` can be used on the container.

**Example:**

```css
img {
  float: left;
  margin-right: 10px;
}
p {
  text-align: justify;
}
```

Here, text in the paragraph wraps around the floated image. I now rarely use floats for layout, favoring Flexbox or CSS Grid.

## Describe z-index and how stacking context is formed

The `z-index` property in CSS controls the stacking order of elements along the z-axis (which determines which elements appear on top of others).

**How `z-index` works:**

- Higher `z-index` values appear in front of lower ones.
- Only elements with a `position` value of `relative`, `absolute`, `fixed`, or `sticky` can have a `z-index` applied.

**Stacking context:**

- A stacking context is a hierarchical group of elements that defines how child elements are stacked.
- A new stacking context is formed when an element has:

  - A `position` value other than `static` and a `z-index` other than `auto`
  - `opacity` less than 1
  - CSS transforms, filters, flex items with `z-index`, certain `mix-blend-mode` values, etc.

- Within a stacking context, child elements are stacked according to their `z-index` values, but they cannot escape their parent’s context.

**Example:**

```css
.parent {
  position: relative;
  z-index: 10;
}
.child {
  position: absolute;
  z-index: 5; /* This z-index is relative to .parent stacking context */
}
```

In practice, understanding stacking contexts helps avoid unexpected layering issues when building complex UIs with overlays, modals, or dropdowns.

## Describe BFC (Block Formatting Context) and how it works

A BFC (Block Formatting Context) is an isolated layout environment in CSS that determines how elements are positioned and interact with each other. It’s a key concept for controlling float behavior, collapsing margins, and element containment.

**How BFC works:**

- Elements inside a BFC do not overlap with floats from outside the BFC.
- Margins between elements inside a BFC do not collapse with margins outside it.
- Containing floats: A BFC container will expand to include floated child elements.

**How to create a BFC:**

- Using `overflow: hidden | auto | scroll`
- `display: flow-root`
- `float: left | right`
- `position: absolute | fixed`
- `display: inline-block | table` (with some limitations)

**Example:**

```css
.container {
  overflow: hidden; /* creates a BFC */
}
.floated-child {
  float: left;
}
```

In this example, `.container` will wrap around the floated child, preventing height collapse. I occasionally forget the name "BFC", but I do use the concept.

## What are the various clearing techniques and which is appropriate for what context?

In CSS, clearing techniques are used to handle floated elements and prevent layout issues.

- Use **`clear`** for single elements that must drop below floats.
- Use **empty block elements** as a quick-and-dirty fix.
- Use **overflow method** for simple container wrapping, but watch for hidden content.
- Use **clearfix** for a reliable, modern float containment solution.
- Prefer **Flexbox/Grid** for new layouts instead of floats when possible.

### 1. Clear property

- Apply `clear: left | right | both` to the element following floated elements.
- Appropriate when you have a single element that needs to appear below floats.

```css
.clearfix {
  clear: both;
}
```

### 2. Empty element / spacer div

- Insert an empty element with `clear: both` after floats.
- Less recommended now; can clutter HTML.

### 3. Overflow property on the container

- Set `overflow: hidden | auto` on the parent to contain floats.
- Simple and effective for most cases; be cautious as it may clip overflowing content.

### 4. clearfix hack (modern)

- Use a pseudo-element to clear floats without extra markup:

```css
.clearfix::after {
  content: "";
  display: table;
  clear: both;
}
```

- Widely used in modern layouts; avoids adding empty elements.

**Which to use:**

- For individual clearing needs: `clear` is sufficient.
- For containing floated children in a parent container: `clearfix` or `overflow` is preferred.
- Avoid empty elements unless working on legacy projects.

## How would you approach fixing browser-specific styling issues?

- **Identify the problem**: Use dev tools to inspect applied and overridden styles, testing across browsers and devices.
- **Validate code**: Ensure HTML/CSS follows current web standards to reduce inconsistencies.
- **Vendor prefixes**: Apply `-webkit-`, `-moz-`, etc., for partially supported CSS properties.
- **Feature detection**: Use `@supports` in CSS or Modernizr rather than targeting specific browsers.
- **Fallbacks**: Provide alternative styles for unsupported features.
- **Test and iterate**: Check fixes across all target browsers.
- **Document fixes**: Keep notes on browser-specific adjustments for maintainability.

## How do you serve your pages for feature-constrained browsers? What techniques/processes do you use?

I Don't :D

Actually for feature-constrained browsers (browsers that lack support for certain modern features), I use progressive enhancement: start with a basic, functional version, add advanced features for modern browsers, use polyfills for missing features\*, rely on feature detection (@supports or JS), and ensure layouts remain responsive and accessible.

**\*Example:**

- If a browser doesn’t support `Promise`, i can include a Promise polyfill so my code use Promises still works.

```html
<script src="https://cdn.jsdelivr.net/npm/promise-polyfill/dist/polyfill.min.js"></script>
```

## What are the different ways to visually hide content (and make it available only for screen readers)?

1. **`sr-only` technique (most common)**

```css
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}
```

2. **Using `clip-path` or `clip`**

```css
.clip-hidden {
  position: absolute;
  clip: rect(0 0 0 0);
  width: 1px;
  height: 1px;
  overflow: hidden;
}
```

Older method but still effective.

3. **Using `visibility: hidden` or `display: none` (NOT accessible)**

4. **Using `aria-hidden="true"` (hides from screen readers)**

- Not for our purpose, but relevant.

**Preferred approach:**
I typically use the `sr-only` class (Bootstrap uses this pattern) because it's reliable, widely supported, and works across browsers.

## Have you ever used a grid system, and if so, what do you prefer?

Yes, I’ve used several grid systems.

- **Flexbox-based grids:** Useful for simpler, one-dimensional layouts.
- **Framework grids (Bootstrap / Tailwind’s utility grid):** Helpful when speed, consistency, or team standards are more important than full custom control. Tailwind’s grid utilities are my favorite for rapid prototyping.

## Have you used or implemented media queries or mobile specific layouts/CSS?

Yes, obviously. I typically follow a mobile-first approach, starting with the smallest viewport styles and scaling up using `min-width` breakpoints. This ensures better performance and cleaner overrides. For mobile-specific layouts, I adjust typography, spacing, grid structure, and sometimes even component behavior. I also use modern units like `clamp()`, `vw`, and fluid spacing to keep things responsive without too many breakpoints.

## Are you familiar with styling SVG?

Yep, I’ve worked quite a bit with styling SVGs in different contexts. When I'm using inline SVGs, I treat them almost like regular DOM elements: I can style individual paths or groups using CSS, apply `currentColor` to inherit text color, or even manipulate them dynamically with JavaScript for things like animations or theme toggling. For example, on a previous project we used inline SVG icons so that we could easily adjust stroke and fill colors based on the user's theme (light/dark) without maintaining separate asset versions.

When using external SVGs (like in an `<img>` tag), I’m aware that direct CSS styling isn’t possible, so in those cases I either inline the SVG, use a React component wrapper (e.g., SVGR), or apply filters when appropriate. I’ve also handled more advanced use cases like animating stroke paths with CSS keyframes or using SVGs for complex UI elements, such as a custom progress indicator, where I adjusted dash offsets and stroke lengths programmatically to reflect state.

## Can you give an example of an `@media` property other than `screen`?

`print`, used to define styles specifically for printing. I haven’t used it until now.

## What are some of the "gotchas" for writing efficient CSS?

- **Overly complex or deeply nested selectors**
- **Overusing universal or attribute selectors**
- **Excessive `!important` usage**
- **Animating layout-heavy properties**

## What are the advantages/disadvantages of using CSS preprocessors? Describe what you like and dislike about the CSS preprocessors you have used

- **Advantages:** Variables, nesting, mixins, modular partials.
- **Disadvantages:** extra build step, over-nesting, harder debugging.
- **Likes:** SCSS features and modularity.
- **Dislikes:** deep nesting, debugging without sourcemaps.

## How would you implement a web design comp that uses non-standard fonts?

I’d use `@font-face` with WOFF/WOFF2, provide fallback fonts, optimize weights/styles, use `font-display: swap`, and ensure accessibility and licensing.

## Explain how a browser determines what elements match a CSS selector

When a browser processes a webpage, it constructs a DOM tree from the HTML and a CSSOM tree from the CSS. To determine which elements match a CSS selector, it essentially walks the DOM tree and evaluates each element against the rules in the CSSOM.

1. **Selector Parsing**: The browser parses CSS selectors into an internal representation that’s easy to evaluate.

2. **Matching Elements**: For each rule, the browser checks each element in the DOM to see if it satisfies the selector.

   - Simple selectors like `div` or `.class` are fast because they just check the tag name or class list.
   - Complex selectors like `div > .item:first-child` require checking parent/child relationships and pseudo-classes.

3. **Specificity Calculation**: If multiple selectors match the same element, the browser calculates specificity to determine which style applies.

4. **Cascade and Inheritance**: The browser applies rules according to the cascade, accounting for `!important` flags and inherited properties.

## Describe pseudo-elements and discuss what they are used for

Pseudo-elements are CSS keywords that let we style specific parts of an element without extra HTML. They use the `::` syntax, like `::before`, `::after`, `::first-line`, and `::first-letter`.

**Common uses:**

- `::before` / `::after`: Add content or decorative elements before or after an element’s content, such as icons or visual indicators.
- `::first-line`: Style the first line of text in a block, often for typographic effects.
- `::first-letter`: Style the first letter of a block, useful for drop caps or decorative headings.

## Explain your understanding of the box model and how you would tell the browser in CSS to render your layout in different box models.

The CSS box model defines how the size of an element is calculated and how spacing around it works. Each element is considered as a rectangular box with four layers:

1. **Content**: The actual content, like text or an image.
2. **Padding**: Space between the content and the border.
3. **Border**: Surrounds the padding and content.
4. **Margin**: Space outside the border, separating elements from each other.

By default, browsers use the `content-box` model, where the width and height only include the content, and padding and border are added outside. In contrast, the `border-box` model includes padding and border in the specified width and height, which often simplifies layout calculations.

To specify the box model in CSS, we use the `box-sizing` property:

```css
/* Default behavior */
div {
  box-sizing: content-box;
}

/* Including padding and border in width/height */
div {
  box-sizing: border-box;
}
```

In practice, I usually apply `box-sizing: border-box` globally using:

```css
*,
*::before,
*::after {
  box-sizing: border-box;
}
```

This prevents unexpected layout shifts when adding padding or borders, especially in responsive designs.

## What does `* { box-sizing: border-box; }` do? What are its advantages?

`* { box-sizing: border-box; }` sets the `box-sizing` property to `border-box` for all elements. This means an element's width and height include its content, padding, and border.

**Advantages:**

- Simplifies layout calculations, especially when adding padding or borders.
- Prevents unexpected element growth that can break layouts.
- Makes responsive designs easier to manage because total dimensions are predictable.

## What is the CSS `display` property and can you give a few examples of its use?

The CSS `display` property defines how an element is rendered and how it interacts with other elements in the layout.

**Common values and examples:**

- `block`: Element takes full width, starts on a new line.

  ```css
  div {
    display: block;
  }
  ```

- `inline`: Element flows inline with text, width/height ignored.

  ```css
  span {
    display: inline;
  }
  ```

- `inline-block`: Flows inline but respects width/height.

  ```css
  button {
    display: inline-block;
  }
  ```

- `flex`: Turns container into a flex container for flexible layouts.

  ```css
  .nav {
    display: flex;
    justify-content: space-between;
  }
  ```

- `grid`: Enables CSS Grid layout.

  ```css
  .container {
    display: grid;
    grid-template-columns: 1fr 2fr;
  }
  ```

## What is the difference between inline and inline-block?

The main difference between `inline` and `inline-block` lies in how they handle width, height, and layout:

- **inline**:

  - Flows within text.
  - Width and height properties are ignored.
  - Cannot have top and bottom margin/padding affect layout the same way block elements do.

- **inline-block**:

  - Flows inline but behaves like a block in terms of box model.
  - Width and height are respected.
  - Can have vertical margins and padding applied properly.

**Example:**

```css
span.inline {
  display: inline;
  width: 100px;
  height: 50px;
} /* width/height ignored */
span.inline-block {
  display: inline-block;
  width: 100px;
  height: 50px;
} /* respected */
```

I often use `inline-block` for buttons or menu items that need to sit inline but maintain fixed dimensions.

## What is the difference between the "nth-of-type()" and "nth-child()" selectors?

`:nth-child()` and `:nth-of-type()` are both CSS pseudo-classes for selecting elements based on their position, but they differ in what they count:

- **`:nth-child(n)`**

  - Selects the nth child of its parent regardless of type.
  - Example: `li:nth-child(2)` selects the second child of its parent, even if it’s not an `li`.

- **`:nth-of-type(n)`**

  - Selects the nth child of its parent of a specific type.
  - Example: `li:nth-of-type(2)` selects the second `li` element among its siblings, ignoring other types.

In a project, I used `:nth-of-type()` to style every other paragraph in a blog post while ignoring headings or other elements, which ensured consistent styling only for the targeted type.

## What is the difference between a relative, fixed, absolute and statically positioned element?

CSS positioning determines how an element is placed in the document flow.

- **static** (default):

  - Normal document flow.
  - `top`, `left`, `right`, `bottom` have no effect.

- **relative**:

  - Moves element relative to its normal position.
  - Space for the element is still reserved in the flow.

- **absolute**:

  - Positioned relative to the nearest positioned ancestor (not `static`).
  - Removed from normal flow; does not affect other elements.

- **fixed**:

  - Positioned relative to the viewport.
  - Stays in place even when scrolling.
  - Removed from normal flow.

In a dashboard project, I used `relative` for container offsets, `absolute` for tooltips inside components, and `fixed` for a sticky header that stays visible during scroll.

## What existing CSS frameworks have you used locally, or in production? How would you change/improve them?

I have experience using several CSS frameworks both locally and in production, for example:

- **Bootstrap**: Used for rapid prototyping and responsive grids. I would improve it by removing unused components and using custom theming to reduce CSS bundle size.
- **Tailwind CSS**: Used in multiple production projects for utility-first styling. I like its flexibility but would improve workflow by standardizing design tokens and creating a consistent component library to avoid repetitive class names.
- **Material UI**: Used for React applications with prebuilt components. I would improve performance by tree-shaking unused components and optimizing theme overrides.

## Have you used CSS Grid?

Yes, I have used CSS Grid for layout design. It allows precise control over rows, columns, and placement of elements, making complex responsive layouts easier to implement.

For example, in a dashboard project, I used Grid to create a multi-column layout where widgets could span multiple rows and columns. This made it easy to rearrange elements for different screen sizes without additional wrappers or media-query hacks. I often combine Grid with `auto-fit` and `minmax()` to make layouts fully responsive and maintain consistent spacing.

## Can you explain the difference between coding a web site to be responsive versus using a mobile-first strategy?

Responsive design and mobile-first strategy are related but different approaches:

- **Responsive design**:

  - The website adapts to different screen sizes using CSS techniques like media queries, flexible grids, and fluid images.
  - Can be implemented starting from desktop or mobile.

- **Mobile-first strategy**:

  - CSS and layout are designed starting with the smallest screen size (mobile) and progressively enhanced for larger screens.
  - Typically uses min-width media queries rather than max-width.

The main difference is the starting point: mobile-first focuses on designing for mobile first, ensuring core functionality and performance for constrained devices, then enhancing for larger screens. In a recent project, I used mobile-first Grid and Flex layouts to optimize performance on mobile before scaling up for desktop, which improved loading times and usability.

## Have you ever worked with retina graphics? If so, when and what techniques did you use?

No; specifically, not.

## Is there any reason you'd want to use `translate()` instead of _absolute positioning_, or vice-versa? And why?

Yes, there are reasons to choose `translate()` over absolute positioning or vice-versa:

- **`translate()`** (CSS transform):

  - Moves an element visually without affecting the document flow.
  - Excellent for animations and transitions because it leverages GPU acceleration, making movements smoother.
  - The element’s original space in the layout is preserved, preventing layout shifts.

- **Absolute positioning**:

  - Removes the element from normal flow, positioning it relative to the nearest positioned ancestor.
  - Useful when you need a fixed layout element that doesn’t move with animations.

## How is clearfix css property useful?

The clearfix technique is used in CSS to clear floats and prevent parent elements from collapsing when they contain floated child elements.

Without clearfix, a container with only floated children would collapse because floated elements are removed from the normal document flow.

**Common clearfix method:**

```css
.clearfix::after {
  content: "";
  display: table;
  clear: both;
}
```

**Use case:** I used clearfix in a project where a card container had floated images inside. Without clearfix, the container’s height collapsed, breaking the layout. Applying clearfix ensured the container correctly wrapped its children, maintaining proper spacing and layout integrity.

## Can you explain the difference between px, em and rem as they relate to font sizing?

The difference between `px`, `em`, and `rem` lies in how they calculate size relative to other elements:

- **px** (pixels):

  - Absolute unit.
  - Font size is fixed and does not scale with parent elements.
  - Example: `font-size: 16px;`

- **em**:

  - Relative to the font size of the parent element.
  - Example: If parent is 16px, `1.5em` = 24px.
  - Can compound through nested elements.

- **rem** (root em):

  - Relative to the root element (`html`) font size.
  - Example: If `html { font-size: 16px; }`, `2rem` = 32px, regardless of parent.

In practice, I often use `rem` for consistent global sizing and spacing, and `em` for components that should scale relative to their parent, while `px` is reserved for very precise control when needed and implementation based on Figma designs.

## Can you give an example of a pseudo class? Can you provide an example use case for a pseudo class?

A pseudo-class in CSS defines a special state of an element. For example, `:hover` applies styles when the user hovers over an element.

**Example:**

```css
button:hover {
  background-color: #0055ff;
  color: white;
}
```

## What is the difference between a block level element and an inline element? Can you provide examples of each type of element?

Block-level elements and inline elements differ in how they occupy space and interact with the document flow:

- **Block-level elements**:

  - Start on a new line and take up the full width of their parent by default.
  - Can contain other block and inline elements.
  - Examples: `<div>`, `<p>`, `<h1>`–`<h6>`, `<section>`.

- **Inline elements**:

  - Flow within text and only take up as much width as needed.
  - Cannot contain block elements.
  - Examples: `<span>`, `<a>`, `<strong>`, `<em>`.

Block-level elements start on a new line and take up the full width (e.g., `<div>`, `<p>`, `<h1>`), while inline elements flow within text and only take the width they need (e.g., `<span>`, `<a>`, `<strong>`). I use block elements for layout structures like sections or cards, and inline elements to style text without breaking flow.

## What is the difference between CSS Grid and Flexbox? When would you use one over the other?

- **Flexbox**:

  - One-dimensional layout (row or column).
  - Best for distributing space among items in a single line or column.
  - Handles alignment and spacing efficiently along a single axis.
  - Example: Navigation bars, button groups, or card lists.

- **CSS Grid**:

  - Two-dimensional layout (rows and columns).
  - Best for complex layouts that require control over both axes.
  - Allows precise placement of items in a grid.
  - Example: Dashboards, galleries, or magazine-style layouts.

In projects, I typically use Flexbox for simple component-level layouts like menus or forms and Grid for entire page or section layouts where I need both row and column control.

## What is the difference between fixed, fluid and responsive layouts?

The differences between fixed, fluid, and responsive layouts are in how they handle width and adapt to screen sizes:

- **Fixed layout**:

  - Uses a fixed width in pixels.
  - Does not adjust when the viewport changes.
  - Example: `width: 960px;`

- **Fluid layout (liquid layout)**:

  - Uses percentages instead of fixed units.
  - Expands or shrinks with the browser window.
  - Example: `width: 80%;`

- **Responsive layout**:

  - Combines flexible (fluid) widths with CSS media queries.
  - Adapts to different screen sizes, switching layouts as needed.
  - Example: `@media (max-width: 768px) { ... }`

In projects, I usually implement responsive layouts with fluid widths and media queries to ensure usability and consistent design across devices, while avoiding the rigidity of fixed layouts.
