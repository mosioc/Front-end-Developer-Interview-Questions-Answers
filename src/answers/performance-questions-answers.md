---
title: Performance Questions - Answers
layout: layouts/page.njk
---

## What tools would you use to find a performance bug in your code?

- **Chrome DevTools Performance panel** - to record and analyze runtime performance, identify long tasks, and spot bottlenecks in JavaScript execution
- **Lighthouse** - for auditing overall performance, identifying opportunities like render-blocking resources, and getting actionable metrics (FCP, LCP, TBT, etc.)
- **React DevTools Profiler** - to see which components re-render unnecessarily and how long renders take
- **Browser console warnings** - React often warns about missing keys, excessive renders, or deprecated patterns that cause performance issues
- **Network panel** - to spot slow API calls, large payloads, or missing compression
- **webpack-bundle-analyzer** - to visualize bundle size and find bloated dependencies or duplicate packages
- **why-did-you-render** - to log unnecessary re-renders in React during development
- **Memory panel** (Chrome) - to detect memory leaks by taking heap snapshots and analyzing retainers

## What are some ways you may improve your website's scrolling performance?

- **Avoid expensive operations in scroll handlers** - throttle or debounce scroll events, and use `requestAnimationFrame` for visual updates
- **Use `passive: true` event listeners** for touch and wheel events so the browser can optimize scrolling without waiting for JavaScript
- **Virtualize long lists** - use libraries like `react-window` or `react-virtuoso` to only render visible items
- **Promote elements to their own compositor layer** - apply `will-change: transform` or use `transform: translateZ(0)` sparingly for elements that animate or scroll frequently
- **Avoid forced synchronous layouts** - don't read layout properties (like `offsetHeight`) immediately after writing to the DOM; batch reads and writes separately
- **Use `contain` CSS property** - `contain: content` or `content-visibility: auto` tells the browser an element's layout is independent, reducing layout calculation scope
- **Lazy-load off-screen content** - defer rendering of below-the-fold content until it's near the viewport
- **Minimize layout thrashing** - limit reflow triggers by keeping CSS simple, avoiding deeply nested selectors, and reducing DOM complexity
- **Compress and properly size images** - large images cause jank as they decode; use `loading="lazy"` and responsive image techniques

## Explain the difference between layout, painting, and compositing

These are the three main stages of the browser's pixel pipeline that turn HTML/CSS into what we see on screen:

### Layout (also called Reflow)
The browser calculates the exact **position and size** of every element on the page. This happens when geometry-related properties change-width, height, padding, margin, font-size, etc. Layout is the most expensive stage because changing one element can cascade and force recalculation of many others.

### Painting
Once positions are known, the browser **fills in the pixels** for each element-colors, borders, shadows, text, background images. This is a rasterization step where the browser essentially draws the visual appearance of each element onto surfaces. Triggered by changes to visual-only properties like `color`, `background-color`, `box-shadow`, or `outline`.

### Compositing
The browser takes the painted layers and **assembles them onto the final screen** in the correct stacking order. This is the cheapest stage-it's handled by the GPU and can be done without touching the main thread. Only properties like `transform`, `opacity`, and `filter` can be animated purely on the compositor without triggering layout or paint.

**The key takeaway**: For smooth 60fps animations, stick to compositor-only properties (`transform` and `opacity`). Triggering layout or paint during animation causes jank because those stages run on the main thread.
