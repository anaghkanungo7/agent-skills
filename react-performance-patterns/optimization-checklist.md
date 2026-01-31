# React Performance Optimization Checklist

Use this checklist to systematically optimize your React application. Items are ordered by impact (biggest wins first).

## Before You Start: Measure

- [ ] **Run Lighthouse audit** - Get baseline performance score
- [ ] **Profile with React DevTools** - Identify slow components
- [ ] **Check bundle size** - Run `npm run build` and note size
- [ ] **Measure Core Web Vitals** - LCP, INP, CLS scores
- [ ] **Identify actual bottlenecks** - Don't optimize blindly

## High Impact (Do These First)

### 1. Production Build

- [ ] **Build in production mode** - `NODE_ENV=production`
- [ ] **Remove React.StrictMode** - In production build
- [ ] **Enable minification** - Should be default in production
- [ ] **Remove console.logs** - Or use build tools to strip them

```bash
# Check if you're in production mode
# Should NOT see warnings about development mode
npm run build && npm run start
```

### 2. Code Splitting

- [ ] **Split by route** - Each route is a separate chunk
- [ ] **Lazy load heavy components** - Charts, editors, modals
- [ ] **Dynamic import third-party libraries** - Load when needed
- [ ] **Measure bundle reduction** - Compare before/after sizes

```tsx
// Before: Everything in one bundle (2MB)
import Dashboard from './Dashboard';
import HeavyChart from './HeavyChart';

// After: Lazy loaded (300KB initial, 200KB on demand)
const Dashboard = lazy(() => import('./Dashboard'));
const HeavyChart = lazy(() => import('./HeavyChart'));
```

**Impact**: 50-80% initial bundle size reduction

### 3. Image Optimization

- [ ] **Use Next.js Image component** - Or equivalent for your framework
- [ ] **Specify width/height** - Prevents layout shift (CLS)
- [ ] **Use modern formats** - WebP or AVIF
- [ ] **Lazy load below-fold images** - `loading="lazy"`
- [ ] **Compress images** - Use tools like Squoosh or TinyPNG
- [ ] **Use CDN** - For faster delivery

```tsx
// Before: Unoptimized image
<img src="/hero.jpg" alt="Hero" />

// After: Optimized with Next.js Image
<Image
  src="/hero.jpg"
  alt="Hero"
  width={1200}
  height={630}
  priority // Above-fold
  placeholder="blur"
/>
```

**Impact**: 1-3 second LCP improvement

### 4. Virtualize Long Lists

- [ ] **Identify lists with 100+ items** - Profile to confirm slowness
- [ ] **Install react-window** - `npm install react-window`
- [ ] **Implement FixedSizeList or VariableSizeList** - Render only visible
- [ ] **Measure improvement** - Should see 10-100x faster renders

```tsx
// Before: 10,000 items = 5 second render
{items.map(item => <ItemRow item={item} />)}

// After: 10,000 items = 50ms render
<FixedSizeList height={600} itemCount={items.length} itemSize={50}>
  {({ index, style }) => (
    <div style={style}><ItemRow item={items[index]} /></div>
  )}
</FixedSizeList>
```

**Impact**: 10-100x faster rendering for long lists

### 5. Debounce Search/Input

- [ ] **Identify inputs that trigger expensive operations** - Search, autocomplete
- [ ] **Add debounce (300-500ms)** - Wait for user to stop typing
- [ ] **Show loading state** - Indicate search is happening
- [ ] **Measure API call reduction** - Should drop 90%+

```tsx
// Before: 500 API calls while typing "react performance"
const handleSearch = (value: string) => {
  searchAPI(value); // Called on every keystroke
};

// After: 5 API calls (only when user pauses)
const debouncedSearch = useDebouncedCallback(
  (value: string) => searchAPI(value),
  300
);
```

**Impact**: 90-99% reduction in unnecessary operations

## Medium Impact (Optimize After High Impact Items)

### 6. Memoization

- [ ] **Identify frequently re-rendering components** - Use React DevTools Profiler
- [ ] **Add React.memo to expensive pure components** - Check if props actually change
- [ ] **Use useMemo for heavy calculations** - Filtering, sorting large arrays
- [ ] **Use useCallback for callbacks to memoized children** - Stable references
- [ ] **Verify memoization is working** - Re-profile to confirm fewer renders

```tsx
// Memoize expensive component
const ExpensiveComponent = memo(({ data }: Props) => {
  // Heavy rendering logic
  return <div>{processData(data)}</div>;
});

// Memoize expensive calculation
const sorted = useMemo(
  () => data.sort((a, b) => expensiveCompare(a, b)),
  [data]
);

// Stable callback for memoized child
const handleClick = useCallback(() => {
  doSomething();
}, []);
```

**When to use memo:**
- [ ] Component renders frequently with same props
- [ ] Rendering is expensive (> 16ms)
- [ ] Props are primitives or stable references

**When NOT to use memo:**
- [ ] Component already fast (< 5ms)
- [ ] Props are always new objects/arrays
- [ ] More components will read the value than will update it

### 7. Context Optimization

- [ ] **Split large contexts** - Separate user, theme, notifications
- [ ] **Memoize context values** - Prevent new object on every render
- [ ] **Use context selectors** - zustand, jotai, or custom solution
- [ ] **Keep context values stable** - Don't recreate objects/functions

```tsx
// Before: One big context, everything re-renders
const AppContext = createContext({ user, theme, notifications });

// After: Split contexts, components subscribe to what they need
const UserContext = createContext(user);
const ThemeContext = createContext(theme);
const NotificationContext = createContext(notifications);
```

### 8. Bundle Optimization

- [ ] **Run bundle analyzer** - `ANALYZE=true npm run build`
- [ ] **Identify large dependencies** - Look for > 100KB packages
- [ ] **Tree shake imports** - Use `import { map } from 'lodash-es'` not `import _ from 'lodash'`
- [ ] **Remove unused dependencies** - Run `npx depcheck`
- [ ] **Replace heavy libraries** - Consider lighter alternatives
- [ ] **Use native alternatives** - Replace simple utils with vanilla JS

```tsx
// Before: Entire lodash (500KB)
import _ from 'lodash';
const doubled = _.map(arr, n => n * 2);

// After: Just map function (5KB)
import map from 'lodash-es/map';
const doubled = map(arr, n => n * 2);

// Best: Native (0KB)
const doubled = arr.map(n => n * 2);
```

**Impact**: 20-50% bundle size reduction

### 9. Avoid Inline Objects/Arrays

- [ ] **Find inline styles in render** - `style={{ color: 'red' }}`
- [ ] **Move to constants** - Or use useMemo for dynamic values
- [ ] **Check memoized components** - Ensure props are stable

```tsx
// Before: New object every render breaks memoization
<MemoizedChild style={{ color: 'red' }} options={['a', 'b']} />

// After: Stable references
const STYLE = { color: 'red' };
const OPTIONS = ['a', 'b'];
<MemoizedChild style={STYLE} options={OPTIONS} />
```

### 10. Form Optimization

- [ ] **Use form libraries** - React Hook Form or Formik
- [ ] **Uncontrolled inputs when possible** - Reduce re-renders
- [ ] **Debounce validation** - Don't validate on every keystroke
- [ ] **Split large forms** - Multiple steps or lazy load sections

```tsx
// Before: Re-renders entire form on every keystroke
const [formData, setFormData] = useState({});
<input value={formData.name} onChange={(e) => setFormData({...formData, name: e.target.value})} />

// After: No unnecessary re-renders with React Hook Form
const { register } = useForm();
<input {...register('name')} />
```

## Lower Impact (Polish After Main Optimizations)

### 11. Reduce Re-renders

- [ ] **Children as props pattern** - Prevent re-renders
- [ ] **Move state down** - Closer to where it's used
- [ ] **Lift content up** - Out of components that re-render

```tsx
// Before: SlowComponent re-renders when count changes
function Parent() {
  const [count, setCount] = useState(0);
  return (
    <div>
      <button onClick={() => setCount(count + 1)}>{count}</button>
      <SlowComponent /> {/* Re-renders unnecessarily */}
    </div>
  );
}

// After: SlowComponent doesn't re-render
function Parent() {
  return (
    <div>
      <Counter />
      <SlowComponent /> {/* Doesn't re-render */}
    </div>
  );
}

function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

### 12. Lazy Load Heavy Components

- [ ] **Identify components > 100KB** - Charts, editors, maps
- [ ] **Lazy load with React.lazy** - Only when needed
- [ ] **Add Suspense boundary** - With loading fallback
- [ ] **Preload on hover** - For better UX

```tsx
const HeavyChart = lazy(() => import('./HeavyChart'));

<Suspense fallback={<ChartSkeleton />}>
  <HeavyChart data={data} />
</Suspense>
```

### 13. Throttle Scroll/Resize

- [ ] **Identify scroll/resize handlers** - Can fire 100+ times/second
- [ ] **Add throttle (100-200ms)** - Limit execution frequency
- [ ] **Use passive event listeners** - Better scroll performance

```tsx
// Before: Fires 100+ times while scrolling
const handleScroll = () => {
  if (isNearBottom()) loadMore();
};

// After: Fires max once per 200ms
const throttledScroll = useThrottledCallback(
  () => { if (isNearBottom()) loadMore(); },
  200
);
```

### 14. Web Workers for Heavy Computation

- [ ] **Identify blocking computations** - Freezes UI
- [ ] **Move to Web Worker** - Off main thread
- [ ] **Measure main thread time** - Should drop significantly

```tsx
const worker = new Worker(new URL('./worker.ts', import.meta.url));
worker.postMessage(largeDataset);
worker.onmessage = (e) => setResult(e.data);
```

### 15. Remove Dev-Only Code

- [ ] **Remove console.logs** - Or use webpack to strip
- [ ] **Remove debugging code** - Leftover from development
- [ ] **Remove React DevTools comments** - In production HTML

```js
// webpack config to remove console in production
optimization: {
  minimize: true,
  minimizer: [
    new TerserPlugin({
      terserOptions: {
        compress: {
          drop_console: true,
        },
      },
    }),
  ],
},
```

## Framework-Specific Optimizations

### Next.js

- [ ] **Use Server Components** - Reduce client JS (App Router)
- [ ] **Static generation when possible** - `generateStaticParams`
- [ ] **Incremental Static Regeneration** - For dynamic content
- [ ] **Edge runtime for API routes** - Faster than Node.js runtime
- [ ] **next/font for web fonts** - Automatic optimization

### Vite

- [ ] **Enable SWC/esbuild** - Faster than Babel
- [ ] **Code splitting with dynamic imports** - Vite handles automatically
- [ ] **Preload critical chunks** - Use `vite-plugin-pwa`

## Testing Performance

### Development

```bash
# Profile in development
npm run dev
# Open React DevTools > Profiler
# Record interaction
# Analyze render times
```

### Production

```bash
# Build production bundle
npm run build

# Analyze bundle size
ANALYZE=true npm run build

# Run Lighthouse
npx lighthouse http://localhost:3000 --view

# Check Core Web Vitals
# Use PageSpeed Insights or Chrome DevTools
```

## Performance Budget

Set and enforce:

```json
{
  "budgets": [
    {
      "initial": {
        "js": 300, // 300KB max JS
        "css": 50,  // 50KB max CSS
        "total": 500 // 500KB total
      },
      "metrics": {
        "FCP": 1800,  // First Contentful Paint < 1.8s
        "LCP": 2500,  // Largest Contentful Paint < 2.5s
        "TTI": 3800,  // Time to Interactive < 3.8s
        "CLS": 0.1    // Cumulative Layout Shift < 0.1
      }
    }
  ]
}
```

## Measuring Success

Track these metrics before and after optimization:

- [ ] **Lighthouse score**: Target > 90
- [ ] **Initial bundle size**: Target < 500KB
- [ ] **LCP**: Target < 2.5s
- [ ] **INP**: Target < 200ms
- [ ] **CLS**: Target < 0.1
- [ ] **Component render time**: Target < 16ms (60fps)
- [ ] **Time to Interactive**: Target < 3.8s

## Common Mistakes to Avoid

- [ ] **Don't optimize too early** - Measure first
- [ ] **Don't memoize everything** - Has overhead
- [ ] **Don't split too aggressively** - Can hurt UX with many loading states
- [ ] **Don't use inline functions as dependencies** - Breaks memoization
- [ ] **Don't skip production build testing** - Dev mode is slower

## Tools

- **React DevTools Profiler**: Component render profiling
- **Chrome DevTools Performance**: CPU/memory profiling
- **Lighthouse**: Comprehensive audits
- **Webpack Bundle Analyzer**: Bundle size visualization
- **PageSpeed Insights**: Core Web Vitals
- **Web Vitals Extension**: Real-time CWV monitoring

## Quick Reference: When to Use What

| Problem | Solution | Expected Impact |
|---------|----------|-----------------|
| Slow list (100+ items) | Virtualization | 10-100x faster |
| Large bundle (> 1MB) | Code splitting | 50-80% smaller |
| Many API calls on typing | Debounce | 90-99% fewer calls |
| Component re-renders often | React.memo | 50-90% fewer renders |
| Heavy calculation | useMemo | Varies |
| Slow image load | Next/Image + WebP | 1-3s LCP improvement |
| Context re-renders | Split contexts | 50-80% fewer renders |
| Inline objects/arrays | Move to constants | Enables memoization |

## Regular Performance Reviews

**Monthly:**
- [ ] Run Lighthouse audit
- [ ] Check bundle size growth
- [ ] Review new dependencies

**Quarterly:**
- [ ] Full profiling session
- [ ] Review Core Web Vitals
- [ ] Update performance budget

---

**Remember**: The best optimization is the one you measure. Always profile before and after changes to verify impact.