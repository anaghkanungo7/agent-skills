# Common SVG Pitfalls and How to Avoid Them

This guide covers the most frequent SVG mistakes developers make and provides practical solutions. Learn from these patterns to save hours of debugging.

## 1. The Scaling Disaster

### The Problem

```html
<!-- This won't scale properly -->
<svg width="100" height="100">
  <circle cx="50" cy="50" r="40" />
</svg>
```

When you try to resize with CSS:
```css
.icon {
  width: 24px; /* Icon doesn't scale, gets cropped */
}
```

### Why It Happens

Fixed `width` and `height` attributes create a rigid bounding box. The SVG doesn't know how to scale its internal content.

### The Fix

```html
<!-- Always use viewBox -->
<svg viewBox="0 0 100 100" class="icon">
  <circle cx="50" cy="50" r="40" />
</svg>
```

```css
.icon {
  width: 24px;
  height: auto; /* Maintains aspect ratio */
}
```

### How viewBox Works

`viewBox="minX minY width height"` defines the coordinate system:
- `minX, minY`: Starting position (usually 0, 0)
- `width, height`: The internal coordinate space

Think of viewBox as a camera viewport. Regardless of the SVG's display size, viewBox defines what part of the coordinate space is visible.

### Real-World Example

```tsx
// Flexible icon component
export function Icon({ size = 24 }: { size?: number }) {
  return (
    <svg
      viewBox="0 0 24 24"
      width={size}
      height={size}
      fill="none"
      stroke="currentColor"
    >
      <path d="M12 2L2 7l10 5 10-5-10-5z" />
    </svg>
  );
}

// Usage: Works at any size
<Icon size={16} />
<Icon size={48} />
<Icon size={128} />
```

## 2. The Export Bloat

### The Problem

Exporting from Figma/Sketch produces this:

```svg
<svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
  <g clip-path="url(#clip0_1234_5678)">
    <g filter="url(#filter0_d_1234_5678)">
      <path d="M12.00000000000000 2.00000000000000 L12.00001000000000..." fill="#000000"/>
    </g>
  </g>
  <defs>
    <clipPath id="clip0_1234_5678">
      <rect width="24" height="24" fill="white"/>
    </clipPath>
    <filter id="filter0_d_1234_5678" x="-5" y="-5" width="34" height="34">
      <feFlood flood-opacity="0" result="BackgroundImageFix"/>
      <!-- 20 more lines of filter definitions -->
    </filter>
  </defs>
</svg>
```

**File size**: 8.2 KB

### Why It Happens

Design tools:
- Add metadata for editor features
- Export with excessive decimal precision
- Include unnecessary wrappers and definitions
- Don't optimize for production

### The Fix

Run through SVGO:

```bash
npx svgo icon.svg -o icon-optimized.svg
```

Result:

```svg
<svg viewBox="0 0 24 24" fill="none">
  <path d="M12 2L12..." />
</svg>
```

**File size**: 0.4 KB (95% reduction!)

### Prevention Strategy

1. **Set up SVGO in your build process:**
   ```json
   {
     "scripts": {
       "optimize-svg": "svgo -f ./src/icons -o ./src/icons"
     }
   }
   ```

2. **Pre-commit hook:**
   ```bash
   # .husky/pre-commit
   npx svgo -f ./public/icons
   git add ./public/icons
   ```

3. **Next.js plugin:**
   ```js
   // next.config.js
   module.exports = {
     webpack(config) {
       config.module.rules.push({
         test: /\.svg$/,
         use: ['@svgr/webpack', 'svgo-loader']
       });
       return config;
     }
   };
   ```

## 3. The Theming Nightmare

### The Problem

```svg
<!-- Hardcoded colors -->
<svg viewBox="0 0 24 24">
  <path fill="#3b82f6" stroke="#1e40af" d="..." />
  <circle fill="#ef4444" cx="12" cy="12" r="5" />
</svg>
```

You can't theme this without modifying the SVG file itself.

### Why It Happens

Design tools export with the exact colors from your design. Great for fidelity, terrible for flexibility.

### The Fix

**Option 1: Use currentColor** (inherits parent's text color)

```svg
<svg viewBox="0 0 24 24">
  <path fill="currentColor" stroke="currentColor" d="..." />
</svg>
```

```css
.icon { color: #3b82f6; }
.icon:hover { color: #1e40af; }

.dark-mode .icon { color: #60a5fa; }
```

**Option 2: CSS Custom Properties**

```svg
<svg viewBox="0 0 24 24" style="--primary: #3b82f6; --secondary: #ef4444;">
  <path fill="var(--primary)" d="..." />
  <circle fill="var(--secondary)" cx="12" cy="12" r="5" />
</svg>
```

```css
:root {
  --primary: #3b82f6;
  --secondary: #ef4444;
}

.dark-mode {
  --primary: #60a5fa;
  --secondary: #f87171;
}
```

**Option 3: React Props**

```tsx
interface IconProps {
  primaryColor?: string;
  secondaryColor?: string;
}

export function Icon({
  primaryColor = "currentColor",
  secondaryColor = "currentColor"
}: IconProps) {
  return (
    <svg viewBox="0 0 24 24">
      <path fill={primaryColor} d="..." />
      <circle fill={secondaryColor} cx="12" cy="12" r="5" />
    </svg>
  );
}
```

### Real-World Pattern

```tsx
// Flexible, themable icon system
export function Icon({
  name,
  size = 24,
  color = "currentColor",
  className = ""
}: IconProps) {
  const icons = {
    search: <path d="M21 21l-6-6..." />,
    close: <path d="M6 18L18 6..." />,
    // ... more icons
  };

  return (
    <svg
      viewBox="0 0 24 24"
      width={size}
      height={size}
      fill="none"
      stroke={color}
      className={className}
    >
      {icons[name]}
    </svg>
  );
}

// Usage
<Icon name="search" color="#3b82f6" />
<Icon name="search" className="text-blue-500" color="currentColor" />
```

## 4. The Accessibility Gap

### The Problem

```tsx
// Icon button with no accessible name
<button onClick={handleClose}>
  <svg viewBox="0 0 24 24">
    <path d="M6 18L18 6M6 6l12 12" />
  </svg>
</button>
```

Screen reader announces: "Button"

User thinks: "Button? What button? What does it do?"

### Why It Happens

Developers focus on visual implementation and forget that icons don't convey meaning to screen reader users.

### The Fix

**For icon-only buttons:**

```tsx
<button onClick={handleClose} aria-label="Close dialog">
  <svg aria-hidden="true" viewBox="0 0 24 24">
    <path d="M6 18L18 6M6 6l12 12" />
  </svg>
</button>
```

**For decorative icons (with text):**

```tsx
<button onClick={handleSave}>
  <svg aria-hidden="true" viewBox="0 0 24 24">
    <path d="..." />
  </svg>
  <span>Save Changes</span>
</button>
```

**For meaningful standalone graphics:**

```tsx
<svg role="img" aria-labelledby="chart-title">
  <title id="chart-title">Revenue Growth Q4 2025</title>
  {/* chart content */}
</svg>
```

### Quick Decision Tree

```
Is there adjacent text describing the icon?
├─ YES → aria-hidden="true" on SVG
└─ NO → aria-label on button/link, aria-hidden on SVG

Is the SVG decorative (purely visual)?
└─ YES → aria-hidden="true" focusable="false"

Is the SVG meaningful (conveys information)?
└─ YES → role="img" + <title> + aria-labelledby
```

## 5. The Performance Killer

### The Problem

```tsx
// Rendering 1000 complex SVGs on a page
{items.map(item => (
  <div key={item.id}>
    <svg viewBox="0 0 200 200">
      {/* 50+ path elements with complex gradients */}
      <defs>
        <linearGradient id="grad1">...</linearGradient>
        <filter id="shadow1">...</filter>
      </defs>
      <path d="M..." />
      {/* ... 50 more paths */}
    </svg>
  </div>
))}
```

Result: Page takes 4+ seconds to render, janky scrolling.

### Why It Happens

- Each SVG creates its own rendering context
- Complex paths are expensive to render
- Duplicate gradient/filter definitions waste memory
- Browser struggles with massive DOM trees

### The Fix

**Strategy 1: Use sprite sheets for repeated icons**

```tsx
// icons-sprite.svg (one file with all icons)
<svg xmlns="http://www.w3.org/2000/svg" style="display: none;">
  <defs>
    <symbol id="icon-search" viewBox="0 0 24 24">
      <path d="M21 21l-6-6..." />
    </symbol>
    <symbol id="icon-close" viewBox="0 0 24 24">
      <path d="M6 18L18 6..." />
    </symbol>
  </defs>
</svg>

// Component
export function Icon({ name }: { name: string }) {
  return (
    <svg width="24" height="24">
      <use href={`/icons-sprite.svg#icon-${name}`} />
    </svg>
  );
}
```

**Strategy 2: Virtualize large lists**

```tsx
import { FixedSizeList } from 'react-window';

<FixedSizeList
  height={600}
  itemCount={items.length}
  itemSize={50}
>
  {({ index, style }) => (
    <div style={style}>
      <Icon name={items[index].icon} />
      {items[index].label}
    </div>
  )}
</FixedSizeList>
```

**Strategy 3: Simplify complex SVGs**

```tsx
// Before: 50 paths, 8KB
<svg>
  <path d="M..." />
  <path d="M..." />
  {/* 48 more paths */}
</svg>

// After: Combined paths, 2KB
<svg>
  <path d="M...M...M..." /> {/* All paths combined */}
</svg>
```

**Strategy 4: Use CSS for simple shapes**

```css
/* Instead of SVG circle icon */
.dot {
  width: 10px;
  height: 10px;
  border-radius: 50%;
  background: #3b82f6;
}

/* Instead of SVG square */
.square {
  width: 10px;
  height: 10px;
  background: #3b82f6;
}
```

## 6. The Animation Jank

### The Problem

```css
/* Animating SVG attributes (janky) */
@keyframes move {
  from { cx: 0; }
  to { cx: 100; }
}

circle {
  animation: move 1s infinite;
}
```

Result: Choppy animation, especially on mobile.

### Why It Happens

Animating SVG attributes (cx, cy, r, etc.) triggers layout recalculations on every frame. Browser can't optimize these.

### The Fix

**Use CSS transforms (GPU-accelerated):**

```css
/* Good: Smooth 60fps animation */
@keyframes move {
  from { transform: translateX(0); }
  to { transform: translateX(100px); }
}

circle {
  animation: move 1s infinite;
  transform-origin: center;
}
```

**For SMIL animations, prefer transform:**

```svg
<!-- Avoid -->
<circle cx="50" cy="50" r="10">
  <animate attributeName="cx" from="0" to="100" dur="1s" />
</circle>

<!-- Better -->
<circle cx="50" cy="50" r="10">
  <animateTransform
    attributeName="transform"
    type="translate"
    from="0 0"
    to="100 0"
    dur="1s"
  />
</circle>
```

**For JavaScript, use requestAnimationFrame:**

```tsx
// Bad: Using setInterval
setInterval(() => {
  element.setAttribute('cx', x);
  x += 1;
}, 16);

// Good: Using requestAnimationFrame
function animate() {
  element.style.transform = `translateX(${x}px)`;
  x += 1;
  requestAnimationFrame(animate);
}
requestAnimationFrame(animate);
```

### Performance Checklist

- [ ] Use CSS transforms, not attribute changes
- [ ] Add `will-change: transform` for frequently animated elements
- [ ] Remove `will-change` after animation completes
- [ ] Test on low-end devices (throttle CPU in DevTools)
- [ ] Respect `prefers-reduced-motion`

```css
.animated {
  will-change: transform;
  animation: spin 1s linear infinite;
}

.animated.finished {
  will-change: auto; /* Important: remove after animation */
}

@media (prefers-reduced-motion: reduce) {
  .animated {
    animation: none;
  }
}
```

## 7. The IE11 / Old Browser Trap

### The Problem

```tsx
// Works in modern browsers, breaks in IE11/old Safari
<svg>
  <circle style="fill: var(--color);" />
</svg>
```

### Why It Happens

CSS custom properties in SVG aren't supported in IE11 and older Safari versions.

### The Fix (if you need old browser support)

**Option 1: Inline styles with fallbacks**

```tsx
<svg>
  <circle style={`fill: ${color}; fill: var(--color, ${color});`} />
</svg>
```

**Option 2: Use props instead of CSS variables**

```tsx
export function Icon({ color = "#000" }: { color?: string }) {
  return (
    <svg viewBox="0 0 24 24">
      <circle fill={color} />
    </svg>
  );
}
```

**Option 3: PostCSS plugin to transpile**

```bash
npm install postcss-custom-properties
```

```js
// postcss.config.js
module.exports = {
  plugins: {
    'postcss-custom-properties': {
      preserve: true, // Keep for modern browsers
      fallback: true  // Add fallbacks for old browsers
    }
  }
}
```

## 8. The CORS Issue

### The Problem

```html
<!-- External SVG fails to load or show incorrect colors -->
<img src="https://cdn.example.com/icon.svg" />
```

Browser console: "Cross-Origin Request Blocked"

### Why It Happens

Browsers restrict cross-origin resource loading for security. SVGs loaded via `<img>` can't execute scripts or access external resources.

### The Fix

**Option 1: Serve SVGs from same origin**

```html
<img src="/icons/icon.svg" />
```

**Option 2: Configure CORS headers on CDN**

```
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: GET
```

**Option 3: Inline the SVG**

```tsx
import IconSVG from './icon.svg';

export function Icon() {
  return <IconSVG />; // Inlined via SVGR
}
```

**Option 4: Use base64 data URI (small SVGs only)**

```tsx
const iconDataURI = `data:image/svg+xml;base64,${btoa(svgString)}`;
<img src={iconDataURI} alt="Icon" />
```

## 9. The Mobile Safari Gotcha

### The Problem

```css
/* SVG looks fine on desktop, blurry on iPhone */
.icon {
  width: 24px;
  height: 24px;
}
```

### Why It Happens

Mobile Safari renders at 2x or 3x pixel density. Fixed pixel sizes can look blurry.

### The Fix

**Use relative units or let SVG scale:**

```css
.icon {
  width: 1.5rem; /* Scales with text size */
  height: auto;
}
```

**Or set explicit sizes for retina:**

```tsx
export function Icon({ size = 24 }: { size?: number }) {
  // Double size for retina, browser scales down for crisp rendering
  const retinaSize = size * 2;

  return (
    <svg
      viewBox="0 0 24 24"
      width={size}
      height={size}
      style={{ display: 'block' }} // Prevents baseline gap
    >
      <path d="..." />
    </svg>
  );
}
```

## 10. The Webpack/Bundler Confusion

### The Problem

```tsx
// Import SVG, but it doesn't work
import logo from './logo.svg';

// Results in: <img src="[object Object]" />
<img src={logo} alt="Logo" />
```

### Why It Happens

Different bundlers handle SVG imports differently:
- Create React App: SVG becomes a URL string
- Next.js (without config): URL string
- Next.js (with SVGR): React component
- Vite: URL string (unless configured)

### The Fix

**Check what your bundler returns:**

```tsx
import logo from './logo.svg';
console.log(typeof logo); // 'string' or 'object'?
```

**For URL string (default):**

```tsx
import logoUrl from './logo.svg';
<img src={logoUrl} alt="Logo" />
```

**For React component (SVGR):**

```tsx
import Logo from './logo.svg';
<Logo />
```

**Configure SVGR in Next.js:**

```js
// next.config.js
module.exports = {
  webpack(config) {
    config.module.rules.push({
      test: /\.svg$/,
      use: ['@svgr/webpack']
    });
    return config;
  }
};
```

**Both URL and component (Vite):**

```tsx
import logoUrl from './logo.svg?url';
import Logo from './logo.svg?react';

<img src={logoUrl} />  // As image
<Logo />               // As component
```

## Prevention Checklist

Before you ship SVGs, verify:

- [ ] Uses `viewBox` for scaling
- [ ] Optimized with SVGO (or similar)
- [ ] Colors are themable (`currentColor` or CSS variables)
- [ ] Accessible (`aria-label` or `aria-hidden` as appropriate)
- [ ] File size is reasonable for use case
- [ ] Tested on mobile (especially Safari)
- [ ] Animations use CSS transforms, not attribute changes
- [ ] Respects `prefers-reduced-motion`
- [ ] Works in target browsers (check caniuse.com)
- [ ] CORS configured if loading from CDN

## Quick Debugging Commands

```bash
# Check SVG file size
ls -lh icon.svg

# Validate SVG syntax
xmllint --noout icon.svg

# Optimize and see size difference
npx svgo icon.svg -o icon-opt.svg && ls -lh icon*.svg

# View SVG in browser for debugging
open icon.svg  # macOS
start icon.svg # Windows
xdg-open icon.svg # Linux
```

## Resources

- [SVG2 Spec](https://www.w3.org/TR/SVG2/) - Official specification
- [SVGO](https://github.com/svg/svgo) - Optimization tool
- [Can I Use SVG Features](https://caniuse.com/?search=svg) - Browser compatibility
- [MDN SVG Reference](https://developer.mozilla.org/en-US/docs/Web/SVG) - Comprehensive docs

---

**Pro tip**: Create a project-specific SVG checklist and add it to your PR template. Catching these issues before code review saves everyone time.