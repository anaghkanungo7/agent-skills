# React Performance Patterns

Battle-tested patterns for building fast, responsive React applications. Learn to identify bottlenecks and implement optimizations that matter.

## What This Skill Does

This skill helps AI agents guide you through React performance optimization:

- **Identify Bottlenecks**: Use profiling tools to find real performance issues
- **Memoization Strategies**: When and how to use memo, useMemo, useCallback
- **Code Splitting**: Reduce initial bundle size with lazy loading
- **List Optimization**: Virtualize long lists for smooth scrolling
- **Context Optimization**: Prevent unnecessary re-renders from context
- **Bundle Optimization**: Tree shaking, dynamic imports, and dependency management
- **Image Optimization**: Responsive images, lazy loading, modern formats

## When to Use This Skill

Use this skill when you need to:

- Improve slow React application performance
- Reduce bundle size
- Optimize component re-renders
- Fix janky scrolling or slow interactions
- Improve Core Web Vitals (LCP, INP, CLS)
- Implement virtualization for large lists
- Optimize form performance
- Profile and debug performance issues

## What You'll Learn

### Core Topics

1. **Profiling & Measurement**
   - React DevTools Profiler
   - Chrome Performance tab
   - Lighthouse audits
   - Core Web Vitals monitoring

2. **Rendering Optimization**
   - React.memo() for components
   - useMemo() for expensive calculations
   - useCallback() for stable functions
   - Avoiding inline objects/arrays

3. **Code Splitting**
   - Route-based splitting
   - Component lazy loading
   - Dynamic imports for libraries
   - Preloading strategies

4. **List Virtualization**
   - react-window for long lists
   - Fixed vs. variable size lists
   - Infinite scroll optimization

5. **Context Performance**
   - Splitting contexts by concern
   - Context selectors (zustand, etc.)
   - Memoizing context values

6. **Bundle Optimization**
   - Tree shaking best practices
   - Analyzing bundle size
   - Removing unused dependencies
   - Dynamic imports for heavy libraries

7. **Async Performance**
   - Debouncing user input
   - Throttling scroll/resize handlers
   - Web Workers for heavy computation
   - useTransition for non-blocking updates

8. **Image Optimization**
   - Next.js Image component
   - Responsive images (srcset/sizes)
   - Lazy loading strategies
   - Modern formats (WebP/AVIF)

## Installation

```bash
npx skills add anaghkanungo7/agent-skills/react-performance-patterns
```

## Example Usage

After installing, your AI coding assistant will provide expert performance guidance:

**Example 1: Diagnosing slow renders**
```
User: "My list of 1000 items is really slow"

Agent: "Let's optimize with virtualization:
1. Profile the component to measure current performance
2. Implement react-window for rendering only visible items
3. Expected improvement: 5s render → 50ms render"
```

**Example 2: Reducing bundle size**
```
User: "My bundle is 2MB, how can I reduce it?"

Agent: "Let's analyze and optimize:
1. Run bundle analyzer to identify large dependencies
2. Implement code splitting for routes
3. Tree shake lodash imports
4. Lazy load heavy chart libraries
Target: Get under 500KB initial bundle"
```

**Example 3: Fixing unnecessary re-renders**
```
User: "This component re-renders too often"

Agent: "Let's diagnose and fix:
1. Check React DevTools Profiler to see why it's rendering
2. Add React.memo if props are stable
3. Use useMemo for inline object/array props
4. Ensure callbacks are useCallback-wrapped"
```

## Key Patterns Covered

### Quick Wins
- Production build optimization
- Route-based code splitting
- Lazy loading below-fold components
- Memoizing expensive components
- Virtualizing long lists

### Advanced Optimization
- Web Workers for heavy computation
- Bundle analysis and tree shaking
- Context splitting for granular updates
- Incremental rendering with useTransition
- Custom debounce/throttle hooks

### Tooling
- React DevTools Profiler usage
- Chrome Performance profiling
- Lighthouse audits
- Bundle size analysis
- Performance budgets

## Common Problems Solved

- **Slow list scrolling**: Virtualization with react-window (100+ items)
- **Large bundle size**: Code splitting and dynamic imports
- **Unnecessary re-renders**: Memoization strategies
- **Slow form input**: Debouncing and controlled vs uncontrolled
- **Heavy computations blocking UI**: Web Workers or useTransition
- **Context causing re-renders**: Split contexts or use selectors
- **Poor Core Web Vitals**: Image optimization and render optimization

## Performance Improvements

Real-world impact from these patterns:

- **Virtualization**: 5000ms → 50ms render time (100x faster)
- **Code splitting**: 2MB → 300KB initial bundle (85% reduction)
- **Memoization**: 60 → 5 renders per interaction (92% reduction)
- **Image optimization**: 4s → 1.2s LCP (70% improvement)
- **Debouncing**: 500 → 5 API calls per search (99% reduction)

## Complementary Skills

This skill works well with:
- `seo-optimization-guide` - For Core Web Vitals and SEO performance
- `vector-design-best-practices` - For optimizing SVG performance
- Testing skills - For performance regression testing

## Best Practices Enforced

- Measure before optimizing (profile first!)
- Code split by route minimum
- Virtualize lists over 100 items
- Memoize expensive pure components
- Debounce user input (search, autocomplete)
- Optimize images (WebP, lazy loading, dimensions)
- Keep bundle size under 500KB initial
- Maintain 60fps interactions
- Core Web Vitals in "Good" range

## Tools Covered

- **React DevTools Profiler**: Component render profiling
- **Chrome DevTools Performance**: Main thread analysis
- **Lighthouse**: Performance audits
- **Webpack Bundle Analyzer**: Bundle size visualization
- **react-window**: List virtualization
- **use-debounce**: Debouncing and throttling

## Contributing

Found an issue or have a suggestion? Visit the [GitHub repository](https://github.com/anaghkanungo7/agent-skills) to contribute.

## License

MIT

---

**Created by**: Anagh Kanungo
**Version**: 1.0.0
**Last Updated**: 2026