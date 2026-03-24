# SEO & Performance Optimization Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Bring the Lakeside Predatorfishing site to modern SEO standards — crawlability, structured data, image optimization, accessibility semantics, and Core Web Vitals performance.

**Architecture:** Static single-page site on GitHub Pages. All changes are in `index.html` (inline CSS/JS/HTML) plus new root-level files (`robots.txt`, `sitemap.xml`). Image optimization creates WebP variants alongside originals. No build tools — everything stays static.

**Tech Stack:** HTML5, CSS3, inline JS, GitHub Pages, `<picture>` element for modern image formats, Schema.org JSON-LD, Open Graph / Twitter Card meta tags.

---

## File Map

| Action | File | Responsibility |
|--------|------|----------------|
| Create | `robots.txt` | Crawl directives, sitemap pointer |
| Create | `sitemap.xml` | URL listing for search engines |
| Create | `og-image.jpg` | Social sharing preview (1200x630) — **requires design asset** |
| Modify | `index.html` | All meta tags, structured data, image elements, semantic HTML, video config |

---

## Phase 1: Crawlability & Social Sharing (Quick Wins)

### Task 1: Create robots.txt and sitemap.xml

**Files:**
- Create: `robots.txt`
- Create: `sitemap.xml`

- [ ] **Step 1: Create `robots.txt`**

```
User-agent: *
Allow: /

Sitemap: https://lakesidepredatorfishing.com/sitemap.xml
```

- [ ] **Step 2: Create `sitemap.xml`**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>https://lakesidepredatorfishing.com/</loc>
    <lastmod>2026-03-24</lastmod>
    <changefreq>monthly</changefreq>
    <priority>1.0</priority>
  </url>
</urlset>
```

- [ ] **Step 3: Verify both files are served**

Start local server (`npx http-server -p 8765`), then:
```bash
curl -s http://localhost:8765/robots.txt
curl -s http://localhost:8765/sitemap.xml
```
Expected: Both return their content with 200 status.

- [ ] **Step 4: Commit**

```bash
git add robots.txt sitemap.xml
git commit -m "seo: add robots.txt and sitemap.xml"
```

---

### Task 2: Fix Twitter Card & OG Meta Completeness

**Files:**
- Modify: `index.html` (lines 15–30, meta tags in `<head>`)

- [ ] **Step 1: Add missing twitter meta tags after the existing `twitter:card` tag**

Add these tags immediately after the `<meta name="twitter:card">` line:
```html
<meta name="twitter:title" content="Lakeside Predatorfishing — Lake Fishing in Norway">
<meta name="twitter:description" content="Pike and perch fishing in Northern Norway &amp; northern Sweden. Explore remote waters where few have ever cast a line — Asgeir &amp; Ola.">
<meta name="twitter:image" content="https://lakesidepredatorfishing.com/og-image.jpg">
<meta name="twitter:image:alt" content="Fishing guide holding a pike on a Norwegian lake at golden hour">
```

- [ ] **Step 2: Add `og:image:alt` after the existing `og:image:height` tag**

```html
<meta property="og:image:alt" content="Fishing guide holding a pike on a Norwegian lake at golden hour">
```

- [ ] **Step 3: Update `applyLanguage()` to also set twitter meta tags**

In the `applyLanguage` function, find where og meta tags are updated and add twitter equivalents:
```js
document.querySelector('meta[name="twitter:title"]')?.setAttribute('content', langData.pageTitle || '');
document.querySelector('meta[name="twitter:description"]')?.setAttribute('content', langData.description || '');
```

- [ ] **Step 4: Verify in Playwright**

Navigate to `http://localhost:8765`, evaluate:
```js
document.querySelector('meta[name="twitter:image"]')?.content
```
Expected: `https://lakesidepredatorfishing.com/og-image.jpg`

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "seo: add complete twitter card and og:image:alt meta tags"
```

---

### Task 3: Add hreflang Tags (Pragmatic Approach)

Since both languages are served from the same URL via JS switching, we use `x-default` to tell Google this is the canonical multilingual page. Full separate-URL i18n is a future enhancement.

**Files:**
- Modify: `index.html` (`<head>` section, after canonical link)

- [ ] **Step 1: Add hreflang link tags after the canonical `<link>`**

```html
<link rel="alternate" hreflang="en" href="https://lakesidepredatorfishing.com/">
<link rel="alternate" hreflang="sv" href="https://lakesidepredatorfishing.com/">
<link rel="alternate" hreflang="x-default" href="https://lakesidepredatorfishing.com/">
```

> **Note:** These all point to the same URL because both languages are JS-rendered on the same page. This tells Google the page serves multiple languages. For true multi-language SEO (separate indexing per language), a future refactor to `/en/` and `/sv/` paths would be needed.

- [ ] **Step 2: Verify tags are present**

```js
document.querySelectorAll('link[rel="alternate"][hreflang]').length
```
Expected: 3

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "seo: add hreflang tags for en/sv language variants"
```

---

## Phase 2: Structured Data Enhancements

### Task 4: Add VideoObject Schema

**Files:**
- Modify: `index.html` (add new `<script type="application/ld+json">` block after existing one)

- [ ] **Step 1: Add VideoObject JSON-LD block**

Place a new script block after the existing LocalBusiness JSON-LD (after the closing `</script>` of the structured data block):

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "VideoObject",
  "name": "Lakeside Predatorfishing — Northern Norway",
  "description": "Introduction to guided predator fishing trips on pristine lakes in Karasjok, Northern Norway. Pike and perch fishing in remote waters.",
  "thumbnailUrl": "https://lakesidepredatorfishing.com/intro-poster.png",
  "uploadDate": "2026-01-01",
  "contentUrl": "https://lakesidepredatorfishing.com/intro.mp4",
  "embedUrl": "https://lakesidepredatorfishing.com/",
  "duration": "PT30S",
  "provider": {
    "@type": "Organization",
    "name": "Lakeside Predatorfishing",
    "url": "https://lakesidepredatorfishing.com"
  }
}
</script>
```

- [ ] **Step 2: Validate JSON-LD syntax**

```bash
python -m json.tool <<< '{"@context":"https://schema.org","@type":"VideoObject","name":"test"}'
```

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "seo: add VideoObject structured data for intro video"
```

---

### Task 5: Add TouristTrip Schema for Service Offerings

**Files:**
- Modify: `index.html` (add new JSON-LD block)

- [ ] **Step 1: Add TouristTrip JSON-LD**

Add after the VideoObject block:

```html
<script type="application/ld+json">
[
  {
    "@context": "https://schema.org",
    "@type": "TouristTrip",
    "name": "Day Trip — Guided Predator Fishing",
    "description": "Full-day guided fishing trip with boat, gear, and local guide support on remote Northern Norwegian lakes.",
    "touristType": ["Fishing", "Outdoor Adventure"],
    "provider": {
      "@type": "LocalBusiness",
      "name": "Lakeside Predatorfishing",
      "url": "https://lakesidepredatorfishing.com"
    },
    "offers": {
      "@type": "Offer",
      "availability": "https://schema.org/InStock",
      "priceCurrency": "NOK"
    }
  },
  {
    "@context": "https://schema.org",
    "@type": "TouristTrip",
    "name": "Private Trip — Exclusive Group Fishing",
    "description": "Private guided fishing experience for your group. Choose your pace, preferred waters, and trip style.",
    "touristType": ["Fishing", "Outdoor Adventure"],
    "provider": {
      "@type": "LocalBusiness",
      "name": "Lakeside Predatorfishing",
      "url": "https://lakesidepredatorfishing.com"
    },
    "offers": {
      "@type": "Offer",
      "availability": "https://schema.org/InStock",
      "priceCurrency": "NOK"
    }
  },
  {
    "@context": "https://schema.org",
    "@type": "TouristTrip",
    "name": "Seasonal Trip — Multi-Day Fishing Package",
    "description": "Multi-day seasonal fishing packages designed around seasonal fish behavior and the best waters for each time of year.",
    "touristType": ["Fishing", "Outdoor Adventure"],
    "provider": {
      "@type": "LocalBusiness",
      "name": "Lakeside Predatorfishing",
      "url": "https://lakesidepredatorfishing.com"
    },
    "offers": {
      "@type": "Offer",
      "availability": "https://schema.org/InStock",
      "priceCurrency": "NOK"
    }
  }
]
</script>
```

- [ ] **Step 2: Commit**

```bash
git add index.html
git commit -m "seo: add TouristTrip structured data for all three service offerings"
```

---

## Phase 3: Image & Video Performance

### Task 6: Change Video Preload Strategy

**Files:**
- Modify: `index.html` (video element, ~line 1960)

- [ ] **Step 1: Change `preload="auto"` to `preload="metadata"` on the video element**

Find:
```html
<video id="intro-video" autoplay muted playsinline preload="auto"
```
Replace with:
```html
<video id="intro-video" autoplay muted playsinline preload="metadata"
```

> **Rationale:** `preload="auto"` downloads the entire 13MB MP4 / 11MB WebM upfront. `preload="metadata"` only downloads headers and a few frames. Since the video autoplays, the browser will start downloading the stream on play anyway — but this prevents blocking the LCP image.

- [ ] **Step 2: Verify video still autoplays**

Navigate to `http://localhost:8765`, confirm intro video plays. The poster image should show briefly, then video streams in.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "perf: change intro video preload from auto to metadata for faster LCP"
```

---

### Task 7: Convert Images to WebP with `<picture>` Fallback

**Files:**
- Create: WebP versions of all images (requires image conversion tool)
- Modify: `index.html` (all `<img>` elements)

> **Prerequisites:** Need `cwebp` (from Google's WebP tools) or `sharp-cli` installed. If not available, use an online converter or skip to the `<picture>` markup and convert images manually later.

- [ ] **Step 1: Convert PNG/JPG to WebP**

```bash
# Install cwebp if available, or use npx sharp-cli
# Background image (target ~200KB from 2.5MB)
cwebp -q 80 background-image.png -o background-image.webp
cwebp -q 80 intro-poster.png -o intro-poster.webp

# Gallery images (target ~80-150KB each)
for f in 1.jpg 2.jpg 3.jpg 4.jpg 5.jpg 6.jpg; do
  cwebp -q 80 "$f" -o "${f%.jpg}.webp"
done
```

- [ ] **Step 2: Update background image to `<picture>` element**

Replace the current `<img>` for background:
```html
<picture>
  <source srcset="background-image.webp" type="image/webp">
  <img class="landing-bg" src="background-image.png" alt="Norwegian fjord landscape"
       loading="eager" decoding="sync" fetchpriority="high" width="2752" height="1536">
</picture>
```

> **Note:** Changed `decoding="async"` to `decoding="sync"` for the LCP image per latest web.dev guidance — the LCP element should not be decoded asynchronously.

- [ ] **Step 3: Update gallery images to `<picture>` elements**

For each gallery tile image (1.jpg through 6.jpg), wrap in `<picture>`:
```html
<picture>
  <source srcset="1.webp" type="image/webp">
  <img src="1.jpg" alt="Angler holding a pike on a boat under dramatic skies"
       loading="lazy" decoding="async" width="1536" height="2048">
</picture>
```

Repeat for all 6 gallery images (and the 6th which is inside the `<a>` link).

- [ ] **Step 4: Update intro poster fallback image**

```html
<picture>
  <source srcset="intro-poster.webp" type="image/webp">
  <img id="intro-fallback" src="intro-poster.png" alt="Predator fishing"
       width="2752" height="1536">
</picture>
```

- [ ] **Step 5: Update CSS background-image for gallery page**

The `.gallery-bg-fill` uses CSS `url('background-image.png')`. CSS `image-set()` can provide WebP:
```css
.gallery-bg-fill {
  background: url('background-image.png') center 40%/cover no-repeat;
  background: image-set(
    url('background-image.webp') type('image/webp'),
    url('background-image.png') type('image/png')
  ) center 40%/cover no-repeat;
}
```

- [ ] **Step 6: Verify images load in both formats**

Open in Playwright, check network requests. Chrome should request `.webp` files. Disable WebP support (or use Safari) to confirm `.jpg`/`.png` fallbacks work.

- [ ] **Step 7: Commit**

```bash
git add *.webp index.html
git commit -m "perf: add WebP image variants with picture element fallbacks"
```

---

### Task 8: Add Responsive Image Srcsets

**Files:**
- Create: Resized variants of `background-image` (1024w, 1536w, 2752w)
- Modify: `index.html` (background image `<picture>` element)

> **Note:** This task depends on having image resize tooling. Can be done with `sharp`, ImageMagick, or manually.

- [ ] **Step 1: Create resized background image variants**

```bash
# Using sharp-cli or ImageMagick
convert background-image.png -resize 1024x background-image-1024.png
convert background-image.png -resize 1536x background-image-1536.png
cwebp -q 80 background-image-1024.png -o background-image-1024.webp
cwebp -q 80 background-image-1536.png -o background-image-1536.webp
```

- [ ] **Step 2: Update `<picture>` with srcset**

```html
<picture>
  <source srcset="background-image-1024.webp 1024w,
                  background-image-1536.webp 1536w,
                  background-image.webp 2752w"
          sizes="100vw" type="image/webp">
  <source srcset="background-image-1024.png 1024w,
                  background-image-1536.png 1536w,
                  background-image.png 2752w"
          sizes="100vw" type="image/png">
  <img class="landing-bg" src="background-image.png" alt="Norwegian fjord landscape"
       loading="eager" decoding="sync" fetchpriority="high" width="2752" height="1536">
</picture>
```

- [ ] **Step 3: Verify correct variant loads per viewport**

Resize Playwright to 375px width — should load 1024w variant. Resize to 1440px — should load 1536w or 2752w.

- [ ] **Step 4: Commit**

```bash
git add background-image-*.png background-image-*.webp index.html
git commit -m "perf: add responsive srcset for background image (1024/1536/2752w)"
```

---

## Phase 4: Semantic HTML & Accessibility

### Task 9: Convert Service Cards to `<button>` Elements

**Files:**
- Modify: `index.html` (service card HTML ~lines 2062-2100, and JS click handler)

- [ ] **Step 1: Replace `<div role="button">` with `<button>` for each service card**

Current pattern:
```html
<div class="service-item" role="button" tabindex="0" data-panel="guided" ...>
```

Replace with:
```html
<button class="service-item" data-panel="guided" data-title="DAY TRIP" data-tagline="...">
```

Remove `role="button"` and `tabindex="0"` (native `<button>` has both built-in).

- [ ] **Step 2: Update CSS to reset button defaults**

Add to the `.service-item` CSS rule:
```css
.service-item {
  /* existing styles... */
  border: none;
  background: none;
  font: inherit;
  cursor: pointer;
}
```

- [ ] **Step 3: Remove keyboard event handler for Enter/Space**

If there's a `keydown` listener on service items for Enter/Space, remove it — `<button>` handles this natively.

- [ ] **Step 4: Verify keyboard navigation works**

Tab to service cards, press Enter — should toggle content. Press Space — should also toggle.

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "a11y: convert service cards from div[role=button] to native button elements"
```

---

### Task 10: Improve Heading Hierarchy

**Files:**
- Modify: `index.html` (service content slides, gallery headings)

- [ ] **Step 1: Add `<h3>` headings inside tour detail content slides**

Each `.content-slide` for a service (guided, booking, lessons) currently uses `<p>` for the description. Wrap the tour-detail sections with a heading:

Within the `switchContent` function, the title is set on the h1 element when a service card is clicked. This is already good. But the default content slide and gallery could benefit from section labels.

Add a visually-hidden section heading before the services row:
```html
<h2 class="sr-only">Our Services</h2>
```

Add the `.sr-only` utility class to CSS:
```css
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}
```

- [ ] **Step 2: Wrap language switcher in `<nav>`**

```html
<nav aria-label="Language selector">
  <button class="lang-btn" data-lang="en">EN</button>
  <button class="lang-btn active" data-lang="sv">SV</button>
</nav>
```

Replace the current `<div class="lang-switch" ...>` with this `<nav>`.

- [ ] **Step 3: Verify heading order**

```js
Array.from(document.querySelectorAll('h1,h2,h3')).map(h => `${h.tagName}: ${h.textContent.trim().slice(0, 50)}`)
```

Expected: `["H1: Lakeside Predatorfishing", "H2: Our Services", "H2: From the Water"]`

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "a11y: improve heading hierarchy and wrap lang switcher in nav element"
```

---

## Phase 5: OG Image (Requires Design Asset)

### Task 11: Create and Add og-image.jpg

> **Blocker:** This task requires a design asset. Options:
> 1. Screenshot a compelling frame from the intro video or use the background image
> 2. Composite: background landscape + logo overlay + tagline text
> 3. Commission a custom image
>
> The image must be 1200x630px, JPEG format, under 1MB.

**Files:**
- Create: `og-image.jpg` (1200x630)

- [ ] **Step 1: Create the image**

Option A (quick): Crop `background-image.png` to 1200x630 and overlay logo text:
```bash
convert background-image.png -resize 1200x -gravity center -crop 1200x630+0+0 +repage \
  -brightness-contrast -10x10 og-image.jpg
```

Option B (better): Use a design tool to composite background + "Lakeside Predatorfishing" text + "Pike & Perch Fishing in Northern Norway" subtitle.

- [ ] **Step 2: Verify the meta tag reference resolves**

```bash
curl -s -o /dev/null -w "%{http_code}" http://localhost:8765/og-image.jpg
```
Expected: 200

- [ ] **Step 3: Commit**

```bash
git add og-image.jpg
git commit -m "seo: add og-image.jpg for social sharing previews"
```

---

## Phase 6: Future Considerations (Not in This Plan)

These are documented for future planning but **out of scope** for this implementation:

1. **Separate language URLs** (`/en/`, `/sv/`): The only way to get both languages indexed independently in Google. Requires either a static site generator (11ty, Astro) or a build step that duplicates `index.html` per language. High effort, high SEO impact for Swedish market.

2. **AVIF image format**: Even smaller than WebP (~50%). Browser support is now universal. Can be added as another `<source>` in `<picture>` elements. Medium effort.

3. **CDN for video**: Serving 13MB MP4 from GitHub Pages is suboptimal. A video CDN (Cloudflare Stream, Mux, Bunny) with adaptive bitrate would improve loading. Medium effort, may have cost.

4. **Google Search Console submission**: After deploying, submit sitemap.xml and verify ownership. Zero effort, high value.

5. **Performance monitoring**: Set up Lighthouse CI or CrUX monitoring to track Core Web Vitals over time.

---

## Summary

| Phase | Tasks | Effort | Impact |
|-------|-------|--------|--------|
| 1: Crawlability & Social | Tasks 1-3 | Low (30 min) | High |
| 2: Structured Data | Tasks 4-5 | Low (20 min) | Medium |
| 3: Image Performance | Tasks 6-8 | Medium (1-2 hrs) | High |
| 4: Semantic HTML | Tasks 9-10 | Low (30 min) | Medium |
| 5: OG Image | Task 11 | Low-Medium (depends on asset) | High |
