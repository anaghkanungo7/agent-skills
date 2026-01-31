---
name: React Performance Patterns
description: Battle-tested patterns for optimizing React applications, from component design to bundle optimization
---

# React Performance Patterns

You are an expert in React performance optimization. You help developers identify performance bottlenecks, implement efficient rendering patterns, and build fast, responsive React applications. Your guidance is based on real-world production experience and current best practices.

## Core Performance Principles

### 1. Measure Before Optimizing

Never optimize blindly. Always profile first:

```tsx
// Use React DevTools Profiler
import { Profiler } from 'react';

function onRenderCallback(
  id: string,
  phase: 'mount' | 'update',
  actualDuration: number,
  baseDuration: number,
  startTime: number,
  commitTime: number
) {
  console.log({ id, phase, actualDuration });
}

<Profiler id="ExpensiveComponent" onRender={onRenderCallback}>
  <ExpensiveComponent />
</Profiler>
```

**Key metrics to track:**
- Render count
- Render duration
- Component mount/update time
- Bundle size
- Network waterfall
- Core Web Vitals (LCP, INP, CLS)

### 2. Avoid Unnecessary Renders

React re-renders when:
- State changes
- Props change
- Parent re-renders
- Context value changes

Your job: minimize wasted renders.

### 3. Code Split Aggressively

Users shouldn't download code for pages they never visit.

### 4. Optimize Heavy Operations

Move expensive calculations off the main thread or cache results.

## Pattern 1: Memoization

### React.memo() - Prevent Component Re-renders

```tsx
// Before: Re-renders on every parent render
function UserCard({ user }: { user: User }) {
  return <div>{user.name}</div>;
}

// After: Only re-renders when user prop changes
const UserCard = memo(({ user }: { user: User }) => {
  return <div>{user.name}</div>;
});
```

**When to use:**
- Pure functional components
- Components that render frequently with same props
- Expensive render operations
- List items

**When NOT to use:**
- Component always receives new props
- Props contain objects/arrays created inline
- Component is already fast

### useMemo() - Memoize Expensive Calculations

```tsx
function DataTable({ data, filters }: Props) {
  // Bad: Recalculates on every render
  const filtered = data.filter(item =>
    filters.every(f => f.fn(item))
  );

  // Good: Only recalculates when data or filters change
  const filtered = useMemo(
    () => data.filter(item => filters.every(f => f.fn(item))),
    [data, filters]
  );

  return <Table data={filtered} />;
}
```

**When to use:**
- Filtering/sorting large arrays
- Complex calculations
- Derived data that's expensive to compute
- Creating objects/arrays passed as props

**Cost/benefit check:**
```tsx
// Not worth it (simple operation)
const doubled = useMemo(() => count * 2, [count]);

// Worth it (expensive operation)
const sorted = useMemo(
  () => items.sort((a, b) => expensiveCompare(a, b)),
  [items]
);
```

### useCallback() - Memoize Functions

```tsx
function Parent() {
  // Bad: New function on every render
  const handleClick = () => {
    doSomething();
  };

  // Good: Stable function reference
  const handleClick = useCallback(() => {
    doSomething();
  }, []);

  return <MemoizedChild onClick={handleClick} />;
}
```

**When to use:**
- Passing callbacks to memoized children
- Dependencies in useEffect/useMemo
- Creating stable event handlers
- Working with debounce/throttle

**Common mistake:**
```tsx
// Mistake: useCallback with new object in dependency
const handleClick = useCallback(() => {
  doSomething(data);
}, [data]); // If 'data' is a new object each render, callback still changes

// Better: Destructure stable values
const { id, name } = data;
const handleClick = useCallback(() => {
  doSomething({ id, name });
}, [id, name]);
```

## Pattern 2: Code Splitting & Lazy Loading

### Component-Level Code Splitting

```tsx
import { lazy, Suspense } from 'react';

// Bad: Bundles everything upfront
import HeavyChart from './HeavyChart';
import AdminPanel from './AdminPanel';

// Good: Load on demand
const HeavyChart = lazy(() => import('./HeavyChart'));
const AdminPanel = lazy(() => import('./AdminPanel'));

function Dashboard() {
  const [showChart, setShowChart] = useState(false);

  return (
    <div>
      <button onClick={() => setShowChart(true)}>
        Show Chart
      </button>

      {showChart && (
        <Suspense fallback={<Skeleton />}>
          <HeavyChart />
        </Suspense>
      )}
    </div>
  );
}
```

### Route-Based Code Splitting

```tsx
// Next.js: Automatic code splitting by route
// app/dashboard/page.tsx
export default function DashboardPage() {
  return <Dashboard />;
}

// React Router: Manual code splitting
const Dashboard = lazy(() => import('./pages/Dashboard'));
const Profile = lazy(() => import('./pages/Profile'));

function App() {
  return (
    <Routes>
      <Route
        path="/dashboard"
        element={
          <Suspense fallback={<Spinner />}>
            <Dashboard />
          </Suspense>
        }
      />
      <Route
        path="/profile"
        element={
          <Suspense fallback={<Spinner />}>
            <Profile />
          </Suspense>
        }
      />
    </Routes>
  );
}
```

### Preloading for Better UX

```tsx
// Preload on hover (faster perceived performance)
function Navigation() {
  const handleMouseEnter = () => {
    // Preload Dashboard chunk
    import('./pages/Dashboard');
  };

  return (
    <Link
      to="/dashboard"
      onMouseEnter={handleMouseEnter}
    >
      Dashboard
    </Link>
  );
}
```

## Pattern 3: Virtualization (Long Lists)

For lists with 100+ items, render only what's visible.

### Using react-window

```tsx
import { FixedSizeList } from 'react-window';

// Bad: Rendering 10,000 items
function BadList({ items }: { items: Item[] }) {
  return (
    <div>
      {items.map(item => (
        <ItemRow key={item.id} item={item} />
      ))}
    </div>
  );
}

// Good: Virtualized (only renders ~20 visible items)
function GoodList({ items }: { items: Item[] }) {
  return (
    <FixedSizeList
      height={600}
      itemCount={items.length}
      itemSize={50}
      width="100%"
    >
      {({ index, style }) => (
        <div style={style}>
          <ItemRow item={items[index]} />
        </div>
      )}
    </FixedSizeList>
  );
}
```

**Performance impact:**
- 10,000 items without virtualization: 5-10 second render
- 10,000 items with virtualization: 50-100ms render

### Dynamic Size Lists

```tsx
import { VariableSizeList } from 'react-window';

function DynamicList({ items }: { items: Item[] }) {
  const getItemSize = (index: number) => {
    // Return height based on content
    return items[index].type === 'large' ? 120 : 60;
  };

  return (
    <VariableSizeList
      height={600}
      itemCount={items.length}
      itemSize={getItemSize}
      width="100%"
    >
      {({ index, style }) => (
        <div style={style}>
          <ItemRow item={items[index]} />
        </div>
      )}
    </VariableSizeList>
  );
}
```

## Pattern 4: Optimize Context Usage

### Problem: Context Causes Unnecessary Re-renders

```tsx
// Bad: Every consumer re-renders on any state change
const AppContext = createContext<AppState>(null);

function AppProvider({ children }: { children: ReactNode }) {
  const [user, setUser] = useState<User | null>(null);
  const [theme, setTheme] = useState<'light' | 'dark'>('light');
  const [notifications, setNotifications] = useState<Notification[]>([]);

  const value = { user, setUser, theme, setTheme, notifications, setNotifications };

  return <AppContext.Provider value={value}>{children}</AppContext.Provider>;
}

// This re-renders when ANYTHING in context changes
function UserAvatar() {
  const { user } = useContext(AppContext); // Re-renders on theme change!
  return <Avatar user={user} />;
}
```

### Solution 1: Split Contexts

```tsx
// Good: Separate concerns
const UserContext = createContext<UserState>(null);
const ThemeContext = createContext<ThemeState>(null);
const NotificationContext = createContext<NotificationState>(null);

// Now components only subscribe to what they need
function UserAvatar() {
  const { user } = useContext(UserContext); // Only re-renders on user change
  return <Avatar user={user} />;
}
```

### Solution 2: Context Selectors

```tsx
// Using zustand (or similar library)
import create from 'zustand';

const useStore = create<AppState>((set) => ({
  user: null,
  theme: 'light',
  setUser: (user) => set({ user }),
  setTheme: (theme) => set({ theme }),
}));

// Select only what you need
function UserAvatar() {
  const user = useStore((state) => state.user); // Only re-renders on user change
  return <Avatar user={user} />;
}

function ThemeSwitcher() {
  const theme = useStore((state) => state.theme); // Only re-renders on theme change
  return <ThemeToggle theme={theme} />;
}
```

### Solution 3: Memoize Context Value

```tsx
function AppProvider({ children }: { children: ReactNode }) {
  const [user, setUser] = useState<User | null>(null);

  // Prevent new object on every render
  const value = useMemo(
    () => ({ user, setUser }),
    [user]
  );

  return <AppContext.Provider value={value}>{children}</AppContext.Provider>;
}
```

## Pattern 5: Debounce & Throttle

### Debounce: Wait for User to Stop Typing

```tsx
import { useDebouncedCallback } from 'use-debounce';

function SearchInput() {
  const [query, setQuery] = useState('');

  // Bad: API call on every keystroke
  const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
    const value = e.target.value;
    setQuery(value);
    searchAPI(value); // 🔥 Too many requests!
  };

  // Good: Wait 300ms after user stops typing
  const debouncedSearch = useDebouncedCallback(
    (value: string) => {
      searchAPI(value);
    },
    300
  );

  const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
    const value = e.target.value;
    setQuery(value);
    debouncedSearch(value);
  };

  return <input value={query} onChange={handleChange} />;
}
```

**Custom debounce hook:**

```tsx
function useDebounce<T>(value: T, delay: number): T {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    return () => {
      clearTimeout(handler);
    };
  }, [value, delay]);

  return debouncedValue;
}

// Usage
function SearchResults() {
  const [query, setQuery] = useState('');
  const debouncedQuery = useDebounce(query, 300);

  useEffect(() => {
    if (debouncedQuery) {
      searchAPI(debouncedQuery);
    }
  }, [debouncedQuery]);

  return <input value={query} onChange={(e) => setQuery(e.target.value)} />;
}
```

### Throttle: Limit Execution Frequency

```tsx
import { useThrottledCallback } from 'use-debounce';

function InfiniteScroll() {
  // Bad: Fires hundreds of times while scrolling
  const handleScroll = () => {
    if (isNearBottom()) {
      loadMore();
    }
  };

  // Good: Fires at most once every 200ms
  const throttledScroll = useThrottledCallback(
    () => {
      if (isNearBottom()) {
        loadMore();
      }
    },
    200
  );

  return <div onScroll={throttledScroll}>{/* content */}</div>;
}
```

## Pattern 6: Optimize Images

### Use Next.js Image Component

```tsx
import Image from 'next/image';

// Bad: Unoptimized, layout shift, loads all sizes
<img src="/hero.jpg" alt="Hero" />

// Good: Optimized, responsive, lazy loaded
<Image
  src="/hero.jpg"
  alt="Hero"
  width={1200}
  height={630}
  priority // For above-fold images
  placeholder="blur"
  blurDataURL="data:image/jpeg;base64,..."
/>
```

**Benefits:**
- Automatic WebP/AVIF format
- Responsive image sizes
- Lazy loading by default
- Prevents layout shift (width/height specified)
- Built-in blur placeholder

### For Non-Next.js Projects

```tsx
<img
  src="/hero-800.webp"
  srcSet="
    /hero-400.webp 400w,
    /hero-800.webp 800w,
    /hero-1200.webp 1200w
  "
  sizes="(max-width: 600px) 400px, (max-width: 1200px) 800px, 1200px"
  alt="Hero image"
  width="1200"
  height="630"
  loading="lazy"
/>
```

## Pattern 7: Optimize Heavy Computations

### Web Workers for CPU-Intensive Tasks

```tsx
// worker.ts
self.onmessage = (e: MessageEvent) => {
  const { data } = e;

  // Expensive computation
  const result = data.map((item) => {
    return complexCalculation(item);
  });

  self.postMessage(result);
};

// Component.tsx
function DataProcessor({ data }: { data: number[] }) {
  const [result, setResult] = useState<number[]>([]);

  useEffect(() => {
    const worker = new Worker(new URL('./worker.ts', import.meta.url));

    worker.postMessage(data);

    worker.onmessage = (e: MessageEvent) => {
      setResult(e.data);
    };

    return () => worker.terminate();
  }, [data]);

  return <Chart data={result} />;
}
```

### Incremental Rendering (Time Slicing)

```tsx
import { useTransition } from 'react';

function SearchResults() {
  const [query, setQuery] = useState('');
  const [isPending, startTransition] = useTransition();

  const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
    const value = e.target.value;

    // High priority: Update input immediately
    setQuery(value);

    // Low priority: Update results (can be interrupted)
    startTransition(() => {
      setSearchResults(search(value));
    });
  };

  return (
    <>
      <input value={query} onChange={handleChange} />
      {isPending && <Spinner />}
      <Results data={searchResults} />
    </>
  );
}
```

## Pattern 8: Bundle Optimization

### Analyze Your Bundle

```bash
# Next.js
ANALYZE=true npm run build

# Create React App
npm install --save-dev webpack-bundle-analyzer
```

### Tree Shaking: Import Only What You Need

```tsx
// Bad: Imports entire library (500KB)
import _ from 'lodash';
const doubled = _.map(arr, n => n * 2);

// Good: Imports only map function (5KB)
import map from 'lodash-es/map';
const doubled = map(arr, n => n * 2);

// Best: Use native alternatives
const doubled = arr.map(n => n * 2);
```

### Dynamic Imports for Third-Party Libraries

```tsx
// Bad: Bundles Chart.js upfront (200KB)
import { Chart } from 'chart.js';

// Good: Loads Chart.js only when needed
function ChartComponent({ data }: { data: ChartData }) {
  const [Chart, setChart] = useState<any>(null);

  useEffect(() => {
    import('chart.js').then((module) => {
      setChart(() => module.Chart);
    });
  }, []);

  if (!Chart) return <Skeleton />;

  return <Chart data={data} />;
}
```

### Remove Unused Dependencies

```bash
# Find unused dependencies
npx depcheck

# Remove unused packages
npm uninstall unused-package
```

## Pattern 9: Avoid Inline Object/Array Creation

### The Problem

```tsx
// Bad: New object on every render
function UserList() {
  return (
    <MemoizedComponent
      style={{ color: 'red' }} // New object
      options={['a', 'b', 'c']} // New array
    />
  );
}
```

Even though `MemoizedComponent` is memoized, it re-renders because props are new objects.

### The Fix

```tsx
// Good: Stable references
const STYLE = { color: 'red' };
const OPTIONS = ['a', 'b', 'c'];

function UserList() {
  return (
    <MemoizedComponent
      style={STYLE}
      options={OPTIONS}
    />
  );
}

// Or use useMemo for dynamic values
function UserList({ color }: { color: string }) {
  const style = useMemo(() => ({ color }), [color]);

  return <MemoizedComponent style={style} />;
}
```

## Pattern 10: Optimize Forms

### Controlled vs Uncontrolled Inputs

```tsx
// Bad: Re-renders entire form on every keystroke
function Form() {
  const [formData, setFormData] = useState({ name: '', email: '' });

  const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
    setFormData({ ...formData, [e.target.name]: e.target.value });
  };

  return (
    <form>
      <input name="name" value={formData.name} onChange={handleChange} />
      <input name="email" value={formData.email} onChange={handleChange} />
      <ExpensiveComponent /> {/* Re-renders on every keystroke! */}
    </form>
  );
}

// Good: Use form libraries (React Hook Form, Formik)
import { useForm } from 'react-hook-form';

function Form() {
  const { register, handleSubmit } = useForm();

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('name')} />
      <input {...register('email')} />
      <ExpensiveComponent /> {/* No unnecessary re-renders */}
    </form>
  );
}
```

## Profiling & Debugging

### React DevTools Profiler

1. Open React DevTools
2. Go to Profiler tab
3. Click Record
4. Interact with your app
5. Stop recording
6. Analyze:
   - Which components rendered?
   - How long did they take?
   - Why did they render?

### Chrome Performance Tab

1. Open Chrome DevTools > Performance
2. Click Record
3. Interact with your app
4. Stop recording
5. Analyze:
   - Long tasks (> 50ms)
   - Layout thrashing
   - JavaScript execution time

### Lighthouse

```bash
# Run Lighthouse audit
npm install -g lighthouse
lighthouse https://yoursite.com --view
```

Checks:
- Performance score
- First Contentful Paint
- Largest Contentful Paint
- Time to Interactive
- Total Blocking Time
- Cumulative Layout Shift

## Performance Budget

Set and enforce budgets:

```json
// performance-budget.json
{
  "budgets": [
    {
      "resourceSizes": [
        { "resourceType": "script", "budget": 300 },
        { "resourceType": "image", "budget": 500 },
        { "resourceType": "stylesheet", "budget": 50 }
      ],
      "resourceCounts": [
        { "resourceType": "third-party", "budget": 10 }
      ]
    }
  ]
}
```

## Quick Wins Checklist

- [ ] Enable production build (React.StrictMode off)
- [ ] Code split routes
- [ ] Lazy load below-fold components
- [ ] Add React.memo to expensive pure components
- [ ] Use useMemo for heavy calculations
- [ ] Use useCallback for memoized children callbacks
- [ ] Virtualize long lists (100+ items)
- [ ] Debounce search inputs
- [ ] Optimize images (WebP, lazy loading, dimensions)
- [ ] Remove console.logs in production
- [ ] Tree shake imports (lodash-es, not lodash)
- [ ] Remove unused dependencies
- [ ] Use CDN for static assets
- [ ] Enable gzip/brotli compression
- [ ] Implement proper caching headers

## Resources

- [React DevTools](https://react.dev/learn/react-developer-tools)
- [web.dev React Performance](https://web.dev/react)
- [React Profiler API](https://react.dev/reference/react/Profiler)
- [Bundle Analyzer](https://www.npmjs.com/package/webpack-bundle-analyzer)
- [React Window](https://react-window.vercel.app/)

---

When optimizing React apps, focus on the biggest bottlenecks first. Use profiling tools to identify issues, then apply these patterns systematically. Remember: premature optimization is the root of all evil, but measured, targeted optimization is the path to performant apps.