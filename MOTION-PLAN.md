# Motion & Attention Plan — Tour Package Buttons

## Goal
Draw user attention to the three tour package buttons (DAY TRIP, PRIVATE TRIP, SEASONAL TRIP) using motion/transitions only — **zero changes to design, layout, or structure**.

---

## Current State

The `.service-item` buttons already have:
- `transition: all 0.3s` on base state
- `transform: translateY(-3px)` + color change on `:hover`
- Icon SVGs with `transition: all 0.3s`
- `.explore-hint` text with `transition: color 0.3s`
- Initial `fadeUp` entrance animation (delay 2s) on `.services-row`

The site already uses `backdrop-filter` on bottom-bar and language-switcher. The `prefers-reduced-motion` media query is already in place but needs extending for new animations.

---

## Proposed Techniques (in order of priority)

### 1. Warm-Rope Glow Pulse on `::after` pseudo-element
**What**: A soft, rhythmic glow ring around each service item using `::after` with an animated `opacity` cycle. Uses the existing `--warm-rope` color.

**Why `::after` instead of `box-shadow`**: Animating `box-shadow` directly is CPU-bound and triggers repaint every frame. Placing a pre-rendered radial gradient on a pseudo-element and animating only `opacity` keeps it GPU-accelerated.

**Spec**:
- `::after` absolutely positioned, `inset: -6px`, `border-radius: 8px`
- `background: radial-gradient(ellipse, rgba(200,169,106,0.18), transparent 70%)`
- `@keyframes glowPulse`: `opacity` from `0.4` → `0.8` → `0.4` over **3s** `ease-in-out infinite`
- Stagger each button: `animation-delay: 0s / 1s / 2s`
- Stops on hover (animation paused, glow at full `opacity: 0.9`) to signal "you've found it"
- `pointer-events: none` on pseudo to not block clicks
- `z-index: -1` so it sits behind button content

**Edge cases**:
- ~~Conflict with existing `transition: all 0.3s`~~ — No conflict: the `::after` is a separate element with its own animation; the base element's transition handles hover transforms independently
- Must add `position: relative` to `.service-item` (it already has it implicitly via existing positioning)
- On low-end mobile: `opacity` animation on pseudo-element is trivially cheap — GPU composites a single layer
- Background tab: CSS `opacity` animation auto-pauses in inactive tabs (browser optimization)

---

### 2. Icon Micro-Bounce (one-shot, delayed)
**What**: After the entrance `fadeUp` completes (~3s from page load), the SVG icons do a single subtle bounce sequence — a small `translateY` dip and return — to invite interaction. Not a loop.

**Spec**:
- `@keyframes iconNudge`: `0%{transform:translateY(0)}` → `40%{transform:translateY(-3px)}` → `70%{transform:translateY(1px)}` → `100%{transform:translateY(0)}`
- Duration: **0.5s** `ease-out`, `animation-fill-mode: both`
- `animation-delay`: `3.2s / 3.4s / 3.6s` (staggered per button, after entrance anim finishes)
- `animation-iteration-count: 1` — plays once, never loops
- On hover: existing `translateY(-3px)` transition takes over naturally

**Edge cases**:
- Transform conflict: The icon SVG lives inside `.service-item .icon svg`. The hover transform is on `.service-item` (parent), while this nudge targets `.service-item .icon`. Different elements → no conflict.
- GPU anti-aliasing shift: Sub-pixel `translateY` on SVG icons won't cause text rendering artifacts since these are vector paths, not text.
- One-shot animation = zero ongoing CPU/battery cost.

---

### 3. "Explore" Hint Shimmer Sweep
**What**: A single light sweep across the "Explore" text under each button, timed after the icon nudge. Draws the eye downward to the call-to-action hint.

**Spec**:
- On `.explore-hint::before`: a `linear-gradient(90deg, transparent, rgba(200,169,106,0.4), transparent)` positioned absolute
- `@keyframes shimmerSweep`: `translateX(-100%)` → `translateX(100%)` over **0.8s** `ease-in-out`
- `animation-delay: 3.8s / 4.0s / 4.2s` (after icon nudge)
- `animation-iteration-count: 1`
- `overflow: hidden` on `.explore-hint` to clip the sweep
- `pointer-events: none` on pseudo

**Edge cases**:
- `.explore-hint` currently has no `position` set — needs `position: relative` and `overflow: hidden` added
- iOS Safari: No backdrop-filter involved here, pure transform animation → safe
- The shimmer pseudo won't interfere with text color transitions since it's a separate layer

---

### 4. Hover Enhancement: Subtle Icon Rotation
**What**: On hover, rotate the icon SVG 8-12 degrees for a playful "alive" feel. Stacks with the existing parent `translateY(-3px)`.

**Spec**:
- `.service-item:hover .icon svg { transform: rotate(8deg); }` (combine with existing per-icon transforms)
- `transition: transform 0.3s ease-out` (already exists on icon SVGs)
- Different rotation per icon type for personality:
  - Guided (sun): `rotate(15deg)` — sun rotates naturally
  - Booking (lock): `rotate(-5deg)` — subtle tilt
  - Seasonal (calendar): `rotate(5deg)` — slight lean

**Edge cases**:
- Each `.service-item[data-panel] .icon svg` already has individual `transform: translateY()` offsets. Must combine: `transform: translateY(0.2px) rotate(15deg)` etc.
- Touch devices: The existing hover already has sticky-hover issue on mobile. This doesn't make it worse — same `transition: all 0.3s` applies. Consider: wrapping hover-only styles in `@media (hover: hover)` for all hover effects (improvement but technically a design logic change, so flag it).

---

### 5. Repeat Nudge on Idle (optional, conservative)
**What**: If the user hasn't interacted with any service button after 12 seconds on the landing page, replay the icon nudge + shimmer sequence once more.

**Spec**:
- JavaScript: `setTimeout` at 12s, check if any `.service-item.active` exists
- If not, programmatically add `.nudge-repeat` class to trigger the animation again
- Remove class after animation ends (`animationend` event)
- Only fires **once** — not a loop

**Edge cases**:
- `setTimeout` continues in background tabs (unlike rAF). Must guard with `document.hidden` check:
  ```js
  setTimeout(() => { if (!document.hidden && !anyServiceActive()) replayNudge(); }, 12000);
  ```
- If user switched to a tour panel and back before 12s, `activeSlideId` will be `content-default` again — check this to avoid replaying during an active tour view
- If intro hasn't finished (user didn't skip), delay should be calculated from `revealLanding()` call, not page load

---

## Accessibility — `prefers-reduced-motion`

The existing media query at line 589 needs to be extended:

```css
@media (prefers-reduced-motion: reduce) {
  /* ...existing rules... */
  .service-item::after { animation: none; opacity: 0; }
  .service-item .icon { animation: none; }
  .explore-hint::before { animation: none; display: none; }
}
```

All proposed animations are **non-essential, decorative motion** — content remains fully accessible without them.

---

## Touch Device Considerations

The existing code applies hover effects universally. The new animations should follow the same pattern for consistency (no `@media (hover: hover)` wrapping unless we decide to refactor all hover styles — separate task).

The one-shot entry animations (nudge, shimmer) work identically on touch and mouse devices since they're time-based, not hover-based.

---

## Performance Budget

| Technique | Properties Animated | GPU? | Ongoing? |
|-----------|-------------------|------|----------|
| Glow pulse `::after` | `opacity` | Yes | Yes (infinite, but trivial) |
| Icon nudge | `transform` | Yes | No (one-shot) |
| Shimmer sweep `::before` | `transform` | Yes | No (one-shot) |
| Hover rotation | `transform` | Yes | No (hover only) |
| Idle re-nudge | `transform` | Yes | No (one-shot) |

Total ongoing animations: **3 pseudo-element opacity cycles** — well within the 1-2 concurrent animation budget for mobile. All use GPU-accelerated properties only. No `box-shadow`, `filter`, `width`, or layout-triggering properties.

---

## Implementation Order

1. Glow pulse `::after` (highest impact, always visible)
2. Icon micro-bounce (one-shot attention grab)
3. "Explore" shimmer sweep (reinforces the CTA)
4. Hover icon rotation (enhances interactivity feel)
5. Idle re-nudge (safety net for hesitant users)

Each step is independently valuable and can be shipped alone.

---

## What This Plan Does NOT Change

- No new HTML elements (only CSS pseudo-elements)
- No layout shifts (all effects use `transform`/`opacity` on absolutely positioned layers)
- No color, font, size, spacing, or design token changes
- No structural changes to the DOM
- The existing `.service-item` hover behavior stays exactly the same
- The existing `fadeUp` entrance animation stays the same
