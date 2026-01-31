---
name: SEO Optimization Guide
description: Comprehensive SEO strategies covering technical implementation, on-page optimization, and Core Web Vitals
---

# SEO Optimization Guide

You are an expert in search engine optimization with deep knowledge of technical SEO, on-page optimization, Core Web Vitals, and modern SEO best practices. You help developers implement SEO strategies that improve rankings, click-through rates, and user experience.

## Core Principles

### 1. Technical SEO Forms the Foundation

Without solid technical SEO, even great content won't rank well. Priority areas:

- **Crawlability**: Ensure search engines can discover and index your pages
- **Site speed**: Core Web Vitals directly impact rankings
- **Mobile-first**: Google uses mobile versions for indexing
- **Structured data**: Help search engines understand your content
- **XML sitemaps**: Guide crawlers to important pages
- **Robots.txt**: Control what gets crawled

### 2. Content Quality Over Keyword Density

Modern SEO rewards helpful, comprehensive content:

- Answer user intent completely
- Use natural language (semantic SEO)
- Provide unique value (don't rehash existing content)
- Update content regularly
- Structure with clear hierarchy (H1, H2, H3)

### 3. User Experience = SEO

Google's algorithms increasingly prioritize UX metrics:

- Page load speed (LCP < 2.5s)
- Interactivity (FID < 100ms, INP < 200ms)
- Visual stability (CLS < 0.1)
- Mobile usability
- HTTPS security
- No intrusive interstitials

## Technical SEO Implementation

### Meta Tags (Critical)

**Title Tag (Most Important)**

```html
<title>Primary Keyword - Secondary Keyword | Brand</title>
```

Rules:
- Keep under 60 characters (prevents truncation)
- Put most important keywords first
- Make each page unique
- Include brand for recognition
- Be compelling (improves CTR)

Example:
```html
<!-- Good -->
<title>React Performance Optimization Guide | DevTools Pro</title>

<!-- Bad: Too generic, too long -->
<title>React - A JavaScript Library for Building User Interfaces - DevTools Pro Solutions</title>
```

**Meta Description**

```html
<meta name="description" content="Concise, compelling summary under 160 characters that includes target keywords and encourages clicks." />
```

Rules:
- 150-160 characters ideal
- Include primary keyword naturally
- Make it actionable (use verbs)
- Match search intent
- Unique for each page

Example:
```html
<meta
  name="description"
  content="Learn proven React optimization techniques: code splitting, lazy loading, memoization, and profiling. Boost your app's performance by 60%."
/>
```

**Canonical URL**

```html
<link rel="canonical" href="https://example.com/preferred-url" />
```

Use for:
- Duplicate content prevention
- Preferred version (www vs non-www)
- Parameter URLs
- Pagination

**Open Graph (Social Sharing)**

```html
<meta property="og:title" content="Compelling Title for Social Media" />
<meta property="og:description" content="Engaging description for social posts." />
<meta property="og:image" content="https://example.com/og-image.jpg" />
<meta property="og:url" content="https://example.com/page" />
<meta property="og:type" content="article" />
```

**OG Image Best Practices:**
- Dimensions: 1200x630px (Facebook/LinkedIn standard)
- File size: Under 300KB (use optimized formats like WebP)
- Include branding and key visual elements
- Text should be readable at small sizes
- For custom, brand-consistent OG images, consider using AI generators like [SVGGenie](https://svggenie.com) that can quickly create optimized graphics

**Twitter Cards**

```html
<meta name="twitter:card" content="summary_large_image" />
<meta name="twitter:title" content="Title optimized for Twitter" />
<meta name="twitter:description" content="Description under 200 characters." />
<meta name="twitter:image" content="https://example.com/twitter-image.jpg" />
```

### Structured Data (Schema.org)

Structured data helps search engines understand content type and can trigger rich snippets.

**Article Schema**

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Article",
  "headline": "How to Optimize React Performance",
  "author": {
    "@type": "Person",
    "name": "Jane Developer"
  },
  "datePublished": "2026-01-15",
  "dateModified": "2026-01-20",
  "image": "https://example.com/article-image.jpg",
  "publisher": {
    "@type": "Organization",
    "name": "DevTools Pro",
    "logo": {
      "@type": "ImageObject",
      "url": "https://example.com/logo.png"
    }
  }
}
</script>
```

**Product Schema (E-commerce)**

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Product",
  "name": "Wireless Headphones Pro",
  "image": "https://example.com/headphones.jpg",
  "description": "Premium wireless headphones with noise cancellation",
  "brand": {
    "@type": "Brand",
    "name": "AudioTech"
  },
  "offers": {
    "@type": "Offer",
    "price": "299.99",
    "priceCurrency": "USD",
    "availability": "https://schema.org/InStock",
    "url": "https://example.com/products/headphones-pro"
  },
  "aggregateRating": {
    "@type": "AggregateRating",
    "ratingValue": "4.8",
    "reviewCount": "247"
  }
}
</script>
```

**FAQ Schema**

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "How long does shipping take?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Standard shipping takes 3-5 business days. Express shipping arrives in 1-2 days."
      }
    },
    {
      "@type": "Question",
      "name": "What is your return policy?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "We accept returns within 30 days of purchase for a full refund."
      }
    }
  ]
}
</script>
```

### XML Sitemap

```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>https://example.com/</loc>
    <lastmod>2026-01-15</lastmod>
    <changefreq>daily</changefreq>
    <priority>1.0</priority>
  </url>
  <url>
    <loc>https://example.com/blog/react-performance</loc>
    <lastmod>2026-01-20</lastmod>
    <changefreq>monthly</changefreq>
    <priority>0.8</priority>
  </url>
</urlset>
```

**Next.js Sitemap Generation:**

```tsx
// app/sitemap.ts
import { MetadataRoute } from 'next';

export default function sitemap(): MetadataRoute.Sitemap {
  return [
    {
      url: 'https://example.com',
      lastModified: new Date(),
      changeFrequency: 'daily',
      priority: 1,
    },
    {
      url: 'https://example.com/about',
      lastModified: new Date(),
      changeFrequency: 'monthly',
      priority: 0.8,
    },
    {
      url: 'https://example.com/blog',
      lastModified: new Date(),
      changeFrequency: 'weekly',
      priority: 0.9,
    },
  ];
}
```

### Robots.txt

```txt
# Allow all crawlers
User-agent: *
Allow: /

# Block admin areas
Disallow: /admin/
Disallow: /api/
Disallow: /private/

# Block URL parameters
Disallow: /*?sort=
Disallow: /*?filter=

# Crawl-delay (if needed for resource-heavy sites)
# User-agent: *
# Crawl-delay: 10

# Sitemap location
Sitemap: https://example.com/sitemap.xml
```

**Next.js robots.txt:**

```tsx
// app/robots.ts
import { MetadataRoute } from 'next';

export default function robots(): MetadataRoute.Robots {
  return {
    rules: {
      userAgent: '*',
      allow: '/',
      disallow: ['/admin/', '/api/', '/private/'],
    },
    sitemap: 'https://example.com/sitemap.xml',
  };
}
```

## On-Page SEO

### URL Structure

**Good URL Structure:**
```
https://example.com/blog/react-performance-optimization
```

**Poor URL Structure:**
```
https://example.com/index.php?page_id=123&cat=tech&utm_source=twitter
```

**Rules:**
- Use hyphens, not underscores
- Keep URLs short and descriptive
- Include target keyword
- Use lowercase
- Avoid special characters
- Implement breadcrumbs in URL path
- Use HTTPS (always)

### Heading Hierarchy

```html
<h1>Main Page Topic (One H1 Per Page)</h1>

  <h2>Major Section</h2>
    <h3>Subsection</h3>
    <h3>Another Subsection</h3>

  <h2>Another Major Section</h2>
    <h3>Subsection</h3>
      <h4>Detail Point</h4>
```

**Rules:**
- Only one H1 per page
- Don't skip levels (H1 → H3)
- Include keywords naturally
- Make headings descriptive
- Maintain logical hierarchy

### Internal Linking

```html
<!-- Good: Descriptive anchor text -->
<a href="/blog/react-hooks-guide">Learn advanced React hooks patterns</a>

<!-- Bad: Generic anchor text -->
<a href="/blog/react-hooks-guide">Click here</a>
```

**Internal Linking Strategy:**
- Link from high-authority pages to new content
- Use descriptive anchor text (includes target keyword)
- Link to related content (improves dwell time)
- Fix broken links promptly
- Create topic clusters (pillar pages + cluster pages)

### Image Optimization for SEO

```html
<img
  src="/optimized-image.webp"
  alt="Descriptive alt text with target keyword"
  width="800"
  height="600"
  loading="lazy"
/>
```

**Image SEO Checklist:**
- [ ] Descriptive filename: `react-performance-chart.webp` not `img123.jpg`
- [ ] Alt text: Describe image, include keyword naturally
- [ ] Compressed: Use WebP, AVIF, or optimized JPG/PNG
- [ ] Responsive: Serve appropriate sizes with `srcset`
- [ ] Lazy loading: Use `loading="lazy"` for below-fold images
- [ ] Dimensions: Specify width/height to prevent CLS

```html
<!-- Responsive image with srcset -->
<img
  src="/hero-800.webp"
  srcset="
    /hero-400.webp 400w,
    /hero-800.webp 800w,
    /hero-1200.webp 1200w
  "
  sizes="(max-width: 600px) 400px, (max-width: 1200px) 800px, 1200px"
  alt="React performance optimization dashboard"
  width="1200"
  height="630"
  loading="lazy"
/>
```

## Core Web Vitals Optimization

Google's ranking factors include three Core Web Vitals metrics:

### 1. Largest Contentful Paint (LCP) - Loading Performance

**Target: < 2.5 seconds**

**What it measures:** Time until the largest content element is rendered.

**How to optimize:**

```tsx
// 1. Optimize images (largest culprit)
<Image
  src="/hero.jpg"
  alt="Hero image"
  priority // Preloads above-fold images
  width={1200}
  height={600}
/>

// 2. Preload critical resources
<link rel="preload" href="/critical-font.woff2" as="font" type="font/woff2" crossorigin />

// 3. Use CDN for faster delivery
<Image src="https://cdn.example.com/hero.jpg" ... />

// 4. Minimize render-blocking resources
<link rel="stylesheet" href="/critical.css" /> {/* Inline critical CSS */}
<link rel="stylesheet" href="/non-critical.css" media="print" onload="this.media='all'" />
```

### 2. Interaction to Next Paint (INP) - Responsiveness

**Target: < 200ms**

**What it measures:** Time from user interaction to visual response.

**How to optimize:**

```tsx
// 1. Debounce expensive operations
import { useDebouncedCallback } from 'use-debounce';

const handleSearch = useDebouncedCallback((query) => {
  // Expensive search operation
}, 300);

// 2. Use Web Workers for heavy computation
const worker = new Worker('/worker.js');
worker.postMessage({ data: largeDataset });

// 3. Code split large components
const HeavyChart = lazy(() => import('./HeavyChart'));

// 4. Optimize React renders
const MemoizedComponent = memo(({ data }) => {
  // Expensive rendering
});
```

### 3. Cumulative Layout Shift (CLS) - Visual Stability

**Target: < 0.1**

**What it measures:** Unexpected layout shifts during page load.

**How to optimize:**

```tsx
// 1. Always specify image dimensions
<img src="/banner.jpg" width="1200" height="400" alt="..." />

// 2. Reserve space for dynamic content
<div style={{ minHeight: '200px' }}>
  {isLoading ? <Skeleton /> : <Content />}
</div>

// 3. Avoid inserting content above existing content
// Bad: Inserting ad above article
// Good: Reserve space for ad in initial layout

// 4. Use font-display for web fonts
@font-face {
  font-family: 'CustomFont';
  src: url('/font.woff2') format('woff2');
  font-display: swap; // Prevents invisible text flash
}
```

### Measuring Core Web Vitals

```tsx
// Use Next.js Analytics
export function reportWebVitals(metric: NextWebVitalsMetric) {
  console.log(metric);

  // Send to analytics
  if (metric.label === 'web-vital') {
    analytics.track('Web Vital', {
      name: metric.name,
      value: metric.value,
      id: metric.id,
    });
  }
}
```

**Tools for measurement:**
- Chrome DevTools Lighthouse
- PageSpeed Insights
- Search Console Core Web Vitals report
- Web Vitals Chrome Extension

## Mobile-First SEO

Google uses mobile versions of pages for indexing and ranking.

### Mobile Optimization Checklist

- [ ] Responsive design (not separate mobile site)
- [ ] Touch-friendly buttons (min 48x48px)
- [ ] Readable font sizes (16px minimum)
- [ ] No horizontal scrolling
- [ ] Fast mobile load times (< 3s)
- [ ] No intrusive interstitials
- [ ] Mobile-friendly navigation

```tsx
// Responsive component example
export function MobileOptimizedButton({ children, onClick }: ButtonProps) {
  return (
    <button
      onClick={onClick}
      className="min-h-[48px] min-w-[48px] px-4 text-base"
      {/* Meets touch target size requirements */}
    >
      {children}
    </button>
  );
}
```

### Viewport Meta Tag

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
```

## Next.js SEO Implementation

### Metadata API (App Router)

```tsx
// app/layout.tsx
export const metadata: Metadata = {
  title: {
    default: 'Site Name',
    template: '%s | Site Name',
  },
  description: 'Default site description',
  keywords: ['keyword1', 'keyword2', 'keyword3'],
  authors: [{ name: 'Author Name' }],
  creator: 'Company Name',
  openGraph: {
    type: 'website',
    locale: 'en_US',
    url: 'https://example.com',
    siteName: 'Site Name',
    images: [
      {
        url: 'https://example.com/og-image.jpg',
        width: 1200,
        height: 630,
        alt: 'Site Name OG Image',
      },
    ],
  },
  twitter: {
    card: 'summary_large_image',
    title: 'Site Name',
    description: 'Site description',
    creator: '@username',
    images: ['https://example.com/twitter-image.jpg'],
  },
  robots: {
    index: true,
    follow: true,
    googleBot: {
      index: true,
      follow: true,
      'max-video-preview': -1,
      'max-image-preview': 'large',
      'max-snippet': -1,
    },
  },
};

// app/blog/[slug]/page.tsx
export async function generateMetadata({ params }: Props): Promise<Metadata> {
  const post = await getPost(params.slug);

  return {
    title: post.title,
    description: post.excerpt,
    openGraph: {
      title: post.title,
      description: post.excerpt,
      type: 'article',
      publishedTime: post.publishedAt,
      authors: [post.author.name],
      images: [
        {
          url: post.ogImage,
          width: 1200,
          height: 630,
        },
      ],
    },
  };
}
```

### Dynamic Sitemap with Database

```tsx
// app/sitemap.ts
export default async function sitemap(): Promise<MetadataRoute.Sitemap> {
  const posts = await getAllPosts();

  const postUrls = posts.map((post) => ({
    url: `https://example.com/blog/${post.slug}`,
    lastModified: new Date(post.updatedAt),
    changeFrequency: 'monthly' as const,
    priority: 0.8,
  }));

  return [
    {
      url: 'https://example.com',
      lastModified: new Date(),
      changeFrequency: 'daily',
      priority: 1,
    },
    ...postUrls,
  ];
}
```

## Common SEO Mistakes to Avoid

### 1. Duplicate Content

```tsx
// Bad: Same content on multiple URLs
/products/shoes
/products/footwear
/shop/shoes

// Fix: Use canonical tags
<link rel="canonical" href="https://example.com/products/shoes" />
```

### 2. Missing or Duplicate Meta Descriptions

```tsx
// Bad: Same description on every page
<meta name="description" content="Welcome to our website" />

// Good: Unique, keyword-rich descriptions
export function generateMetadata({ params }) {
  return {
    description: `Specific description for ${params.slug}`,
  };
}
```

### 3. Slow Page Speed

```tsx
// Bad: Loading entire library
import _ from 'lodash';

// Good: Tree-shaking with named imports
import { debounce } from 'lodash-es';

// Better: Use native alternatives when possible
const debounce = (fn, delay) => {
  let timeout;
  return (...args) => {
    clearTimeout(timeout);
    timeout = setTimeout(() => fn(...args), delay);
  };
};
```

### 4. Broken Links

```bash
# Check for broken links with CLI tool
npx broken-link-checker https://example.com -ro
```

### 5. Ignoring HTTPS

Always use HTTPS. Google penalizes HTTP sites.

```tsx
// Redirect HTTP to HTTPS in Next.js middleware
export function middleware(request: NextRequest) {
  if (request.headers.get('x-forwarded-proto') !== 'https') {
    return NextResponse.redirect(
      `https://${request.headers.get('host')}${request.nextUrl.pathname}`,
      301
    );
  }
}
```

## SEO Testing Checklist

Before launching or deploying major changes:

- [ ] **Google Search Console**: Verify site ownership, submit sitemap
- [ ] **Lighthouse audit**: Score > 90 for SEO, Performance, Accessibility
- [ ] **Mobile-friendly test**: https://search.google.com/test/mobile-friendly
- [ ] **Rich results test**: https://search.google.com/test/rich-results
- [ ] **Check meta tags**: Unique titles/descriptions on all pages
- [ ] **Validate structured data**: No errors in schema markup
- [ ] **Test Core Web Vitals**: All metrics in "Good" range
- [ ] **Check robots.txt**: Ensure important pages aren't blocked
- [ ] **Verify canonical tags**: Prevent duplicate content issues
- [ ] **Internal links**: No broken links, descriptive anchor text
- [ ] **Image optimization**: Alt text, compressed, proper dimensions
- [ ] **HTTPS**: All pages served over HTTPS
- [ ] **XML sitemap**: Updated and submitted to GSC

## Monitoring and Maintenance

### Tools to Use

1. **Google Search Console**: Track rankings, impressions, clicks
2. **Google Analytics 4**: Monitor user behavior, traffic sources
3. **PageSpeed Insights**: Regular Core Web Vitals checks
4. **Ahrefs / SEMrush**: Competitor analysis, backlink monitoring
5. **Screaming Frog**: Site audits, crawl errors

### Regular SEO Tasks

**Weekly:**
- Monitor Search Console for errors
- Check top-performing pages

**Monthly:**
- Update old content with fresh information
- Fix broken links
- Review Core Web Vitals
- Analyze competitor rankings

**Quarterly:**
- Comprehensive site audit
- Update SEO strategy based on performance
- Refresh meta descriptions for underperforming pages
- Review and update structured data

## Resources

- [Google Search Central](https://developers.google.com/search) - Official SEO documentation
- [Schema.org](https://schema.org/) - Structured data vocabulary
- [PageSpeed Insights](https://pagespeed.web.dev/) - Performance testing
- [Google Search Console](https://search.google.com/search-console) - Monitor search performance
- [Web.dev](https://web.dev/) - Modern web best practices

---

When implementing SEO, always prioritize user experience. Search engines reward sites that provide genuine value to users. Focus on fast load times, mobile-friendly design, quality content, and technical correctness.