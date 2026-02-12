# Generate Marketing Screenshots

Automated Product Hunt screenshots and listing copy for deployed web applications using Playwright MCP. Takes pixel-perfect screenshots of your key pages and generates launch-ready Product Hunt copy — all from a single command.

## What This Skill Does

This skill turns your deployed site into a complete Product Hunt launch kit:

- **Captures 6 gallery-ready screenshots** at 1440x900 (PH-optimized viewport)
- **Handles Next.js loading quirks** — waits for client hydration, data fetches, and font loading
- **Dismisses overlays** — cookie banners, chat widgets, and modals won't ruin your shots
- **Writes your PH listing** — tagline, description, maker comment, and feature bullets
- **Follows PH best practices** — correct image count, format, order, and character limits

## When to Use This Skill

Use this skill when you need to:

- Prepare marketing screenshots for a Product Hunt launch
- Generate gallery images for any product listing (PH, Indie Hackers, HackerNews Show, etc.)
- Create a structured Product Hunt listing with all required sections
- Capture consistent, high-quality screenshots of a deployed web app
- Build a marketing asset folder for your project

## Prerequisites

1. **Playwright MCP server connected** — the skill uses browser automation tools
2. **Site deployed to a public URL** — localhost screenshots won't have production fonts/images
3. **Key pages accessible** — if pages require auth, have credentials ready

## Installation

```bash
npx skills add anaghkanungo7/agent-skills/generate-marketing-screens
```

## Example Usage

After installing, ask your AI coding assistant:

```
"Take Product Hunt screenshots of my app at https://myapp.com"
```

```
"Generate marketing screenshots and a PH listing for https://myapp.com"
```

```
"I'm launching on Product Hunt — capture screenshots of my homepage, dashboard,
pricing page, and write the listing copy"
```

The skill will:
1. Ask which pages to capture (or propose defaults from your route structure)
2. Navigate to each page, wait for content to load, and take PNG screenshots
3. Handle scrolled views, dismiss popups, and verify content loaded
4. Write a complete `PRODUCT_HUNT.md` with tagline, description, and maker comment
5. Save everything to a `marketing/` directory

## What You Get

```
marketing/
├── 01-homepage.png           # Hero shot (becomes PH thumbnail)
├── 02-main-feature.png       # Core value prop
├── 03-secondary-feature.png  # Shows breadth
├── 04-detail-view.png        # Shows depth
├── 05-pricing.png            # Required for PH
├── 06-extended-view.png      # Below-the-fold polish
└── PRODUCT_HUNT.md           # Complete PH listing copy
```

## Screenshot Specifications

| Setting | Value | Why |
|---|---|---|
| Viewport | 1440x900 | PH gallery standard, no cropping |
| Format | PNG | Sharper than JPEG for UI screenshots |
| Count | 6 max | PH shows 5-6, more gets ignored |
| Mode | Viewport only (not full page) | Full page gets squished in carousel |
| First image | Always homepage | Becomes PH thumbnail/OG image |

## Framework Support

Works with any deployed web application. Wait times adjust automatically:

| Framework | Wait time |
|---|---|
| Next.js (App Router) | 3-5 seconds |
| Next.js (Pages Router) | 2-3 seconds |
| Vite + React SPA | 3-5 seconds |
| Remix | 1-2 seconds |
| Astro | 1-2 seconds |
| Static sites | 1 second |

## Product Hunt Listing Sections

The generated `PRODUCT_HUNT.md` includes:

- **Tagline** (60 chars max) — punchy, outcome-focused
- **Description** (260 chars max) — problem + solution + proof point
- **Longer description** — problem, solution, features, key numbers, tech stack
- **Topics/Categories** — 3-5 PH categories
- **Maker Comment** — casual, first-person, authentic
- **Screenshot descriptions** — one-liner for each gallery image
- **Links** — website and key page URLs

## Tips for a Great Launch

- **Launch Tuesday-Thursday** at 12:01 AM PT for highest traffic
- **Review and personalize** the generated copy — add your voice
- **First screenshot = thumbnail** — make sure your homepage looks great
- **Light mode preferred** — PH gallery has a white background
- **Add browser chrome mockups** to screenshots for extra polish (optional post-processing)

## Complementary Skills

This skill works well with:
- `seo-optimization-guide` — ensure your site's meta tags are ready for the traffic spike
- `react-performance-patterns` — make sure your site is fast when PH visitors arrive

## Contributing

Found an issue or have a suggestion? Visit the [GitHub repository](https://github.com/anaghkanungo7/agent-skills) to contribute.

## License

MIT

---

**Created by**: Anagh Kanungo
**Version**: 1.0.0
**Last Updated**: 2026
