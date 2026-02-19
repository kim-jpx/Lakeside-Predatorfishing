# UX/UI Adjustment Plan — Lakeside Predatorfishing

## Current State Summary

Single-page landing with: intro video overlay, hero section (title, tagline, description, CTAs, service cards), and a bottom bar. All CSS is inline in `index.html`. One breakpoint at `600px`. Fonts: Teko (headings) + Exo 2 (body).

---

## 1. Spacing System Overhaul

### 1a. Expand CSS Custom Properties
- Current scale is too narrow (`0.2rem` – `2rem`, only 6 steps)
- Add `--space-2xl: 3rem`, `--space-3xl: 4.5rem` for larger sections
- Introduce a consistent ratio (e.g. ~1.5x) between steps so spacing feels harmonious

### 1b. Landing Content Container
- Current `padding: var(--space-xs) 1.85rem` is tight and uses a magic number
- Replace with token-based padding: `var(--space-lg) var(--space-xl)` (desktop), `var(--space-md) var(--space-lg)` (mobile)
- Current `gap: 1.12rem` — round to `var(--space-lg)` (1.5rem) for breathing room between title/tagline/divider/content/CTAs

### 1c. Services Row
- Desktop `gap: 2.5rem` feels wide relative to the card size; tighten to ~`2rem`
- Mobile `gap: 1.45rem` — reduce to `1rem` so items don't crowd on small screens
- `margin-top: 0.75rem` is too close to the CTA buttons; increase to `var(--space-lg)`

### 1d. CTA Row
- `margin-top: 0.8rem` — increase to `var(--space-lg)` for clearer separation from the description text
- Mobile column layout `gap: 0.55rem` — increase to `0.75rem`

### 1e. Bottom Bar
- Desktop padding `1rem 2.2rem` — increase vertical to `1.25rem` for better tap target and breathing room
- Mobile padding `0.75rem 1.35rem` — increase to `1rem 1.5rem`
- Mobile stacked `gap: 0.24rem` for contact info is too cramped; increase to `0.5rem`

### 1f. Content Area
- `padding: 0 0.2rem` is nearly zero; increase to `0 var(--space-md)` so text doesn't visually edge-bleed
- Tour detail chips `margin: 0.7rem 0` — normalize to `var(--space-md) 0`

---

## 2. Font Size & Typography

### 2a. Heading (h1)
- Current: `clamp(1.75rem, 4.8vw, 3rem)` — on mid-size tablets this can feel oversized relative to narrow content width
- Adjust to: `clamp(1.6rem, 4vw, 2.75rem)` for a slightly more refined scale

### 2b. Tagline
- Current: `clamp(0.65rem, 1.3vw, 0.85rem)` — the minimum `0.65rem` (~10.4px) is below accessible minimums
- Raise floor to `0.75rem` (~12px): `clamp(0.75rem, 1.3vw, 0.88rem)`

### 2c. Body / Content Slides
- Desktop `0.82rem` (~13px) is slightly small for comfortable reading
- Increase to `0.88rem` (~14px) desktop, `0.8rem` mobile (up from `0.75rem`)

### 2d. Service Item Labels
- Desktop `0.65rem` (~10.4px) is small; increase to `0.72rem`
- Mobile `0.55rem` (~8.8px) is too small to read; increase to `0.62rem`
- Explore hint: `0.5rem` (8px) is barely legible; increase to `0.58rem` desktop, `0.52rem` mobile

### 2e. Bottom Bar
- Contact info: `0.8rem` is fine desktop; mobile `0.72rem` — keep as-is
- Copyright: `0.7rem` desktop, `0.62rem` mobile — increase mobile to `0.65rem`

### 2f. Buttons
- `0.78rem` desktop, `0.75rem` mobile — increase both by ~0.02rem for readability: `0.8rem` / `0.78rem`

### 2g. Tour Detail Chips
- Chip value `1rem` desktop / `0.85rem` mobile — fine, keep as-is
- Chip label `0.58rem` (9.3px) — increase to `0.62rem`; mobile `0.52rem` → `0.56rem`

---

## 3. Alignment & Layout

### 3a. Vertical Centering
- `.landing-content` is flex-centered in viewport, but the bottom-bar pushes visual center upward
- Add `padding-bottom: 60px` (approx bottom-bar height) to `.landing` so content truly sits at optical center

### 3b. Content Max-Width
- `.landing-content` max-width is `660px`, but `.content-area` is capped at `480px`
- The jump from 660 → 480 creates a noticeable narrowing; widen `.content-area` to `520px` for better flow

### 3c. Divider Centering
- Divider has `margin: 0.45rem 0` — ensure it's symmetrical: `0.5rem auto` (auto for horizontal centering is implicit from flex, but margin-block should match)

### 3d. Service Item Internal Alignment
- Icon-to-label spacing: `margin-top: 0.38rem` — normalize to `0.4rem`
- Explore hint `margin-top: 0.22rem` — increase to `0.3rem`
- Ensure all three service items have consistent `min-width` so they form a balanced row

### 3e. Button Sizing
- `min-width: 140px` / `height: 40px` — increase to `min-width: 150px` / `height: 42px` for a slightly more comfortable tap target (especially mobile)
- Mobile `min-width: 160px` / `height: 38px` — height should not shrink on mobile; keep at `42px`

### 3f. Top Logo Button Position
- `top: 1.15rem` desktop / `0.7rem` mobile — standardize to `1rem` / `0.75rem` for alignment with bottom-bar padding

---

## 4. Responsive Breakpoints

### 4a. Add Tablet Breakpoint
- Currently only `max-width: 600px`; add `max-width: 900px` for tablets
- At 600–900px: slightly reduce heading sizes, adjust services-row gap, keep horizontal CTA layout but smaller buttons

### 4b. Mobile Adjustments (under 600px)
- All the font-size and spacing increases noted above in their respective sections
- Ensure CTA buttons in column layout are full-width (`width: 100%`) instead of fixed `min-width` for consistency

### 4c. Small Mobile (under 380px)
- Consider adding `max-width: 380px` breakpoint for very small devices
- Further reduce heading size and services-row gap
- Stack service items 2+1 or reduce icon size

---

## 5. Implementation Order

1. **Spacing tokens** — update `:root` variables and replace magic numbers throughout
2. **Typography** — adjust all font-size values per section 2
3. **Alignment & layout** — apply section 3 fixes
4. **Tablet breakpoint** — add `@media (max-width: 900px)` rules
5. **Mobile polish** — update existing `600px` breakpoint with new values
6. **Small mobile** — add `380px` breakpoint if needed
7. **Visual QA** — test at 360px, 414px, 768px, 1024px, 1440px widths
