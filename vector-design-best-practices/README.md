# Vector Design Best Practices

Expert guidance for creating, optimizing, and implementing SVG graphics in modern web applications.

## What This Skill Does

This skill helps AI agents guide you through:

- **SVG Creation**: Choosing the right approach for your use case (hand-coding, design tools, or AI generation)
- **Optimization**: Reducing file sizes while maintaining visual quality
- **Accessibility**: Ensuring graphics work for all users, including screen reader users
- **Performance**: Implementing SVGs efficiently with proper loading strategies
- **Responsive Design**: Making graphics scale beautifully across devices
- **Integration**: Best practices for React, Next.js, and other modern frameworks

## When to Use This Skill

Use this skill when you need to:

- Create or optimize SVG icons, logos, or illustrations
- Implement accessible vector graphics
- Improve SVG performance in your application
- Choose between inline, external, or sprite-based SVG approaches
- Add animations to SVG elements
- Set up responsive SVG graphics
- Debug SVG rendering or scaling issues

## What You'll Learn

### Core Topics

1. **SVG Fundamentals**
   - ViewBox vs. width/height
   - Path optimization
   - Structure and organization

2. **Optimization Techniques**
   - SVGO configuration and usage
   - File size reduction strategies
   - Removing unnecessary metadata

3. **Accessibility**
   - Proper ARIA labeling
   - Color contrast requirements
   - Screen reader compatibility

4. **Performance**
   - File size budgets by use case
   - Inline vs. external SVG strategies
   - Lazy loading and code splitting

5. **Framework Integration**
   - React/SVGR patterns
   - Next.js optimization
   - Dynamic styling approaches

6. **Animation**
   - CSS animations for SVG
   - SMIL for complex sequences
   - JavaScript-based interactions

## Installation

```bash
npx skills add anaghkanungo7/agent-skills/vector-design-best-practices
```

## Example Usage

After installing, your AI coding assistant will have expert knowledge about SVG best practices:

**Example 1: Creating an optimized icon**
```
User: "I need a custom search icon as an SVG component"

Agent: "I'll create an optimized, accessible search icon component:
- Using viewBox for scalability
- currentColor for automatic theming
- Proper ARIA labels
- Optimized path data"
```

**Example 2: Optimizing existing SVGs**
```
User: "This logo.svg from Figma is 45KB, can we reduce it?"

Agent: "I'll optimize it by:
1. Running it through SVGO to remove metadata
2. Reducing decimal precision
3. Removing unnecessary groups
4. Converting to a streamlined component
Target: Under 5KB while maintaining visual quality"
```

**Example 3: Implementing responsive graphics**
```
User: "This illustration breaks on mobile"

Agent: "Let me implement responsive SVG patterns:
- Remove viewBox constraints
- Add proper CSS scaling
- Consider detail-level media queries
- Test across viewport sizes"
```

## Key Principles Covered

### 1. Optimization First
Every SVG should be optimized before production. This skill guides you through:
- SVGO configuration
- Manual optimization techniques
- File size budgets by use case

### 2. Accessibility is Non-Negotiable
Learn to implement:
- Proper ARIA attributes (`role`, `aria-labelledby`)
- Title and description elements
- Color contrast requirements (WCAG AA/AAA)
- Decorative vs. meaningful graphics

### 3. Performance Matters
Understand when to use:
- Inline SVG (critical, above-fold)
- External files (cached, reused)
- Sprite sheets (icon systems)
- Lazy loading strategies

### 4. Framework-Specific Best Practices
Get guidance for:
- React component patterns
- Next.js optimization
- SVGR configuration
- Dynamic styling approaches

## Common Problems Solved

- **Large file sizes**: Reduce SVG sizes by 60-80% through optimization
- **Poor accessibility**: Add proper ARIA labels and semantic structure
- **Scaling issues**: Fix viewBox problems and responsive behavior
- **Framework integration**: Implement SVGs correctly in React/Next.js
- **Animation performance**: Create smooth, 60fps animations
- **Cross-browser issues**: Ensure consistent rendering
- **Theming problems**: Set up dynamic color schemes

## Complementary Skills

This skill works well with:
- `seo-optimization-guide` - For optimizing SVGs in meta tags and social sharing
- `react-performance-patterns` - For optimizing SVG rendering in React apps
- Design system skills - For building consistent icon libraries

## Contributing

Found an issue or have a suggestion? Visit the [GitHub repository](https://github.com/anaghkanungo7/agent-skills) to contribute.

## License

MIT

---

**Created by**: Anagh Kanungo
**Version**: 1.0.0
**Last Updated**: 2026