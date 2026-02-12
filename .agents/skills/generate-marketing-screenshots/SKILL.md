---
name: generate-marketing-screenshots
description: Automated Product Hunt screenshots and listing copy for deployed Next.js projects using Playwright MCP
---

# Generate Marketing Screenshots & Product Hunt Listing

You are an expert at creating polished marketing assets for product launches. You use Playwright MCP tools to capture pixel-perfect screenshots of deployed web applications and write compelling Product Hunt listing copy. You follow a strict, repeatable playbook that produces gallery-ready images and launch-day copy every time.

## Prerequisites

Before starting, verify:

1. **Playwright MCP server is connected** — you need access to `browser_navigate`, `browser_resize`, `browser_take_screenshot`, `browser_evaluate`, `browser_wait_for`, `browser_snapshot`, and `browser_close`.
2. **The site is deployed and publicly accessible** — screenshots of `localhost` won't reflect production performance, fonts, or CDN-delivered images. If the user provides a localhost URL, warn them that screenshots may not match production and ask if they want to proceed anyway.
3. **You know the project name and key pages** — ask the user if they haven't provided these.

## Step 0: Gather Information

Before touching the browser, collect:

| Info needed | How to get it |
|---|---|
| Product name | Ask the user |
| Public URL | Ask the user |
| Key pages / routes | Ask the user, or read `app/` directory structure if available |
| Whether auth is required | Ask — if pages are behind login, you'll need the user to provide credentials or a session cookie |
| Output directory | Default to `<project-root>/marketing/`, confirm with user |

If the user hasn't specified which pages to screenshot, propose a default set based on reading their route structure:

```
Homepage → /
Main feature → /dashboard or /feed or /app (whatever the primary logged-in view is)
Secondary feature → /analytics or /calendar or /search
Detail page → /items/[id] or /posts/[slug]
Pricing → /pricing
```

Ask the user to confirm or adjust before proceeding.

## Step 1: Create Output Directory

```bash
mkdir -p <project-root>/marketing
```

Use the project root, not a nested frontend directory, unless the user specifies otherwise.

## Step 2: Configure Browser Viewport

Navigate to the homepage and set the viewport to **1440x900** — this is the standard marketing screenshot size that looks good in the Product Hunt gallery carousel and on desktop.

```
browser_navigate → <site-url>
browser_resize → width: 1440, height: 900
```

**Why 1440x900:**
- Product Hunt gallery images display at roughly this aspect ratio
- It's the most common desktop resolution for marketing assets
- Screenshots won't get letterboxed or cropped in the PH carousel
- Looks crisp on retina displays when saved as PNG

After navigating, **wait 3-5 seconds** for the page to fully load. This is critical for Next.js applications where:
- Client components fetch data on mount via `useEffect`
- Fonts load asynchronously from Google Fonts or similar CDNs
- Images lazy-load and may pop in after initial render
- CSS-in-JS solutions (styled-components, Emotion) inject styles after hydration
- Animation libraries (Framer Motion, GSAP) run entrance animations

```
browser_wait_for → 4 seconds
```

Then take a **snapshot** (accessibility tree) to verify the page loaded correctly — look for actual content, not loading skeletons or spinners:

```
browser_snapshot
```

If you see loading indicators, skeleton screens, or empty content areas, wait additional time and re-check. If content still hasn't loaded after 10 seconds, inform the user — the page may require authentication or have a data dependency issue.

## Step 3: Capture Screenshots

### Screenshot Strategy

Capture **exactly 6 screenshots** (Product Hunt shows 5-6 in the gallery; more than that and people don't scroll):

| # | What to capture | Why it matters | Filename |
|---|---|---|---|
| 1 | **Homepage (above the fold)** | This becomes the PH thumbnail and OG image. It's the first thing people see. | `01-homepage.png` |
| 2 | **Main feature page** (dashboard, feed, or primary app view) | Shows the core value proposition — what users actually do in the product | `02-main-feature.png` |
| 3 | **Secondary feature** (analytics, calendar, search, settings) | Shows breadth — the product isn't a one-trick pony | `03-secondary-feature.png` |
| 4 | **Detail page** (individual item, post, or record view) | Shows depth — the product handles details well | `04-detail-view.png` |
| 5 | **Pricing page** | Required for PH — people always check pricing. If no pricing page exists, use another feature page or a "how it works" section | `05-pricing.png` |
| 6 | **Scrolled section** (scroll down on homepage or a feature page to show additional content) | Shows polish — the product has more to offer below the fold | `06-extended-view.png` |

### For Each Page

Follow this exact sequence:

```
1. browser_navigate → <page-url>
2. browser_wait_for → 3 seconds (let client-side data load)
3. browser_snapshot → (verify content loaded, no spinners/skeletons)
4. browser_take_screenshot → { filename: "<absolute-path>/marketing/0N-name.png", type: "png" }
```

**Critical rules:**
- **Always use `type: "png"`** — PNG is sharper than JPEG for UI screenshots. JPEG compression creates artifacts around text and sharp UI edges.
- **Always use absolute file paths** — relative paths may resolve to unexpected locations.
- **Screenshot the viewport, NOT `fullPage: true`** — full-page screenshots are too tall and get squished/cropped in the PH gallery. You want exactly what fits in 1440x900.
- **Homepage MUST be screenshot #1** — it becomes the thumbnail.

### For the Scrolled Screenshot (#6)

```
1. browser_navigate → <page-url> (or stay on current page)
2. browser_wait_for → 2 seconds
3. browser_evaluate → () => window.scrollTo({ top: 1200, behavior: 'instant' })
4. browser_wait_for → 1.5 seconds (let lazy-loaded content and images load after scroll)
5. browser_snapshot → (verify new content is visible)
6. browser_take_screenshot → { filename: "<absolute-path>/marketing/06-extended-view.png", type: "png" }
```

**Why `behavior: 'instant'` instead of `'smooth'`:** Smooth scrolling takes time and you might screenshot mid-animation. Instant scroll ensures the viewport is settled before the screenshot.

**Scroll distance guidance:**
- `1200px` works for most sites — it's roughly one full viewport height down
- If the page has very tall hero sections, you may need `1600-2000px`
- Use `browser_snapshot` after scrolling to verify you're seeing meaningful content, not whitespace

### Handling Edge Cases

**Pages behind authentication:**
- Ask the user to provide a URL with a session token, or to log in manually using the browser tools
- Alternatively, use `browser_navigate` to the login page, then `browser_type` to fill credentials if the user provides them
- Never hardcode or store credentials

**Pages with modals or popups:**
- Dismiss cookie banners, notification prompts, and chat widgets before screenshotting
- Use `browser_click` on dismiss/close buttons, or `browser_evaluate` to hide overlay elements:
  ```
  browser_evaluate → () => { document.querySelectorAll('[class*="cookie"], [class*="banner"], [class*="popup"], [class*="modal-overlay"]').forEach(el => el.remove()) }
  ```

**Dark mode vs light mode:**
- Default to light mode unless the user specifies otherwise — light mode screenshots are more readable in the PH gallery
- If the site has a theme toggle, ensure it's set to light before capturing

**Empty states:**
- Avoid screenshotting empty dashboards or feeds with no data. Ask the user if there's seed data or a demo account.

## Step 4: Write the Product Hunt Listing

After capturing all screenshots, create a `PRODUCT_HUNT.md` file in the marketing directory. Use the screenshots and your understanding of the product (from navigating it) to write compelling copy.

### Template

```markdown
# [Product Name] — Product Hunt Launch

## Tagline (60 characters max)
[One punchy line that explains what the product does. Focus on the outcome, not the technology.]

Guidelines:
- Lead with the benefit, not the feature
- Use active voice
- Avoid jargon
- Count characters carefully — PH truncates at 60

Examples of good taglines:
- "Track your habits with streaks that actually stick"
- "AI-powered code review that catches bugs before your users do"
- "Turn customer feedback into your next feature roadmap"

## Description (260 characters max)
[Elevator pitch: problem + solution + one compelling number or proof point.]

Guidelines:
- First sentence: the pain point
- Second sentence: what your product does about it
- Third sentence (optional): a key metric or social proof
- Count characters — PH truncates at 260

## Longer Description

### The problem
[2-3 sentences describing the pain point. Be specific — "developers waste 3 hours a week on X" is better than "X is hard".]

### What [Product Name] does
[2-3 sentences explaining the solution. Focus on what makes it different from alternatives. Mention the core workflow.]

### What you get
[Bullet list of 4-6 key features. Each bullet should have a **bold header** and a one-line description.]

- **[Feature 1]**: [One line explaining the value, not just what it does]
- **[Feature 2]**: [One line explaining the value]
- **[Feature 3]**: [One line explaining the value]
- **[Feature 4]**: [One line explaining the value]
- **[Feature 5]**: [One line explaining the value]

### Key numbers
[3-4 stats. These can be usage numbers, performance metrics, or comparative benchmarks.]

- [Number] [metric] — [context]
- [Number] [metric] — [context]
- [Number] [metric] — [context]

### Built with
[Tech stack — the PH audience loves knowing what's under the hood. List frameworks, languages, notable libraries, hosting, and AI models if applicable.]

## Topics / Categories
[3-5 Product Hunt categories. Choose from: Productivity, Developer Tools, Design Tools, AI, SaaS, Open Source, Marketing, Analytics, etc.]

## Maker Comment
[Write a casual, first-person comment from the maker's perspective. Include:]
- Why you built it (personal story or pain point)
- What's free vs. paid
- What you're working on next
- A direct ask for feedback

[Keep it conversational — no marketing speak. PH voters connect with authentic maker stories.]

## Screenshots
[Numbered list matching the filenames, with a one-line description of what each shows]

1. `01-homepage.png` — [Description: what the user sees, what it demonstrates]
2. `02-main-feature.png` — [Description]
3. `03-secondary-feature.png` — [Description]
4. `04-detail-view.png` — [Description]
5. `05-pricing.png` — [Description]
6. `06-extended-view.png` — [Description]

## Links
- **Website**: [URL]
- **Key pages**: [List 2-3 important direct links]
```

### Copy Quality Guidelines

When writing the listing copy:

1. **Be specific, not generic** — "Saves 2 hours per week on code review" beats "Makes code review faster"
2. **Lead with outcomes** — What does the user GET, not what the product DOES
3. **Use numbers** — Concrete metrics are more compelling than adjectives
4. **Keep it scannable** — Short paragraphs, bold headers, bullet points
5. **Match the product's voice** — If it's a developer tool, be technical. If it's consumer, be approachable.
6. **Don't oversell** — The PH audience is savvy and will call out hyperbole

## Step 5: Close the Browser

Always close the browser when done:

```
browser_close
```

This frees up system resources and prevents stale browser sessions from interfering with future runs.

## Step 6: Summary

After completing all steps, provide the user with:

1. **List of saved screenshots** with their absolute file paths
2. **Location of PRODUCT_HUNT.md**
3. **Quick review** — note any issues encountered (pages that didn't load, missing content, etc.)
4. **Suggested next steps**:
   - Review and tweak the copy in PRODUCT_HUNT.md
   - Crop or annotate screenshots if needed (e.g., add browser chrome mockups, captions)
   - Upload screenshots to Product Hunt (5-6 images, first image = thumbnail)
   - Schedule the launch for 12:01 AM PT on a Tuesday, Wednesday, or Thursday (highest traffic days)

## Common Mistakes to Avoid

| Mistake | Why it's bad | What to do instead |
|---|---|---|
| Screenshotting before data loads | You get loading skeletons in your PH gallery | Wait 3-5 seconds, verify with `browser_snapshot` |
| Using `fullPage: true` | Screenshots get squished in PH carousel | Use viewport-only screenshots at 1440x900 |
| Using JPEG format | Compression artifacts around text and UI edges | Always use PNG |
| Taking 10+ screenshots | People don't scroll past 5-6 in the gallery | Curate the best 6 |
| Generic tagline | Gets lost in the PH feed | Be specific about the outcome |
| Screenshotting in dark mode | Less readable in PH gallery, which has a white background | Default to light mode |
| Not dismissing popups | Cookie banners and modals ruin screenshots | Dismiss or remove overlays before capturing |
| Screenshotting localhost | Missing production fonts, images, CDN assets | Always use the deployed URL |
| Starting with a feature page | The first screenshot becomes the PH thumbnail | Always start with the homepage |

## Adapting for Non-Next.js Sites

While this playbook is optimized for Next.js, it works for any deployed web application. Adjust wait times based on the framework:

| Framework | Typical load behavior | Recommended wait time |
|---|---|---|
| **Next.js (App Router)** | Server-rendered shell, client hydration + data fetches | 3-5 seconds |
| **Next.js (Pages Router)** | `getServerSideProps` data on load, client hydration | 2-3 seconds |
| **Vite + React (SPA)** | Full client-side render + data fetches | 3-5 seconds |
| **Remix** | Server-rendered with loaders, minimal client work | 1-2 seconds |
| **Astro** | Mostly static, islands hydrate independently | 1-2 seconds |
| **Static sites (Hugo, Jekyll)** | Fully rendered on load | 1 second |

## Resources

- [Product Hunt Launch Guide](https://www.producthunt.com/launch)
- [Product Hunt Image Guidelines](https://www.producthunt.com/launch#media)
- [Best times to launch on Product Hunt](https://www.producthunt.com/launch#timing)
