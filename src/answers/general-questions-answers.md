---
title: General Questions - Answers
layout: layouts/page.njk
---

## When building a new web site or maintaining one, can you explain some techniques you have used to increase performance?

Here are some techniques I’ve used both for new builds and maintenance:

1. **Code Splitting & Lazy Loading**: Using React and Next.js, I split code at the route or component level. For example, on a SaaS dashboard, I lazy-loaded heavy charting components so the initial load was under 1 second, improving perceived performance.

2. **Image Optimization**: I used Next.js `<Image>` with automatic resizing and WebP support, combined with lazy loading. This drastically reduced page weight, especially on media-heavy pages.

3. **Minification & Bundling**: I ensure JS/CSS files are minified and bundled efficiently. On a client project, replacing multiple CSS imports with a single optimized bundle reduced render-blocking requests by 40-50%.

4. **Caching & CDN**: I leverage browser caching for static assets and deploy assets through a CDN to reduce latency. For example, a public-facing marketing site saw Time to First Byte (TTFB) drop.

5. **Server-Side Rendering & Static Generation**: Using Next.js SSR or SSG where appropriate ensures content is pre-rendered, reducing client-side processing. For example, static blog posts were pre-rendered, giving near-instant load times.

6. **Avoiding Unnecessary Re-renders**: In React, I use `React.memo`, `useMemo`, and `useCallback` to prevent expensive component re-renders. On a dynamic dashboard, this reduced CPU usage and improved responsiveness under heavy data loads.

7. **Monitoring & Auditing**: I regularly use Lighthouse, WebPageTest, and Chrome DevTools to identify bottlenecks. For one client, removing unused CSS and third-party scripts improved the Lighthouse performance score.

8. **Efficient API Handling**: I implement caching, debouncing, and request batching for API calls to minimize network overhead.

## Can you describe some SEO best practices or techniques you have used lately?

A few practices I regularly implement include:

1. **Semantic HTML**: Using proper HTML5 elements like `<header>`, `<main>`, `<article>`, `<section>`, and `<footer>` to improve crawlability and accessibility. For example, on a Next.js blog project, I structured the article pages semantically, which helped search engines better understand content hierarchy.

2. **Meta Tags & Open Graph**: I ensure every page has unique `<title>` and `<meta description>` tags, and I implement Open Graph and Twitter Card metadata for better social previews. On an e-commerce project, this improved click-through rates from social media shares.

3. **Server-Side Rendering (SSR) and Static Site Generation (SSG)**: Using Next.js, I leveraged SSR for dynamic pages and SSG for blog posts to ensure pages are pre-rendered for better SEO performance.

4. **Optimized URLs & Routing**: I create human-readable URLs that include keywords without unnecessary parameters. For example, `/products/blue-running-shoes` instead of `/product?id=123`.

5. **Lazy Loading & Image Optimization**: Using Next.js `<Image>` component and lazy loading large assets reduces page load time, which is a ranking factor. I also add descriptive `alt` attributes for accessibility and SEO.

6. **Structured Data / Schema Markup**: I added JSON-LD structured data on product pages and articles to enhance rich snippets in search results.

7. **Performance & Lighthouse Scores**: Optimizing core web vitals is crucial. I monitor LCP, FID, and CLS and reduce render-blocking scripts, which directly improves SEO.

## Can you explain any common techniques or recent issues solved in regards to front-end security?

I go with common techniques.

1. I treat all user‑generated content as untrusted. By default React’s JSX escapes strings interpolated via `{…}`, which helps.

2. I also avoid API usage like `eval()`, `new Function()`, or dynamic script injections with user‑supplied strings. These are red‑flags because they can open up code injection or XSS vectors.

3. Rather than storing sensitive tokens (JWTs, session tokens) in `localStorage` or `sessionStorage`, I prefer to rely on `HttpOnly`, `Secure`, `SameSite` cookies. That way, even if the page suffers an XSS vulnerability, malicious JavaScript can’t read those cookies.

4. Avoid exposing tokens in URLs (query strings, fragments) to prevent accidental leaks through referer logs or shared links.

5. For any state-changing requests (POST/PUT/DELETE), I ensure the backend issues anti‑CSRF tokens (or uses other standard CSRF protection mechanisms), and require that token to be submitted along with the request, especially important if using cookies for session authentication.

6. I avoid (or try at least) relying solely on client-side logic for access control. I treat frontend code as UI-only: actual authorization and validation always happens on the server-side.

7. I treat third‑party packages with caution: before adding a new dependency, I check for security advisories (e.g. using `npm audit`, `npx snyk test`, or other static analysis/SAST tools). Outdated or unmaintained libraries are a common source of vulnerabilities.

8. As part of CI/CD, I include automated dependency scanning so vulnerabilities are caught early, especially relevant for large React/Next.js codebases with many packages.

## What actions have you personally taken on recent projects to increase maintainability of your code?

### 1. Modular & Component-Based Architecture

- I structure React apps into small, reusable components instead of large monolithic ones.
- Example: On a dashboard project, I split each widget (charts, tables, filters) into isolated components with clearly defined props and minimal internal state. This made it easy for new team members to reuse or update components without side effects.

### 2. Type Safety with TypeScript

- Using TypeScript ensures type correctness across components and API calls.
- Example: I converted a JS-heavy client to TS, which helped catch runtime bugs during compile time, especially with complex data returned from APIs.

### 3. Clear Folder & File Structure

- I maintain a predictable folder structure: `components/`, `hooks/`, `services/`, `pages/` (for Next.js), `utils/`.

- This reduces cognitive load when navigating the codebase and makes onboarding faster.

### 4. Reusable Utilities & Custom Hooks

- Extracting repeated logic into utility functions or custom hooks keeps components lean.
- Example: I created a `useFetch` hook with caching and error handling, used across multiple pages to fetch API data consistently.

### 5. Consistent Coding Standards & Linters

- I enforce ESLint + Prettier with a shared configuration across the team.
- This avoids style discrepancies, enforces best practices, and reduces the likelihood of bugs creeping in due to inconsistent patterns.

### 6. Documentation & Comments

- Key business logic, tricky algorithms, or reusable hooks include concise inline documentation.
- Example: On a dynamic form builder project, I documented the state management pattern to help future developers understand how nested fields and validations were handled.

### 7. Test Coverage

- Writing unit and integration tests with Jest and React Testing Library ensures changes don’t break existing functionality. (This is very important to me; I consider legacy code to be code that does not have unit tests.)
- Example: On a client portal, adding tests for form validation and API integration made maintenance far safer when upgrading dependencies.

### 8. Avoiding Anti-Patterns

- I avoid deeply nested state, excessive prop drilling, and large context objects. Instead I use Context or Redux wisely.
- Example: In a multi-user dashboard, I used context selectively for global state and kept local state inside components for simplicity.

## Talk about your preferred development environment

### 1. IDE & Editor

- I use **VS Code** (I can work with NeoVim also) as my primary editor due to its lightweight nature and extensive ecosystem.
- Key extensions I rely on include:
  - ESLint & Prettier for code consistency.
  - VSCode React/TypeScript snippets for rapid component creation.
  - GitLens for enhanced Git visibility.
  - Live Server for static HTML previews.
  - Tailwind CSS IntelliSense if I’m using utility-first CSS.

### 2. Version Control & Collaboration

- **Git** is essential; I follow a feature-branch workflow with pull requests.
- I use GitHub or GitLab, depending on the project, with CI/CD pipelines for automated testing and deployment.

### 3. Package Management

- **npm** or **pnpm** (in linux) for package management, ensuring consistent dependency versions via lock files.
- I regularly audit dependencies for security vulnerabilities.

### 4. Development Servers & Frameworks

- **Next.js** for React projects requiring SSR, SSG, or API routes.
- Local dev server with hot module replacement for fast feedback.

### 5. Testing

- **Jest** + **React Testing Library** for unit and integration tests.
- Sometimes I use Cypress (I don’t know it well, so…) for end-to-end tests on larger projects.

### 6. Debugging & Profiling

- Chrome DevTools for network, performance, and memory profiling.
- React Developer Tools, Vercel, Drizzle and Redux extensions for component tree inspection, state debugging, etc.

### 7. Workflow & Automation

- I set up **lint-staged** and **husky hooks** to enforce linting and formatting before commits.
- Sometimes scripts for building, testing, and running development servers streamline repetitive tasks.

### 8. Terminal & Shell

- I prefer using VS Code integrated terminal with **zsh** and **Oh My Zsh** for efficient navigation and shortcuts.

### 9. Environment Management

- I maintain `.env` files for environment-specific variables, ensuring sensitive credentials are never committed.
- For complex multi-service projects, I sometimes use Docker to replicate production-like environments locally.

## Can you describe your workflow when you create a web page?

### 1. Understanding Requirements

- I start by gathering and clarifying requirements: the purpose of the page, user interactions, target devices, and performance goals.

### 2. Wireframing & Planning

- I sketch a rough layout or use tools like Figma/Sketch to understand structure and hierarchy.
- At this stage, I also plan reusable components, considering what will be dynamic versus static.

### 3. Setting Up the Project

- I create a new branch in Git and set up the page in the existing codebase or framework (React/Next.js).
- I configure folder structure, environment variables, and import necessary global styles or themes.

### 4. Building Structure (HTML/JSX)

- I focus first on semantic markup, accessibility (ARIA attributes), and responsive layout with CSS/utility-first frameworks (like TailwindCSS).
- I ensure HTML is clean and structured for SEO and maintainability.

### 5. Adding Styles & Interactivity

- I implement responsive styling with CSS Modules, Styled Components, or TailwindCSS.
- Interactive elements (modals, sliders, form validation) are added using React state/hooks.
- I focus on minimal DOM updates and avoid unnecessary re-renders for performance.

### 6. Data Integration

- If the page consumes data, I integrate API calls with `fetch` or something like Axios, often via custom hooks.
- I ensure proper error handling, loading states, and caching where needed.

### 7. Optimization

- I optimize images (modern formats or using framework features), fonts (CDN), and assets.
- Code splitting, lazy loading, and SSR/SSG are applied if using Next.js.
- I check Lighthouse scores and adjust for Core Web Vitals.

### 8. Testing

- Unit and integration tests are added for critical components.
- I manually test across browsers and screen sizes (real devices) to ensure consistency and accessibility.

### 9. Review & Deployment

- I open a pull request for code review, address feedback, and ensure CI/CD pipelines pass.
- Once merged, the page is deployed, and I monitor for any post-launch performance or bug issues.

### 10. Documentation & Maintenance

- I document component usage, props, and known caveats.
- This ensures future maintainability and smooth handoff for other developers.

## If you have 5 different stylesheets, how would you best integrate them into the site?

I prioritize maintainability, performance, and avoiding conflicts.

### 1. Assess the Purpose of Each Stylesheet

- Determine if each stylesheet is global (applied across the site) or component/page-specific.
- Identify any overlapping styles or dependencies.

### 2. Combine and Minify Where Possible

- For global stylesheets, I combine them into a single minified CSS bundle to reduce HTTP requests and improve load performance.
- Tools like Webpack or Next.js built-in CSS support handle this automatically during build.

### 3. Use Modular or Scoped CSS for Components

- For page-specific or component-specific stylesheets, I use CSS Modules or Styled Components. This ensures styles don’t leak and avoids specificity conflicts.
- For example, `Button.module.css` would only apply to the Button component.

### 4. Load Styles in the Correct Order

- Load global styles first, then third-party stylesheets, and finally component-specific styles. This prevents overriding issues and ensures intended styles apply correctly.

### 5. Optimize for Performance

- Use `link rel="preload"` or `media` attributes for non-critical stylesheets to avoid blocking render.
- Lazy-load styles for routes/components that aren’t initially visible.

### 6. Consider CSS-in-JS if Using React/Next.js

- Some stylesheets can be integrated directly via CSS-in-JS solutions like Styled Components or Emotion for dynamic styling, reducing the need to manage multiple static CSS files.

## Can you describe the difference between progressive enhancement and graceful degradation?

- **Progressive Enhancement:** Core functionality first -> enhancements later.
- **Graceful Degradation:** Full-featured experience first -> fallback for limitations.

### How would you optimize a website's assets/resources?

### 1. Images

- **Format & Compression:** Modern formats like WebP or AVIF where supported. Compress images using tools like ImageOptim, TinyPNG, or built-in Next.js optimization.
- **Responsive Images:** `<picture>` or Next.js `<Image>` with multiple sizes to serve the appropriate image for each device.
- **Lazy Loading:** Load off-screen images lazily to reduce initial page load.

### 2. Fonts

- **Subset & Preload:** Only include necessary character sets and weights. Preload critical fonts to reduce FOUT/FOIT.
- **Use System Fonts When Possible:** This avoids additional network requests.
- **CDN**

### 3. CSS & JavaScript

- **Minification & Bundling:** Combine and minify CSS/JS files using Webpack, Vite, or Next.js build tools.
- **Code Splitting:** Break JS into chunks to load only what’s needed per page.
- **Tree Shaking:** Remove unused code from libraries to reduce bundle size.
- **Critical CSS:** Inline critical CSS to render above-the-fold content faster.

### 4. Caching & CDN

- **Cache Assets:** Configure long-lived caching headers for static assets to avoid repeated downloads.
- **Content Delivery Network:** Serve assets via a CDN to reduce latency and improve global performance.

### 5. Third-Party Scripts

- **Audit & Defer:** Only load essential scripts and defer or async load third-party scripts (analytics, chat widgets) to prevent render-blocking.
- **Monitor Impact:** Regularly measure their effect on performance using Lighthouse or WebPageTest.

### 6. Media & Other Resources

- **Video Optimization:** Use streaming formats and adaptive bitrates; consider lazy-loading or using thumbnails first.
- **SVGs:** Prefer inline SVGs for small icons; compress larger SVGs with something like SVGO.

### 7. Monitoring & Continuous Optimization

- **Performance Audits:** Use Lighthouse, WebPageTest, and browser dev tools to identify bottlenecks.
- **Automation:** Include optimization scripts in CI/CD pipelines to ensure all assets are optimized before deployment.

## How many resources will a browser download from a given domain at a time? What are the exceptions?

6 concurrent connections per domain for downloading resources like images, scripts, and CSS. This is done to prevent overloading the server. (But not in older browesers.)

## Name 3 ways to decrease page load (perceived or actual load time)

### 1. Optimize and Lazy-Load Assets

- **Images:** Compress images, use modern formats like WebP/AVIF, and serve responsive sizes.
- **Lazy loading:** Load images, videos, or other non-critical content only when they enter the viewport.
- **Benefit:** Reduces initial payload, improving perceived and actual load time.

### 2. Code Splitting & Minification

- **JavaScript/CSS:** Split code into smaller chunks and load only what’s needed per page.
- **Minification:** Remove whitespace, comments, and unused code (tree-shaking).
- **Benefit:** Reduces render-blocking resources and network payload, speeding up initial render.

### 3. Use a CDN & Enable Caching

- **CDN:** Serve static assets from servers closer to the user geographically.
- **Caching:** Leverage browser caching for static files and implement cache-control headers.
- **Benefit:** Reduces server round-trips and speeds up subsequent page loads.

## If you jumped on a project and they used tabs and you used spaces, what would you do?

- I would configure my editor (VS Code, etc.) to use **tabs** to match the project’s style for that codebase. This prevents unnecessary diff noise and maintains a consistent style across commits.
- If the project has **ESLint/Prettier** or similar tools configured, I’d follow their rules and let them automatically format code.
- If the project doesn’t have such tools, I might suggest adding a configuration for consistency, but only as a team discussion, not unilaterally.

## Describe how you would create a simple slideshow page

A slideshow page displays a sequence of images or content slides that users can navigate through either automatically or manually. The page consists of a container holding all slides, with only the current slide visible at any time. Navigation buttons (previous/next) allow manual control, and a timer can advance slides automatically for auto-play.

The slideshow uses **state** to track the current slide and updates this state on user interaction or timer events. **CSS transitions** provide smooth visual effects like fading or sliding between slides. The layout is **responsive**, ensuring images resize properly across devices, and optional features like dots/indicators, keyboard navigation, and lazy loading can enhance usability and performance.

In essence, it’s a **dynamic, interactive component** that combines structured markup, state management, and styling to create a user-friendly experience.

## Explain the importance of standards and standards bodies

1. Ensures Compatibility Across Platforms
2. Promotes Interoperability
3. Improves Maintainability and Scalability
4. Protects Users and the Ecosystem

## What is Flash of Unstyled Content? How do you avoid FOUC?

**Flash of Unstyled Content (FOUC)** is a visual issue where a web page briefly displays unstyled or default-styled HTML before the CSS is fully loaded and applied. This often happens when CSS is loaded asynchronously, or when web fonts or critical styles are delayed, causing a flicker of plain HTML content before the intended styles appear. We can avoid it by:

1. **Inline Critical CSX**: Include the essential CSS needed for above-the-fold content directly in the `<head>` of your HTML. This ensures the page renders correctly immediately.

2. **Load CSS Synchronously:** Place `<link>` tags for critical stylesheets in the `<head>` so they are loaded before the page renders.

3. **Use Preload / Preconnect:**`<link rel="preload" as="style">` or `<link rel="preconnect">` helps the browser fetch CSS faster.

4. **Font Loading Strategies:** Use `font-display: swap` to display fallback fonts while web fonts load, preventing text from disappearing.

5. **Server-Side Rendering / Static Generation:** In frameworks like Next.js, pre-render pages with styles applied to avoid FOUC on first load.

6. **CSS-in-JS & Component-Level Styles:** Some libraries like Styled Components or Emotion inject styles during server-side rendering, ensuring components are styled immediately when rendered.

## Explain what ARIA and screenreaders are, and how to make a website accessible

**ARIA** (Accessible Rich Internet Applications) is a set of HTML attributes that help assistive technologies understand custom UI elements.

**Screen readers** are software that read out content and describe UI elements for visually impaired users.

- Use **semantic HTML** (`<button>`, `<nav>`, `<form>`).
- Provide **labels and alt text** for elements.
- Ensure **keyboard navigation** works.
- Use **ARIA roles/properties** for custom components.
- Maintain good **color contrast** and test with screen readers.

## Explain some of the pros and cons for CSS animations versus JavaScript animations

**CSS Animations:** Smooth, GPU-accelerated, simple to implement; limited control for complex or interactive effects.

**JavaScript Animations:** Full control and interactivity, can handle complex sequences; more CPU-heavy and requires more code.

Using CSS for simple effects, JS for dynamic or complex animations.

## What does CORS stand for and what issue does it address?

**CORS** stands for **Cross-Origin Resource Sharing**. It addresses the security restriction in browsers that prevents a web page from making requests to a different domain than the one that served the page (the same-origin policy). Without CORS, scripts on one site cannot access resources from another site, protecting users from certain attacks like cross-site request forgery (CSRF).

CORS allows servers to specify which origins are permitted to access their resources by sending HTTP headers like `Access-Control-Allow-Origin`. This enables safe cross-origin requests while maintaining security.

## Can you explain what happens when you enter a URL into the browser?

1. **DNS Lookup:** Browser converts the domain (mosioc.com) into an IP address (192.168.1.x).
2. **TCP & TLS Handshake:** Browser connects to the server; HTTPS sites establish encryption.
3. **HTTP Request:** Browser requests the page and sends headers.
4. **Server Response:** Server returns HTML, CSS, JS, images, and other assets.
5. **Parsing & Rendering:** Browser builds the DOM (Document Object Model) and CSSOM (CSS Object Model), combines them into a render tree, runs JS, and paints the page.
6. **Additional Requests:** Browser fetches images, fonts, and scripts referenced in the page.
7. **Interactive Page:** Scripts run, event listeners attach, and the page becomes fully interactive.

## Describe the difference between SSR and CSR. Discuss the pros and cons. Are you familiar with static rendering? Rehydration?

- **SSR (Server-Side Rendering):** Server generates the full HTML for a page and sends it to the browser. Full HTML from server -> fast first load, better SEO, heavier server.
- **CSR (Client-Side Rendering):** Server sends minimal HTML and JavaScript; the browser builds the page dynamically. HTML + JS from server -> dynamic, interactive, slower initial load.
- **Static Rendering (SSG / Static Site Generation):** Generates HTML at build time instead of request time. HTML + JS from server -> dynamic, interactive, slower initial load.
- **Rehydration:** The process where client-side JavaScript takes over the server-rendered HTML to make it interactive. Bridges SSR/SSG HTML to become fully interactive on the client.

### Server-Side Rendering (SSR)

- **Pros:**

  - Faster first-page load (good for SEO and performance on slow devices).
  - Pre-rendered content is immediately visible.

- **Cons:**

  - More server load.
  - Slower subsequent navigation unless combined with client-side hydration.

### Client-Side Rendering (CSR)

- **Pros:**

  - Rich interactivity and smoother subsequent navigation.
  - Less server load.

- **Cons:**

  - Slower initial load.
  - SEO requires extra handling (pre-rendering or dynamic rendering).

### Static Rendering (SSG / Static Site Generation)

- **Pros:**

  - Extremely fast load, low server load, good SEO.

- **Cons:**

  - Not suitable for highly dynamic content.

### Rehydration

- Essential in SSR or SSG with frameworks like React/Next.js.
