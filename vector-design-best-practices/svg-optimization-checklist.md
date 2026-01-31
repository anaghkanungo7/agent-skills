# SVG Optimization Checklist

Use this checklist before shipping any SVG to production. Each optimization can significantly impact file size and rendering performance.

## Pre-Export Optimization (Design Tools)

- [ ] **Flatten unnecessary groups** - Merge layers that don't need separate manipulation
- [ ] **Outline strokes** - Convert strokes to fills when possible (reduces render complexity)
- [ ] **Combine overlapping paths** - Use boolean operations (union, subtract, intersect)
- [ ] **Simplify complex paths** - Use "Simplify" or "Smooth" tools to reduce anchor points
- [ ] **Remove invisible elements** - Delete hidden layers, zero-opacity objects
- [ ] **Use symbols/components** - For repeated elements (reduces code duplication)
- [ ] **Organize artboard size** - Trim empty space, set proper bounds
- [ ] **Name layers meaningfully** - For elements you'll target with CSS/JS

## File-Level Optimization

### Automated (SVGO)

Run through SVGO with this config:

```bash
npx svgo input.svg -o output.svg --config='{
  "plugins": [
    "removeDoctype",
    "removeXMLProcInst",
    "removeComments",
    "removeMetadata",
    "removeEditorsNSData",
    "cleanupAttrs",
    "mergeStyles",
    "inlineStyles",
    "minifyStyles",
    "cleanupIds",
    "removeUselessDefs",
    "cleanupNumericValues",
    "convertColors",
    "removeUnknownsAndDefaults",
    "removeNonInheritableGroupAttrs",
    "removeUselessStrokeAndFill",
    "removeViewBox",
    "cleanupEnableBackground",
    "removeHiddenElems",
    "removeEmptyText",
    "convertShapeToPath",
    "convertEllipseToCircle",
    "moveElemsAttrsToGroup",
    "moveGroupAttrsToElems",
    "collapseGroups",
    "convertPathData",
    "convertTransform",
    "removeEmptyAttrs",
    "removeEmptyContainers",
    "mergePaths",
    "removeUnusedNS",
    "sortDefsChildren",
    "removeTitle",
    "removeDesc"
  ]
}'
```

**Check for:**
- [ ] Metadata removed (Adobe, Sketch, Figma namespaces)
- [ ] Comments stripped
- [ ] Decimal precision reduced (2-3 digits is usually enough)
- [ ] Unnecessary groups collapsed
- [ ] Redundant attributes removed

### Manual Review

- [ ] **Remove xmlns attributes if inline** - Not needed for inline SVGs
  ```svg
  <!-- Remove this for inline SVGs -->
  xmlns="http://www.w3.org/2000/svg"
  ```

- [ ] **Check decimal precision** - Rarely need more than 2-3 decimal places
  ```svg
  <!-- Before: -->
  <path d="M12.345678,23.456789 L45.678912..." />

  <!-- After: -->
  <path d="M12.35,23.46 L45.68..." />
  ```

- [ ] **Simplify colors** - Use 3-digit hex when possible
  ```svg
  <!-- Before: -->
  fill="#3333ff"

  <!-- After: -->
  fill="#33f"
  ```

- [ ] **Remove default values** - SVG spec defines defaults
  ```svg
  <!-- Remove these if they're defaults: -->
  fill="black"           <!-- default is black -->
  fill-rule="nonzero"    <!-- default -->
  stroke="none"          <!-- default -->
  ```

- [ ] **Use shorthand attributes** - Where supported
  ```svg
  <!-- Before: -->
  <circle cx="50" cy="50" r="10" />

  <!-- Consider if path shorthand is smaller: -->
  <circle cx="50" cy="50" r="10" />
  ```

## Accessibility Optimization

- [ ] **Add title for meaningful graphics**
  ```svg
  <svg role="img" aria-labelledby="unique-title-id">
    <title id="unique-title-id">Descriptive title</title>
    <!-- content -->
  </svg>
  ```

- [ ] **Add description for complex graphics**
  ```svg
  <svg role="img" aria-labelledby="title-id desc-id">
    <title id="title-id">Chart showing Q4 revenue</title>
    <desc id="desc-id">Revenue increased from $1M to $1.5M between Oct and Dec</desc>
    <!-- content -->
  </svg>
  ```

- [ ] **Mark decorative graphics as hidden**
  ```svg
  <svg aria-hidden="true" focusable="false">
  ```

- [ ] **Ensure text contrast** - Minimum 4.5:1 for normal text, 3:1 for large text

- [ ] **Don't rely on color alone** - Use patterns, labels, or shapes for information

## Performance Optimization

### File Size Budgets

- [ ] **Icons**: Under 2KB
- [ ] **Logos**: Under 5KB
- [ ] **Illustrations**: Under 20KB
- [ ] **Complex visualizations**: Under 50KB (or consider code-splitting)

### Implementation Strategy

- [ ] **Inline for critical path** - Above-fold, essential graphics
  ```jsx
  <svg viewBox="0 0 24 24" className="icon">...</svg>
  ```

- [ ] **External for reused graphics** - Better caching
  ```html
  <img src="/logo.svg" alt="Logo" />
  ```

- [ ] **Sprite sheets for icon systems** - Reduces HTTP requests
  ```html
  <svg><use href="/sprites.svg#icon-name" /></svg>
  ```

- [ ] **Lazy load below-fold graphics** - Improves initial load
  ```jsx
  <img src="/illustration.svg" loading="lazy" alt="..." />
  ```

## Responsive Design

- [ ] **Use viewBox, not fixed dimensions**
  ```svg
  <!-- Good: -->
  <svg viewBox="0 0 100 100">

  <!-- Avoid: -->
  <svg width="100" height="100">
  ```

- [ ] **Set max constraints in CSS**
  ```css
  .icon {
    width: 100%;
    height: auto;
    max-width: 24px;
  }
  ```

- [ ] **Test at different sizes** - Ensure legibility at min/max sizes

- [ ] **Consider detail levels** - Hide complex details on small screens
  ```svg
  <svg viewBox="0 0 100 100">
    <g class="detail-high"><!-- fine details --></g>
    <g class="detail-low"><!-- simplified --></g>
  </svg>
  ```

## Framework Integration

### React/Next.js

- [ ] **Use SVGR for component conversion** - Or inline directly
- [ ] **Implement proper TypeScript types**
  ```tsx
  interface IconProps {
    size?: number;
    color?: string;
    className?: string;
  }
  ```

- [ ] **Accept standard props** - className, style, aria-label
- [ ] **Use currentColor for theming** - Inherits text color
  ```svg
  <path fill="currentColor" />
  ```

## Testing & Validation

### Visual Testing

- [ ] **Renders correctly in target browsers** - Chrome, Safari, Firefox, Edge
- [ ] **Scales properly** - Test at 0.5x, 1x, 2x, 4x sizes
- [ ] **Dark mode compatible** - If applicable
- [ ] **High contrast mode** - For accessibility

### Technical Testing

- [ ] **No console errors** - Check browser console
- [ ] **Valid SVG markup** - Use W3C validator
- [ ] **File size within budget** - Check actual file size
- [ ] **Compression effective** - Test with gzip/brotli

### Performance Testing

- [ ] **Render performance** - Use Chrome DevTools Performance tab
  - [ ] No layout thrashing
  - [ ] No forced synchronous layouts
  - [ ] Smooth 60fps animations

- [ ] **Memory usage** - Check for memory leaks in animations
- [ ] **Load time** - Measure impact on page load

## Animation-Specific Checks

If SVG includes animations:

- [ ] **Use CSS over JavaScript** - When possible (better performance)
- [ ] **Use transform over position changes** - GPU-accelerated
- [ ] **Add will-change hint** - For frequently animated properties
  ```css
  .animated-element {
    will-change: transform;
  }
  ```

- [ ] **Remove will-change after animation** - Prevents memory issues
- [ ] **Test on low-end devices** - Ensure 60fps or graceful degradation
- [ ] **Provide reduced-motion alternative**
  ```css
  @media (prefers-reduced-motion: reduce) {
    .animated { animation: none; }
  }
  ```

## Final Checks

- [ ] **Backup original file** - Keep unoptimized version for future edits
- [ ] **Version control** - Commit both source and optimized versions
- [ ] **Document special requirements** - If manually maintaining parts
- [ ] **Update component props** - If icon signature changed
- [ ] **Test in production build** - Ensure bundler doesn't break optimization

## File Size Wins - Expected Reductions

After following this checklist, expect these typical reductions:

- **Figma/Sketch exports**: 40-60% reduction
- **Adobe Illustrator**: 50-70% reduction
- **Hand-coded SVGs**: 20-30% reduction
- **Already-optimized SVGs**: 5-15% additional reduction

## Quick Reference: SVGO CLI

```bash
# Basic optimization
npx svgo input.svg

# Custom output path
npx svgo input.svg -o output.svg

# Process entire folder
npx svgo -f ./input-folder -o ./output-folder

# Disable specific plugins
npx svgo input.svg --disable=removeViewBox,removeDimensions

# Pretty print (for debugging)
npx svgo input.svg --pretty
```

## When Optimization Goes Wrong

If optimized SVG looks different:

1. **Compare visually** - Use diff tool or overlay comparison
2. **Check specific plugins** - Disable suspect plugins one by one
3. **Adjust precision** - Increase decimal places if needed
4. **Preserve viewBox** - Disable removeViewBox plugin
5. **Keep transforms** - Disable convertTransform if layout breaks

## Tools & Resources

- **SVGO**: https://github.com/svg/svgo
- **SVGOMG**: https://jakearchibald.github.io/svgomg/ (browser UI for SVGO)
- **SVG Optimizer**: https://petercollingridge.appspot.com/svg-optimiser
- **W3C Validator**: https://validator.w3.org/
- **Contrast Checker**: https://webaim.org/resources/contrastchecker/

---

**Pro tip**: Create a npm script for consistent optimization across your project:

```json
{
  "scripts": {
    "optimize-svg": "svgo -f ./public/icons -o ./public/icons --config=svgo.config.js"
  }
}
```