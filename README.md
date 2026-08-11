# Action Plumbing Heating & HVAC — Website

Static marketing site for **Action Plumbing Heating & HVAC**, Lehigh Valley, PA.  
Built with [Jekyll](https://jekyllrb.com/) and deployed on **Vercel**.

---

## Table of Contents

1. [Tech Stack](#tech-stack)
2. [Local Development Setup](#local-development-setup)
3. [Project Structure](#project-structure)
4. [Site Configuration](#site-configuration)
5. [Layouts](#layouts)
6. [Includes (Partials)](#includes-partials)
7. [Reusable Components](#reusable-components)
   - [Service Card](#service-card)
8. [Styles](#styles)
9. [JavaScript](#javascript)
10. [Images](#images)
11. [Writing Blog Posts](#writing-blog-posts)
12. [Adding New Service Pages](#adding-new-service-pages)
13. [Deployment (Vercel)](#deployment-vercel)
14. [SEO & Meta Tags](#seo--meta-tags)

---

## Tech Stack

| Layer | Tool |
|---|---|
| Static site generator | Jekyll |
| Hosting | Vercel |
| Styling | Plain CSS (custom properties, no framework) |
| JavaScript | Vanilla JS (no build step) |
| Templating | Liquid |
| Plugins | `jekyll-seo-tag`, `jekyll-sitemap` |

No Node, no Webpack, no npm — just Ruby + Jekyll.

---

## Local Development Setup

### Prerequisites

- Ruby ≥ 3.0 with Bundler (`gem install bundler`)
- Git

### Steps

```bash
# 1. Clone the repo
git clone https://github.com/jimmyflame77/Action-Plumbing.git
cd Action-Plumbing

# 2. Install dependencies
bundle install

# 3. Serve locally with live reload
bundle exec jekyll serve --livereload
```

The site will be available at `http://localhost:4000/`.

> **Windows note:** If you hit `wdm` gem errors, run `bundle add wdm` or add `gem "wdm", ">= 0.1.0"` to the Gemfile and re-run `bundle install`.

### Useful commands

```bash
# Build to _site/ without serving
bundle exec jekyll build

# Build with verbose output (good for debugging)
bundle exec jekyll build --verbose

# Wipe the build cache
bundle exec jekyll clean
```

---

## Project Structure

```
Action-Plumbing/
│
├── _config.yml            # Jekyll site configuration
├── _blog-config.json      # Blog metadata reference (categories, defaults)
├── Gemfile                # Ruby gem dependencies
├── robots.txt             # Search engine crawl rules
│
├── _data/
│   └── site.yml           # Global site variables (phone, URLs, company info)
│
├── _includes/             # Reusable HTML partials
│   ├── head.html          # <head> tag (meta, CSS)
│   ├── header.html        # Site header & navigation
│   ├── footer.html        # Site footer
│   ├── scripts.html       # JS includes (end of <body>)
│   └── service-card.html  # *** Reusable service card component ***
│
├── _layouts/
│   ├── default.html       # Base layout (wraps all pages)
│   └── post.html          # Blog post layout (extends default)
│
├── _posts/                # Blog posts (Markdown)
│   └── YYYY-MM-DD-slug.md
│
├── css/
│   └── style.css          # All site styles (single file, no preprocessor)
│
├── js/
│   └── main.js            # Vanilla JS (mobile menu toggle)
│
├── images/                # All site images (WebP preferred)
│
├── index.html             # Home page
└── blog.html              # Blog listing page
```

> **`_site/`** is Jekyll's build output — it is git-ignored and should never be edited directly.

---

## Site Configuration

### `_config.yml`

```yaml
url: "https://actionplumbingonline.com"
baseurl: ""
markdown: kramdown
permalink: none          # Posts use their own slug from the filename
plugins:
  - jekyll-seo-tag
  - jekyll-sitemap
```

`baseurl` is empty because the site uses a custom domain. All internal links should use the `relative_url` filter:

```liquid
href="{{ '/some-page' | relative_url }}"
src="{{ '/images/photo.webp' | relative_url }}"
```

### `_data/site.yml`

Central store for all site-wide variables. Reference them anywhere with `site.data.site.*`.

| Key | Value | Usage |
|---|---|---|
| `phone` | `610-252-1256` | Raw digits |
| `phone_formatted` | `(610) 252-1256` | Display text |
| `phone_link` | `tel:6102521256` | `href` for call buttons |
| `company_name` | `Action Plumbing Heating & HVAC` | Footer, meta |
| `tagline` | Short tagline | SEO / meta |
| `domain` / `main_site` | Full site URL | Absolute links in header/footer |
| `hours` | `24/7 Emergency Service` | Footer |
| `facebook` | Facebook page URL | Social links |
| `year` | `2026` | Footer copyright |

**To update the phone number or any business detail**, only edit `_data/site.yml` — it propagates everywhere automatically.

---

## Layouts

### `_layouts/default.html`

Base layout used by all pages. Renders in this order:

```
head.html → <body> → header.html → {{ content }} → footer.html → scripts.html
```

### `_layouts/post.html`

Extends `default`. Wraps blog post content with:
- JSON-LD `BlogPosting` structured data (auto-populated from front matter)
- Hero image block (if `image:` is set in front matter)
- Article body with meta bar (category badge, date, author)
- Prev/Next article navigation

---

## Includes (Partials)

| File | Purpose |
|---|---|
| `head.html` | `<head>` with charset, viewport, SEO meta, Open Graph, Twitter Card, CSS link |
| `header.html` | Sticky nav bar with desktop links + mobile hamburger menu |
| `footer.html` | Three-column footer (brand, quick links, contact) |
| `scripts.html` | Single `<script>` tag loading `main.js` |
| `service-card.html` | **Reusable service card** — see full docs below |

---

## Reusable Components

### Service Card

**File:** `_includes/service-card.html`

A self-contained card component for displaying a service offering. Matches the visual style of the blog post cards (16:9 masked image, body with title, description, and CTA button). Cards lift and glow orange on hover.

#### Parameters

| Parameter | Required | Description |
|---|---|---|
| `title` | ✅ | Card heading text (e.g. `"Heating &amp; Air"`) |
| `image` | ✅ | Absolute path to image from site root (e.g. `"/images/action-plumbing-hvac.webp"`) |
| `alt` | ✅ | Image alt text for accessibility / SEO |
| `desc` | ✅ | Short paragraph description |
| `link` | ✅ | Destination URL (e.g. `"/heating-and-air"`) |

#### Usage

Drop the `include` tag inside any `.svc-grid` container:

```liquid
<div class="svc-grid">
  {% include service-card.html
    title="Heating &amp; Air"
    image="/images/action-plumbing-hvac.webp"
    alt="HVAC technician servicing a unit"
    desc="Our heating and air conditioning services keep your indoor environment comfortable year-round."
    link="/heating-and-air"
  %}
</div>
```

#### Grid layout

Wrap one or more cards in `.svc-grid` to get the responsive grid:

```html
<div class="svc-grid">
  {% include service-card.html ... %}
  {% include service-card.html ... %}
  {% include service-card.html ... %}
</div>
```

`.svc-grid` uses `grid-template-columns: repeat(auto-fill, minmax(300px, 1fr))` — cards sit in a row on desktop and stack vertically on mobile automatically. No extra classes needed.

#### Styling hooks

All card styles live under the `/* ---- Services Section ---- */` block in `css/style.css`. Key selectors:

| Selector | Purpose |
|---|---|
| `.svc-grid` | Responsive grid wrapper |
| `.svc-card` | Card container (white bg, border-radius, hover lift) |
| `.svc-card-img-wrap` | 16:9 image mask |
| `.svc-card-title` | Navy heading |
| `.svc-card-desc` | Grey description text |
| `.svc-card-btn` | Orange CTA button (inherits `.btn .btn-primary`) |

---

## Styles

**Single file:** `css/style.css` — no preprocessor, no build step.

### CSS Custom Properties (Design Tokens)

Defined in `:root` at the top of the file:

```css
--clr-primary:      #162844   /* Navy blue — backgrounds, headings */
--clr-primary-dark: #0e1b2e   /* Darker navy — footer, overlays */
--clr-accent:       #e05c1a   /* Orange — buttons, highlights, hover glows */
--clr-accent-light: #f07540   /* Lighter orange — button hover states */
--clr-text:         #1a1a2e   /* Near-black body text */
--clr-text-light:   #555      /* Secondary / muted text */
--clr-bg:           #f8f9fc   /* Page background */
--clr-white:        #fff
--clr-border:       #e2e6f0
--container:        1100px    /* Max content width */
--radius:           8px
--shadow / --shadow-lg        /* Box shadow tokens */
--transition:       0.2s ease
```

**Always use these tokens** — never hard-code colour hex values in new components so the whole site can be re-themed from one place.

### Style Sections (in file order)

1. CSS custom properties + reset
2. `.btn` utility classes
3. Header / nav
4. Blog hero
5. Blog listing / post cards
6. Article hero
7. Article body + content styles
8. Article navigation
9. **Services section + service cards** ← new component
10. CTA strip
11. Footer
12. Responsive breakpoints (`768px`, `480px`)

---

## JavaScript

**Single file:** `js/main.js`

Currently handles only the mobile navigation:

- Clicking the hamburger button toggles `.open` on both `#hamburger` and `#mobile-menu`
- Clicking anywhere outside the menu closes it automatically

No libraries or build tools are used. Add new scripts in `main.js` or add additional `<script>` tags in `_includes/scripts.html`.

---

## Images

All images live in `images/` and should be in **WebP format** for performance.

| File | Used for |
|---|---|
| `broken-ac-hot-summer-ga9u35.webp` | Home page hero background |
| `action-plumbing-hvac.webp` | Heating & Air service card |
| `action-plumbing-wrench.webp` | Plumbing & Water service card |
| `action-plumbing-sewage.webp` | Drain & Sewer service card |
| `action-plumbing-hvac-fix-yj1ti3.webp` | Alternate HVAC image |
| `action-plumbing-water-blowout-8pp23t.webp` | Alternate plumbing image |
| `truck-hero.webp` | Service truck / hero image |
| `logo.webp` | Company logo |

Always reference images via `relative_url` so they work on both `localhost` and Vercel:

```liquid
src="{{ '/images/your-image.webp' | relative_url }}"
```

---

## Writing Blog Posts

Blog posts live in `_posts/` and follow the Jekyll naming convention:

```
YYYY-MM-DD-post-slug.md
```

### Required front matter

```yaml
---
layout: post
title: "Your Post Title"
date: 2026-04-09
category: "Tips"            # See _blog-config.json for valid categories
author: "Action Plumbing"
image: "/images/your-image.webp"
excerpt: "One-sentence summary shown in blog cards."
description: "Longer meta description for SEO (150–160 chars)."
---
```

### Available categories

Defined in `_blog-config.json`:

```
Tips | Guides | News | Plumbing | HVAC | Drain & Sewer
```

### Optional front matter

```yaml
robots: "noindex"           # Prevent indexing a specific post
canonical: "https://..."    # Override canonical URL
og_title: "..."             # Override Open Graph title
og_description: "..."       # Override OG description
```

### Post body

Write standard Markdown beneath the front matter. The `post.html` layout automatically renders:
- JSON-LD structured data
- Hero image
- Meta bar (category badge, date, author)
- `<h1>` title
- Article content
- Prev / next navigation

---

## Adding New Service Pages

Three service pages are linked but not yet created:

| Page | URL slug |
|---|---|
| Heating & Air | `/heating-and-air` |
| Plumbing & Water | `/plumbing-and-water` |
| Drain & Sewer | `/sewer-and-drain-cleaning` |

To create a service page:

1. Create `heating-and-air.html` (or `.md`) in the project root.
2. Add front matter:

```yaml
---
layout: default
title: "Heating & Air | Action Plumbing Heating & HVAC"
description: "..."
---
```

3. Build out the page content using existing CSS classes and the service card include as needed.
4. The navigation in `_includes/header.html` already links to these pages — no changes needed there.

---

## Deployment (Vercel)

The site deploys automatically on every push to the connected Git branch via Vercel.

**Production URL:** `https://actionplumbingonline.com` (configure in Vercel → Settings → Domains)

Build settings are defined in `vercel.json`:

- Install: `bundle install`
- Build: `bundle exec jekyll build --trace`
- Output: `_site`

Connect the GitHub repo in the Vercel dashboard, or deploy with the Vercel CLI.

> **`_site/` is gitignored.** Never commit build output.

---

## SEO & Meta Tags

Meta tags are centrally managed in `_includes/head.html`. Each page/post controls its own values through front matter:

| Front matter key | Output |
|---|---|
| `title` | `<title>` + OG/Twitter title fallback |
| `description` | `<meta name="description">` + OG/Twitter fallback |
| `image` | OG image + Twitter card image |
| `canonical` | `<link rel="canonical">` + OG URL |
| `robots` | `<meta name="robots">` (defaults to `index, follow`) |
| `og_title` | Explicit OG title override |
| `og_description` | Explicit OG description override |

The `jekyll-sitemap` plugin auto-generates `/sitemap.xml` on every build, referenced in `robots.txt`.

---

*Last updated: April 2026*
