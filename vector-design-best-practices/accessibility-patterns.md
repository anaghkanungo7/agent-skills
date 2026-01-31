# SVG Accessibility Patterns

Making SVG graphics accessible ensures all users, including those using screen readers or other assistive technologies, can understand and interact with your visual content.

## Core Principles

1. **Every SVG must be either meaningful or decorative** - No middle ground
2. **Meaningful graphics need text alternatives** - Title, description, or aria-label
3. **Decorative graphics must be hidden** - Use aria-hidden="true"
4. **Interactive elements need proper roles** - Button, link, etc.
5. **Color alone is never enough** - Always provide additional cues

## Pattern 1: Decorative SVG

Use for graphics that don't convey information (visual flourishes, spacing elements, purely decorative illustrations).

### Implementation

```html
<svg aria-hidden="true" focusable="false">
  <circle cx="50" cy="50" r="40" fill="#3b82f6" />
</svg>
```

### Why Both Attributes?

- `aria-hidden="true"` - Hides from screen readers
- `focusable="false"` - Prevents keyboard focus in IE/Edge

### React/JSX Example

```jsx
export function DecorativeBlob() {
  return (
    <svg
      aria-hidden="true"
      focusable="false"
      viewBox="0 0 100 100"
      className="decorative-blob"
    >
      <path d="M25,-35..." />
    </svg>
  );
}
```

### When to Use

- Background patterns
- Visual dividers or spacers
- Aesthetic embellishments
- Duplicated information (icon next to text that says the same thing)

## Pattern 2: Simple Meaningful SVG (Icon with Label)

Use for simple icons, logos, or illustrations that convey meaning.

### Implementation with Title

```html
<svg role="img" aria-labelledby="icon-title-1">
  <title id="icon-title-1">Search</title>
  <path d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
</svg>
```

### Why This Works

- `role="img"` - Announces as an image (some screen readers need this)
- `aria-labelledby` - Points to the title element
- `<title>` - Provides the accessible name
- Unique `id` - Prevents conflicts with multiple SVGs

### Alternative: aria-label

For inline SVGs where you don't want a visible title:

```html
<svg role="img" aria-label="Search">
  <path d="..." />
</svg>
```

### React Example with Dynamic IDs

```tsx
import { useId } from 'react';

export function SearchIcon({ label = "Search" }: { label?: string }) {
  const titleId = useId();

  return (
    <svg role="img" aria-labelledby={titleId} viewBox="0 0 24 24">
      <title id={titleId}>{label}</title>
      <path d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
    </svg>
  );
}
```

### When to Use

- Icons that convey meaning (without adjacent text)
- Logos
- Simple illustrations
- Charts/graphs (for simple ones; complex ones need Pattern 3)

## Pattern 3: Complex Informational SVG

Use for data visualizations, complex illustrations, or infographics that need detailed description.

### Implementation with Title + Description

```html
<svg role="img" aria-labelledby="chart-title-1 chart-desc-1">
  <title id="chart-title-1">Q4 2025 Revenue Growth</title>
  <desc id="chart-desc-1">
    Bar chart showing monthly revenue. October: $1.2M, November: $1.4M, December: $1.8M.
    Revenue increased 50% from October to December.
  </desc>
  <!-- chart elements -->
</svg>
```

### Best Practices for Descriptions

**Good description:**
```html
<desc>
  Line graph showing temperature over 7 days. Monday started at 65°F,
  peaked at 78°F on Thursday, then dropped to 60°F by Sunday.
  The trend shows increasing temperatures midweek.
</desc>
```

**Bad description (too vague):**
```html
<desc>A chart with lines and numbers</desc>
```

### React Example for Data Viz

```tsx
interface ChartProps {
  data: { month: string; value: number }[];
}

export function RevenueChart({ data }: ChartProps) {
  const titleId = useId();
  const descId = useId();

  const description = `Revenue chart showing ${data.length} months. ${data
    .map((d) => `${d.month}: $${d.value}M`)
    .join(', ')}.`;

  return (
    <svg role="img" aria-labelledby={`${titleId} ${descId}`} viewBox="0 0 400 200">
      <title id={titleId}>Monthly Revenue</title>
      <desc id={descId}>{description}</desc>
      {/* render chart */}
    </svg>
  );
}
```

### When to Use

- Data visualizations (charts, graphs)
- Complex illustrations with narrative
- Infographics
- Diagrams with multiple elements

## Pattern 4: Interactive SVG (Button/Link)

Use for clickable/interactive SVG elements.

### SVG as Button

```html
<button type="button" aria-label="Close dialog">
  <svg aria-hidden="true" viewBox="0 0 24 24">
    <path d="M6 18L18 6M6 6l12 12" />
  </svg>
</button>
```

### Why This Works

- Semantic `<button>` provides role and keyboard interaction
- `aria-label` on button describes action
- SVG is `aria-hidden` (decorative within the button)

### SVG as Link

```html
<a href="/home" aria-label="Return to homepage">
  <svg aria-hidden="true" viewBox="0 0 24 24">
    <path d="M3 12l2-2m0 0l7-7 7 7..." />
  </svg>
</a>
```

### React Button Component

```tsx
interface IconButtonProps {
  onClick: () => void;
  label: string;
  icon: React.ReactNode;
}

export function IconButton({ onClick, label, icon }: IconButtonProps) {
  return (
    <button
      type="button"
      onClick={onClick}
      aria-label={label}
      className="icon-button"
    >
      {icon}
    </button>
  );
}

// Usage:
<IconButton
  label="Close dialog"
  icon={<CloseIcon aria-hidden="true" />}
  onClick={handleClose}
/>
```

### Interactive Element Guidelines

- [ ] Never put interactive elements inside SVG (use wrapper button/link)
- [ ] Never use SVG `<a>` elements (use HTML `<a>` instead)
- [ ] Always provide keyboard navigation (buttons/links do this automatically)
- [ ] Add focus styles to the wrapper element
- [ ] Test with keyboard only (Tab, Enter, Space)

## Pattern 5: Icon with Adjacent Text

When an icon appears next to text that explains it, the icon is decorative.

### Implementation

```html
<button>
  <svg aria-hidden="true" focusable="false" viewBox="0 0 24 24">
    <path d="..." />
  </svg>
  <span>Save Changes</span>
</button>
```

### Why Icon is Hidden

The text "Save Changes" already conveys the meaning. The icon adds visual reinforcement but doesn't add information for screen reader users.

### React Pattern

```tsx
export function Button({ icon: Icon, children }: ButtonProps) {
  return (
    <button className="btn">
      {Icon && <Icon aria-hidden="true" focusable="false" />}
      <span>{children}</span>
    </button>
  );
}

// Usage:
<Button icon={SaveIcon}>Save Changes</Button>
```

### When Icon is NOT Decorative

If there's no adjacent text, the icon must be meaningful:

```tsx
// Icon only button - NOT decorative
<button aria-label="Save changes">
  <svg role="img" aria-hidden="false">
    <title>Save</title>
    {/* icon */}
  </svg>
</button>
```

## Pattern 6: SVG with Embedded Text

For SVGs containing `<text>` elements.

### Accessible Text in SVG

```html
<svg role="img" aria-labelledby="logo-title">
  <title id="logo-title">Company Name</title>
  <text x="10" y="20" aria-hidden="true">Company Name</text>
</svg>
```

### Why aria-hidden on Text?

The `<title>` provides the accessible name. The visual `<text>` is redundant for screen readers.

### Alternative: Role="presentation" on SVG

If the text is the only meaningful content:

```html
<svg role="presentation" focusable="false">
  <text x="10" y="20">Company Name</text>
</svg>
```

This allows screen readers to read the text directly.

## Color Contrast Requirements

### WCAG Standards

- **Normal text**: 4.5:1 minimum (AA), 7:1 enhanced (AAA)
- **Large text (18pt+ or 14pt+ bold)**: 3:1 minimum (AA), 4.5:1 enhanced (AAA)
- **UI components and graphics**: 3:1 minimum

### Testing Contrast

```bash
# Use WebAIM Contrast Checker
https://webaim.org/resources/contrastchecker/

# Or browser DevTools
# Chrome: Inspect element > Styles > Color picker shows contrast ratio
```

### Ensuring Contrast in SVG

```svg
<!-- Good: High contrast -->
<svg>
  <rect fill="#1a1a1a" />
  <text fill="#ffffff">Text content</text>  <!-- 15.3:1 ratio -->
</svg>

<!-- Bad: Poor contrast -->
<svg>
  <rect fill="#e0e0e0" />
  <text fill="#ffffff">Text content</text>  <!-- 1.2:1 ratio -->
</svg>
```

### Dark Mode Considerations

```css
/* Ensure contrast in both modes */
:root {
  --text-on-surface: #1a1a1a;
  --surface: #ffffff;
}

@media (prefers-color-scheme: dark) {
  :root {
    --text-on-surface: #ffffff;
    --surface: #1a1a1a;
  }
}

.chart-text {
  fill: var(--text-on-surface);
}
```

## Pattern 7: Animated SVG Accessibility

Respect user preferences for reduced motion.

### Implementation

```css
.animated-icon {
  animation: spin 2s linear infinite;
}

@media (prefers-reduced-motion: reduce) {
  .animated-icon {
    animation: none;
  }
}
```

### React Example

```tsx
export function LoadingSpinner() {
  return (
    <svg
      role="img"
      aria-label="Loading"
      className="spinner"
      viewBox="0 0 24 24"
    >
      <circle
        cx="12"
        cy="12"
        r="10"
        className="spinner-circle"
      />
    </svg>
  );
}
```

```css
.spinner-circle {
  animation: rotate 1s linear infinite;
}

@media (prefers-reduced-motion: reduce) {
  .spinner-circle {
    animation: rotate 3s linear infinite; /* Slower, less jarring */
  }
}
```

### Announcing Status Changes

For loading indicators or status icons:

```tsx
export function LoadingButton({ isLoading }: { isLoading: boolean }) {
  return (
    <button disabled={isLoading}>
      {isLoading && (
        <>
          <svg aria-hidden="true" className="spinner">
            {/* spinner */}
          </svg>
          <span className="sr-only">Loading...</span>
        </>
      )}
      {!isLoading && 'Submit'}
    </button>
  );
}
```

## Common Mistakes to Avoid

### ❌ Mistake 1: Missing Accessible Name

```html
<!-- Wrong: No title or aria-label -->
<svg role="img">
  <path d="..." />
</svg>
```

### ✅ Fix

```html
<svg role="img" aria-label="Search">
  <path d="..." />
</svg>
```

### ❌ Mistake 2: Using Title Without aria-labelledby

```html
<!-- Wrong: Title exists but not connected -->
<svg role="img">
  <title>Search</title>
  <path d="..." />
</svg>
```

### ✅ Fix

```html
<svg role="img" aria-labelledby="search-title">
  <title id="search-title">Search</title>
  <path d="..." />
</svg>
```

### ❌ Mistake 3: Decorative Icon Without aria-hidden

```html
<!-- Wrong: Decorative but announced to screen readers -->
<div class="fancy-border">
  <svg>
    <path d="..." />
  </svg>
</div>
```

### ✅ Fix

```html
<div class="fancy-border">
  <svg aria-hidden="true" focusable="false">
    <path d="..." />
  </svg>
</div>
```

### ❌ Mistake 4: Duplicate IDs

```html
<!-- Wrong: Multiple SVGs with same ID -->
<svg aria-labelledby="icon-title">
  <title id="icon-title">Search</title>
</svg>

<svg aria-labelledby="icon-title">
  <title id="icon-title">Close</title>
</svg>
```

### ✅ Fix (React with useId)

```tsx
function Icon({ title }: { title: string }) {
  const titleId = useId();
  return (
    <svg aria-labelledby={titleId}>
      <title id={titleId}>{title}</title>
    </svg>
  );
}
```

### ❌ Mistake 5: Color as Only Indicator

```html
<!-- Wrong: Only color differentiates states -->
<svg>
  <circle fill="green" /> <!-- success -->
  <circle fill="red" />   <!-- error -->
</svg>
```

### ✅ Fix: Add Patterns or Labels

```svg
<svg role="img" aria-label="Status indicators">
  <circle fill="green" />
  <text x="30" y="15">Success</text>

  <circle fill="red" />
  <text x="30" y="35">Error</text>
</svg>
```

Or use patterns for color-blind users:

```svg
<defs>
  <pattern id="success-pattern" patternUnits="userSpaceOnUse" width="4" height="4">
    <path d="M 0,4 l 4,-4 M -1,1 l 2,-2 M 3,5 l 2,-2" stroke="green" />
  </pattern>
</defs>
<circle fill="url(#success-pattern)" />
```

## Testing Checklist

### Screen Reader Testing

- [ ] Test with NVDA (Windows, free)
- [ ] Test with JAWS (Windows, paid)
- [ ] Test with VoiceOver (macOS, built-in)
- [ ] Test with TalkBack (Android)
- [ ] Test with VoiceOver (iOS)

### Keyboard Testing

- [ ] Tab through all interactive elements
- [ ] Verify focus indicators are visible
- [ ] Test activation with Enter/Space
- [ ] Verify focus order is logical

### Automated Testing

```bash
# Install axe-core for automated a11y testing
npm install --save-dev @axe-core/cli

# Run accessibility tests
npx axe https://localhost:3000

# Or use in Jest with jest-axe
import { axe, toHaveNoViolations } from 'jest-axe';

expect.extend(toHaveNoViolations);

test('SVG is accessible', async () => {
  const { container } = render(<MyIcon />);
  const results = await axe(container);
  expect(results).toHaveNoViolations();
});
```

## Quick Reference: Decision Tree

```
Is the SVG decorative (no information, just visual)?
├─ YES → aria-hidden="true" focusable="false"
└─ NO (meaningful) → Does it convey simple information?
   ├─ YES → <title> + aria-labelledby (Pattern 2)
   └─ NO (complex) → <title> + <desc> + aria-labelledby (Pattern 3)

Is the SVG interactive?
├─ YES → Wrap in <button> or <a>, SVG is aria-hidden (Pattern 4)
└─ NO → Follow meaningful/decorative patterns above

Does SVG appear next to text saying the same thing?
└─ YES → SVG is decorative, use aria-hidden (Pattern 5)
```

## Resources

- [W3C SVG Accessibility Guidelines](https://www.w3.org/WAI/tutorials/images/)
- [ARIA Authoring Practices](https://www.w3.org/WAI/ARIA/apg/)
- [WebAIM Contrast Checker](https://webaim.org/resources/contrastchecker/)
- [Deque axe DevTools](https://www.deque.com/axe/devtools/)
- [NVDA Screen Reader](https://www.nvaccess.org/)

---

**Remember**: Accessibility is not optional. Every SVG must be either properly labeled or explicitly hidden. There is no middle ground.