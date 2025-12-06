---
title: HTML Questions - Answers
layout: layouts/page.njk
---

## What does a `doctype` do?

The `<!DOCTYPE>` declaration is used at the very beginning of an HTML document to inform the browser about the version of HTML the page is written in. Its primary purpose is to ensure that the browser renders the page in standards mode, rather than quirks mode, which can cause inconsistencies in styling and layout.

For example, `<!DOCTYPE html>` is used for HTML5 and is the simplest and most common declaration today. Without it, older browsers might render the page using older, less consistent rendering rules.

In my past projects, always including the correct doctype helped prevent unexpected layout shifts and ensured that CSS and JavaScript behaved consistently across different browsers.

## How do you serve a page with content in multiple languages?

To serve a page with content in multiple languages, I usually implement **internationalization (i18n)**. There are a few key strategies I’ve used in production projects:

1. **HTML `lang` Attribute**: Always set the `lang` attribute on the `<html>` tag to indicate the page’s language (e.g., `<html lang="en">`). This helps browsers, search engines, and assistive technologies.

2. **URL-based Language Routing**: For example, in Next.js, I use **i18n routing** like `/en/about` or `/fr/about`. This makes it easy to link to different language versions and allows search engines to index each version separately.

3. **Translation Files**: I maintain separate JSON or JS files containing key-value pairs for each language. I look it as a bundle. For instance:

   ```json
   // en.json
   { "welcome": "Welcome" }

   // fa.json
   { "welcome": "خوش آمدین" }
   ```

4. **Client-side Language Switching**: I use libraries like `next-i18next` or `react-intl` to dynamically load and render translations based on user preference.

5. **Server-side Rendering Considerations**: With SSR (like in Next.js), I ensure the server pre-renders the correct language version to optimize SEO and load performance.

6. **Fallbacks**: Always provide a fallback language in case a translation is missing, usually English.

## What kind of things must you be wary of when designing or developing for multilingual sites?

1. **Text Direction**: Some languages, like Persian or Arabic, are right-to-left (RTL), so UI components and layouts must adapt accordingly.

2. **Layout and Design**: Different languages vary in text length; Some English or many German words tend to be longer, while Persian or Chinese characters are compact. Make sure design is flexible to accommodate expansion or contraction of text.

3. **Encoding**: Always using UTF-8 to support a wide range of characters and avoid broken text or symbols.

4. **SEO Considerations**: Use proper `hreflang` tags and ensure each language version has a unique URL so search engines can index pages correctly.

5. **Date, Time, and Number Formats**: Different regions have different formats. Using localization libraries (like `Intl` in JavaScript) to display them correctly.

6. **Right Font Choice**: Some fonts may not support all characters or diacritics, so choosing fonts with broad multilingual support.

7. **Cultural Sensitivity**: Symbols, colors, and imagery might have different meanings in different cultures.

8. **Performance**: Loading multiple language files can increase payload; lazy-load language packs where appropriate.

9. **Fallbacks**: Having a fallback language in case a translation is missing, typically the primary language of the site.

## What are `data-` attributes good for?

`data-` attributes are custom HTML attributes that allow to store extra information on HTML elements without affecting the DOM’s standard behavior. They are useful for attaching metadata that can be accessed via JavaScript.

For example:

```html
<div class="product" data-id="123" data-category="books">Book Title</div>
```

```javascript
const product = document.querySelector(".product");
console.log(product.dataset.id); // "123"
console.log(product.dataset.category); // "books"
```

## Consider HTML5 as an open web platform. What are the building blocks of HTML5?

Several key building blocks that together enable rich, interactive, and semantic web applications:

1. **Semantics**: Elements like `<header>`, `<footer>`, `<article>`, `<section>`, and `<nav>` provide meaning to the structure of web pages, improving accessibility and SEO.

2. **Forms and Input Types**: HTML5 introduced new input types (`email`, `number`, `date`, `range`, `color`) and attributes (`required`, `placeholder`, `pattern`) to enhance forms and user interaction.

3. **Audio and Video**: `<audio>` and `<video>` elements allow embedding media without relying on third-party plugins like Flash.

4. **Graphics and Multimedia**: `<canvas>` for 2D drawing and `<svg>` for scalable vector graphics enable dynamic visual content.

5. **APIs**: HTML5 provides APIs for advanced functionality, such as:

   - Geolocation API
   - Web Storage (localStorage, sessionStorage)
   - Web Workers
   - WebSockets
   - Drag and Drop API

6. **Offline and Storage**: HTML5 supports offline applications through Service Workers and Application Cache (deprecated), plus client-side storage using localStorage and IndexedDB.

7. **Connectivity and Communication**: WebSockets and server-sent events allow real-time communication between clients and servers.

## Describe the difference between a `cookie`, `sessionStorage` and `localStorage`

The main differences between `cookie`, `sessionStorage`, and `localStorage` relate to scope, lifetime, size, and server accessibility:

1. **Cookie**:

   - Stored on both client and sent with HTTP requests to the server.
   - Small storage limit (~4KB).
   - Can have expiration dates, path, and domain settings.
   - Often used for authentication, session tracking, or server-side data access.

2. **sessionStorage**:

   - Stored only on the client.
   - Scoped to a single tab/window; cleared when the tab is closed.
   - Larger storage limit (~5-10MB depending on browser).
   - Not sent with HTTP requests.

3. **localStorage**:

   - Stored only on the client.
   - Persistent until explicitly cleared by JavaScript or user.
   - Larger storage limit (~5-10MB depending on browser).
   - Not sent with HTTP requests.

## Describe the difference between `<script>`, `<script async>` and `<script defer>`

The difference between `<script>`, `<script async>`, and `<script defer>` relates to how the browser loads and executes JavaScript:

1. **`<script>` (default)**:

   - Blocking: HTML parsing stops until the script is downloaded and executed.
   - Use for scripts that must run immediately.

2. **`<script async>`**:

   - Script loads asynchronously while HTML parsing continues.
   - Executes as soon as it’s downloaded, which may be before or after DOM parsing.
   - Ideal for independent scripts (e.g., analytics, ads) that don’t rely on the DOM.

3. **`<script defer>`**:

   - Script loads in parallel with HTML parsing.
   - Executes **after** the DOM is fully parsed.
   - Maintains execution order with multiple `defer` scripts.
   - Best for scripts that manipulate the DOM safely after it’s ready.

In practice, I usually use `defer` for app scripts to avoid blocking rendering and `async` for third-party scripts that are independent of DOM structure.

## Why is it generally a good idea to position CSS `<link>`s between `<head></head>` and JS `<script>`s just before `</body>`? Do you know any exceptions?

1. **CSS in `<head>`**:

   - Ensures that styles are loaded before the page is rendered.
   - Prevents Flash of Unstyled Content (FOUC) because the browser knows how to render elements immediately.

2. **JS before `</body>`**:

   - Avoids blocking HTML parsing and rendering.
   - Scripts often manipulate DOM elements, so placing them at the end ensures the DOM is fully available.

**Exceptions**:

- Inline scripts or critical JS that must execute immediately (like feature detection or polyfills) may need to be in `<head>`.
- For non-blocking JS that still needs to be in `<head>`, using `defer` or `async` attributes can prevent render-blocking.

In practice, I always prioritize CSS in `<head>` for immediate styling and defer JS for faster perceived page load, adjusting placement for critical scripts when necessary.

## What is progressive rendering?

Progressive rendering is a technique where a web page’s content is displayed incrementally to the user as soon as possible, rather than waiting for the entire page to fully load. The main goal is to improve perceived performance and user experience by showing meaningful content early.

- **Above-the-fold content first**: Prioritize rendering visible content before resources below the fold.
- **Lazy loading**: Images, videos, and other non-critical assets are loaded only when needed.
- **Skeleton screens or placeholders**: Show lightweight placeholders while content is being fetched.
- **Streaming**: For large pages or dynamic content, HTML can be streamed in chunks to render progressively.

In my projects, I’ve used progressive rendering with React by combining server-side rendering (SSR) and code splitting, so users see above-the-fold content almost immediately, and additional components load progressively without blocking the main content.

## Why you would use a `srcset` attribute in an image tag? Explain the process the browser uses when evaluating the content of this attribute

The `srcset` attribute is used in an `<img>` tag to provide the browser with multiple image sources, allowing it to choose the most appropriate one based on device resolution, viewport size, or pixel density. This improves performance and ensures optimal image quality.

**How it works:**

1. You provide a comma-separated list of image URLs with descriptors (`w` for width or `x` for pixel density), e.g.:

```html
<img src="small.jpg" srcset="medium.jpg 768w, large.jpg 1200w" alt="Example" />
```

2. The browser evaluates:

   - The **viewport size**
   - The **device pixel ratio**
   - The **`sizes` attribute** if provided

3. It then selects the most appropriate image to download, balancing quality and performance.

> I don't have recent experience using `srcset`.

## Have you used different HTML templating languages before?

1. **JSX (React)**: I use JSX extensively for React projects. It allows me to write HTML-like syntax inside JavaScript, which is then compiled to efficient JavaScript DOM calls.

2. **Handlebars (Template Engine)**: I’ve worked with Inja and Jinja for client-side and server-side templating. Its syntax (`{{variable}}`) is clean and supports helpers and partials.

## What is the difference between `canvas` and `svg`?

The main difference between `<canvas>` and `<svg>` comes down to rendering approach and use case:

1. **Canvas**:

   - **Bitmap-based**: Draws pixels directly onto a canvas element using JavaScript.
   - **Procedural**: You manually draw shapes, images, or text through JS commands.
   - **Good for**: Dynamic, highly interactive graphics, games, animations, and real-time data visualizations.

2. **SVG**:

   - **Vector-based**: Uses XML markup to define shapes and graphics.
   - **Declarative**: Elements are part of the DOM and can be styled with CSS or manipulated via JS.
   - **Good for**: Scalable graphics, diagrams, charts, and icons.

## What are empty elements in HTML?

Elements that do not have any content or closing tag. They are also called self-closing or void elements. These elements are used to insert something into the document without needing an end tag.

- `<img>` for images
- `<input>` for form fields
- `<br>` for line breaks
- `<hr>` for horizontal rules
- `<meta>` for metadata
- `<link>` for stylesheets

In my experience, knowing empty elements is important for correct HTML syntax and avoiding errors in JSX or other templating languages, where self-closing tags must be properly closed like `<input />`.
