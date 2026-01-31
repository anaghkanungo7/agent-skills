# Technical SEO Checklist

A comprehensive checklist for implementing and auditing technical SEO. Use this before launching new sites or when diagnosing SEO issues.

## Meta Tags & Headers

### Title Tags

- [ ] Every page has a unique title tag
- [ ] Titles are under 60 characters
- [ ] Most important keywords appear first
- [ ] Brand name included consistently
- [ ] No keyword stuffing
- [ ] Titles accurately describe page content

```html
<!-- Example -->
<title>React Performance Tips - Complete Guide | DevBlog</title>
```

### Meta Descriptions

- [ ] Every page has a unique meta description
- [ ] Descriptions are 150-160 characters
- [ ] Include target keyword naturally
- [ ] Actionable and compelling (encourages clicks)
- [ ] Accurately summarizes page content

```html
<meta name="description" content="Learn 12 proven React performance optimization techniques. Reduce load times by 60% with code splitting, memoization, and lazy loading." />
```

### Canonical Tags

- [ ] Canonical tag on every page
- [ ] Points to preferred URL version
- [ ] Absolute URLs (not relative)
- [ ] Consistent www/non-www preference
- [ ] Self-referencing on unique pages

```html
<link rel="canonical" href="https://example.com/blog/article" />
```

### Open Graph Tags

- [ ] og:title present (optimized for social, not same as title tag)
- [ ] og:description present (compelling social copy)
- [ ] og:image present (1200x630px recommended)
- [ ] og:url matches canonical URL
- [ ] og:type specified (article, product, website, etc.)
- [ ] og:site_name included
- [ ] Image < 300KB for fast loading

```html
<meta property="og:title" content="The Ultimate React Performance Guide" />
<meta property="og:description" content="Master React optimization with these battle-tested techniques." />
<meta property="og:image" content="https://example.com/og-images/react-guide.jpg" />
<meta property="og:url" content="https://example.com/blog/react-performance" />
<meta property="og:type" content="article" />
<meta property="og:site_name" content="DevBlog" />
```

### Twitter Cards

- [ ] twitter:card type specified
- [ ] twitter:title present
- [ ] twitter:description present
- [ ] twitter:image present
- [ ] twitter:site username included (if applicable)

```html
<meta name="twitter:card" content="summary_large_image" />
<meta name="twitter:title" content="The Ultimate React Performance Guide" />
<meta name="twitter:description" content="Master React optimization techniques" />
<meta name="twitter:image" content="https://example.com/twitter-images/react-guide.jpg" />
<meta name="twitter:site" content="@yourusername" />
```

### Other Important Meta Tags

- [ ] Viewport meta tag configured
- [ ] Language/locale specified
- [ ] Charset declared (UTF-8)
- [ ] Robots meta tag (if needed)

```html
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<meta name="language" content="English" />
<meta name="robots" content="index, follow" />
```

## Structured Data (Schema.org)

### General

- [ ] Structured data present on relevant pages
- [ ] Valid JSON-LD format
- [ ] No syntax errors
- [ ] Tested with Rich Results Test
- [ ] Appropriate schema type for content

### Article/Blog Post Pages

- [ ] Article schema implemented
- [ ] headline included
- [ ] author information
- [ ] datePublished present
- [ ] dateModified present (if updated)
- [ ] image specified
- [ ] publisher information with logo

```json
{
  "@context": "https://schema.org",
  "@type": "Article",
  "headline": "React Performance Optimization Guide",
  "author": {
    "@type": "Person",
    "name": "Jane Developer"
  },
  "datePublished": "2026-01-15",
  "dateModified": "2026-01-20",
  "image": "https://example.com/article-image.jpg",
  "publisher": {
    "@type": "Organization",
    "name": "DevBlog",
    "logo": {
      "@type": "ImageObject",
      "url": "https://example.com/logo.png"
    }
  }
}
```

### Product Pages (E-commerce)

- [ ] Product schema implemented
- [ ] name, image, description included
- [ ] brand information
- [ ] offers with price and availability
- [ ] aggregateRating (if applicable)
- [ ] review markup (if applicable)

### Organization/Website

- [ ] Organization schema on homepage
- [ ] Company logo specified
- [ ] Contact information
- [ ] Social media profiles linked

### Breadcrumbs

- [ ] BreadcrumbList schema implemented
- [ ] Matches visual breadcrumbs
- [ ] Proper hierarchy

### FAQ Pages

- [ ] FAQPage schema for FAQ content
- [ ] Each Question with acceptedAnswer
- [ ] Clear, concise answers

## URL Structure

- [ ] Clean, readable URLs
- [ ] Hyphens used (not underscores)
- [ ] Lowercase only
- [ ] No special characters
- [ ] Keywords in URL
- [ ] Short and descriptive
- [ ] Consistent trailing slash policy
- [ ] No URL parameters for content (use for filtering only)

```
✅ Good: https://example.com/blog/react-performance-tips
❌ Bad: https://example.com/index.php?p=123&cat=react
```

## HTTPS & Security

- [ ] Site served over HTTPS
- [ ] Valid SSL certificate
- [ ] No mixed content warnings
- [ ] HTTP redirects to HTTPS (301)
- [ ] HSTS header implemented
- [ ] Security headers configured (CSP, X-Frame-Options, etc.)

```nginx
# HTTPS redirect in nginx
server {
    listen 80;
    server_name example.com www.example.com;
    return 301 https://$host$request_uri;
}
```

## XML Sitemap

- [ ] XML sitemap exists
- [ ] Submitted to Google Search Console
- [ ] Submitted to Bing Webmaster Tools
- [ ] Listed in robots.txt
- [ ] Contains all important pages
- [ ] Excludes noindex pages
- [ ] lastmod dates accurate
- [ ] changefreq reasonable
- [ ] priority values set logically
- [ ] Under 50MB and 50,000 URLs (use sitemap index if needed)
- [ ] Valid XML format
- [ ] Gzipped for faster downloads (optional but recommended)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>https://example.com/</loc>
    <lastmod>2026-01-31</lastmod>
    <changefreq>daily</changefreq>
    <priority>1.0</priority>
  </url>
  <url>
    <loc>https://example.com/blog/react-tips</loc>
    <lastmod>2026-01-25</lastmod>
    <changefreq>monthly</changefreq>
    <priority>0.8</priority>
  </url>
</urlset>
```

## Robots.txt

- [ ] Robots.txt exists at root
- [ ] Allows crawling of important pages
- [ ] Blocks admin/private areas
- [ ] Sitemap URL listed
- [ ] No blocking of CSS/JS (outdated practice)
- [ ] Tested with robots.txt Tester in GSC

```txt
User-agent: *
Allow: /

Disallow: /admin/
Disallow: /api/
Disallow: /private/

Sitemap: https://example.com/sitemap.xml
```

## Page Speed & Core Web Vitals

### Largest Contentful Paint (LCP)

- [ ] LCP < 2.5 seconds
- [ ] Largest image/element optimized
- [ ] Critical resources preloaded
- [ ] Server response time < 600ms
- [ ] CDN configured

### Interaction to Next Paint (INP)

- [ ] INP < 200ms
- [ ] Heavy JavaScript deferred
- [ ] Long tasks broken up
- [ ] Event handlers optimized
- [ ] Unnecessary third-party scripts removed

### Cumulative Layout Shift (CLS)

- [ ] CLS < 0.1
- [ ] Image dimensions specified
- [ ] Font loading optimized (font-display: swap)
- [ ] No content inserted above existing content
- [ ] Ad/embed space reserved

### General Performance

- [ ] Lighthouse Performance score > 90
- [ ] First Contentful Paint < 1.8s
- [ ] Time to Interactive < 3.8s
- [ ] Total Blocking Time < 200ms
- [ ] Images optimized (WebP/AVIF)
- [ ] CSS minified
- [ ] JavaScript minified
- [ ] Gzip/Brotli compression enabled
- [ ] Browser caching configured
- [ ] Lazy loading for below-fold images

## Mobile Optimization

- [ ] Responsive design (not separate mobile site)
- [ ] Passes Mobile-Friendly Test
- [ ] Touch targets ≥ 48x48px
- [ ] Text readable without zooming (≥ 16px)
- [ ] No horizontal scrolling
- [ ] Mobile page speed < 3 seconds
- [ ] No Flash or unsupported plugins
- [ ] Properly sized images for mobile
- [ ] No intrusive interstitials

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
```

## Internal Linking

- [ ] Logical site hierarchy
- [ ] Orphan pages identified and linked
- [ ] Descriptive anchor text (no "click here")
- [ ] Links to important pages from homepage
- [ ] Related content linked within articles
- [ ] Broken links fixed
- [ ] Deep links to category/product pages
- [ ] Footer navigation to key pages

## Image Optimization

- [ ] Alt text on all images (descriptive, includes keywords)
- [ ] File names descriptive (react-chart.jpg not IMG_1234.jpg)
- [ ] Appropriate format (WebP for photos, SVG for icons/logos)
- [ ] Compressed without quality loss
- [ ] Responsive images (srcset/sizes)
- [ ] Lazy loading for below-fold images
- [ ] Width and height attributes specified
- [ ] No giant images scaled down with CSS

```html
<img
  src="/hero-800.webp"
  srcset="/hero-400.webp 400w, /hero-800.webp 800w, /hero-1200.webp 1200w"
  sizes="(max-width: 600px) 400px, 800px"
  alt="React performance dashboard showing 60% improvement"
  width="800"
  height="450"
  loading="lazy"
/>
```

## Content Quality

- [ ] Unique content on every page
- [ ] Content over 300 words (for blog posts/articles)
- [ ] Proper heading hierarchy (H1 → H2 → H3)
- [ ] Only one H1 per page
- [ ] Keywords used naturally (no stuffing)
- [ ] Content updated regularly
- [ ] External links to authoritative sources
- [ ] Spelling and grammar checked
- [ ] Content matches search intent

## Crawlability & Indexability

- [ ] Important pages indexable (no noindex)
- [ ] JavaScript content properly rendered
- [ ] Pagination implemented correctly (rel="next"/"prev" or View All page)
- [ ] Infinite scroll doesn't hide content
- [ ] Dynamic content crawlable
- [ ] AJAX content accessible
- [ ] Faceted navigation doesn't create duplicate content
- [ ] URL parameters handled correctly

### Google Search Console Checks

- [ ] No crawl errors
- [ ] No mobile usability issues
- [ ] Coverage report shows expected pages
- [ ] Core Web Vitals in "Good" range
- [ ] Manual actions clear
- [ ] Security issues clear

## Duplicate Content

- [ ] Canonical tags implemented
- [ ] URL parameters consolidated
- [ ] www vs non-www decided (301 redirect the other)
- [ ] Trailing slash consistency
- [ ] HTTP → HTTPS redirect
- [ ] Pagination canonicalized correctly
- [ ] Printer-friendly versions noindexed or canonicalized
- [ ] No scraped/syndicated content without canonical

## International/Multilingual SEO

If applicable:

- [ ] hreflang tags implemented
- [ ] Correct language/region codes
- [ ] All language versions linked
- [ ] URL structure for languages decided (subdomain, subdirectory, or parameter)
- [ ] Localized content (not just machine translated)

```html
<link rel="alternate" hreflang="en" href="https://example.com/en/" />
<link rel="alternate" hreflang="es" href="https://example.com/es/" />
<link rel="alternate" hreflang="x-default" href="https://example.com/" />
```

## Monitoring & Analytics

- [ ] Google Analytics 4 installed
- [ ] Google Search Console verified
- [ ] Bing Webmaster Tools verified
- [ ] Analytics tracking properly
- [ ] Event tracking configured
- [ ] Goals/conversions set up
- [ ] Regular performance monitoring
- [ ] Alerts configured for critical issues

## Accessibility (Impacts SEO)

- [ ] Proper semantic HTML
- [ ] ARIA labels where appropriate
- [ ] Color contrast sufficient (4.5:1 for text)
- [ ] Keyboard navigation works
- [ ] Screen reader friendly
- [ ] Alt text on images
- [ ] Captions for videos
- [ ] Transcripts for audio

## Local SEO (If Applicable)

- [ ] Google Business Profile claimed and optimized
- [ ] NAP (Name, Address, Phone) consistent across web
- [ ] LocalBusiness schema implemented
- [ ] Local keywords in content
- [ ] Location pages for multiple locations
- [ ] Reviews encouraged and responded to
- [ ] Local citations built

## Testing Tools

- [ ] **Google Search Console**: Coverage, Core Web Vitals, Mobile Usability
- [ ] **PageSpeed Insights**: Performance, LCP, INP, CLS
- [ ] **Lighthouse**: Comprehensive audit (SEO, Performance, Accessibility)
- [ ] **Mobile-Friendly Test**: Mobile compatibility
- [ ] **Rich Results Test**: Structured data validation
- [ ] **Schema Markup Validator**: JSON-LD validation
- [ ] **Screaming Frog**: Site crawl and audit
- [ ] **GTmetrix**: Performance analysis

## Pre-Launch Checklist

Before going live:

- [ ] All above items reviewed
- [ ] robots.txt set to allow crawling
- [ ] Temporary noindex tags removed
- [ ] 301 redirects from old URLs (if redesign)
- [ ] Sitemap submitted to search engines
- [ ] Google Analytics tracking verified
- [ ] Social sharing tested (OG tags work)
- [ ] Core Web Vitals in "Good" range
- [ ] Mobile version tested thoroughly
- [ ] HTTPS working everywhere
- [ ] No broken links
- [ ] Structured data validated

## Ongoing Maintenance

Weekly:
- [ ] Check Google Search Console for errors
- [ ] Monitor Core Web Vitals

Monthly:
- [ ] Review top-performing pages
- [ ] Fix new broken links
- [ ] Update old content
- [ ] Check mobile usability

Quarterly:
- [ ] Full site audit with Screaming Frog
- [ ] Competitor analysis
- [ ] Content gap analysis
- [ ] Backlink profile review
- [ ] Technical SEO review

---

**Pro Tip**: Save this as a GitHub issue template for SEO reviews:

```markdown
## Technical SEO Audit

- [ ] Meta tags unique and optimized
- [ ] Structured data validated
- [ ] Core Web Vitals passing
- [ ] Mobile-friendly
- [ ] Sitemap updated
- [ ] robots.txt configured
- [ ] HTTPS everywhere
- [ ] Images optimized
- [ ] No broken links
- [ ] GSC errors resolved
```