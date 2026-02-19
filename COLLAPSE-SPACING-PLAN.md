# Plan: Collapse Hero Content & Unify Transitions

## Part 1 — Tighten Spacing (DONE)
Reduced `.landing-content` gap from `1.5rem` → `0.4rem`, tightened `.divider` margin, `.cta-row` margin-top, `.services-row` margin-top, and matched across all responsive breakpoints.

## Part 2 — Unify Transitions (DONE)
Replaced the collapse/scale transition (default ↔ trip) with the slide transition (used between tour packages) so ALL content switches use one consistent animation.

### CSS removed
- `.content-slide.collapse-out` / `.content-slide.expand-in` (scale + blur)
- `.divider.collapse` / `.content-area.collapse` / `.cta-row.collapse` (scale + opacity)
- `.divider.uncollapse` / `.content-area.uncollapse` / `.cta-row.uncollapse`
- `.kinetic-title.swap-out` / `.tagline.swap-out` / `.swap-in` (scale + blur)

### CSS kept (slide system)
- `.kinetic-title.slide-out` / `.tagline.slide-out` → `translateX(-60px)`
- `.kinetic-title.slide-in` / `.tagline.slide-in` → `translateX(60px)`
- `.content-slide.exit-left` → `translateX(-110%)`
- `.content-slide.enter-from-right` → `translateX(110%)`

### JS removed
- `collapseZone()` function
- `expandZone()` function
- `isDefaultView` variable

### JS rewritten
- `switchContent()` — removed the `if (leavingDefault || goingToDefault)` branch; all transitions now use `slideSwapElement()` for title/tagline and `exit-left`/`enter-from-right` for content panels

## Verification
- Click service item from default: slides (not collapses)
- Click between services: slides (unchanged)
- Click active service or top logo to return to default: slides back
- Test across desktop, tablet, mobile breakpoints
- Verify `prefers-reduced-motion` still works
