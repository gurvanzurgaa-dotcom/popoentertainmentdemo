# DESIGN.md — POPO Entertainment

> One floor for the whole night: a neon-lit lounge where the menu reads like a premium bar card and every number on the page is real.

## 1. Visual Theme & Atmosphere

**Style**: Dark Nightlife Editorial (Dark Editorial seed, pushed towards neon lounge)
**Keywords**: after-hours, neon glow, cold glass, felt green, brand red, printed-menu yellow, confident type, tactile
**Tone**: premium, warm, playful at the edges — NOT cyberpunk, NOT casino-gaudy, NOT a generic SaaS dark mode
**Feel**: walking out of a lift into a dim room where the bar backlight hits a cold pint and the pool balls click.

**Interaction Tier**: L3 Immersive (one WebGL hero scene, one sticky pin/swap story, scroll reveals, cursor spotlight on hover devices)
**Dependencies**: three.js r170 (hero only, ES module from jsDelivr). Everything else is vanilla CSS + JS (IntersectionObserver, rAF). No GSAP, no Lenis.

**Brand anchors (from the printed menu, never changed):**
- Lockup: white pill, black `ENTERTAINMENT` (spaced caps) above red `POPO` (Playfair Display).
- Slogan colours: taste = caramel, play = red, enjoy = lime.
- Menu page header: giant black POPO on paper + yellow handwritten circle.
- Brand logos: real files in `logos/` only. If no real logo exists, show the name in plain type.

## 2. Color Palette & Roles

```css
:root {
  /* Backgrounds */
  --bg: #07060d;            --bg-rgb: 7, 6, 13;
  --surface: #121022;       --surface-rgb: 18, 16, 34;
  --surface-alt: #0c0a17;
  --surface-hover: #1a1730;
  --paper: #f4f2ec;         /* printed-menu paper */

  /* Borders */
  --border: #252040;
  --border-hover: #3b3462;

  /* Text */
  --text: #f5f2ff;          --text-rgb: 245, 242, 255;
  --text-secondary: #b3aecb;
  --text-tertiary: #7d7899;
  --ink: #111111;           /* text on paper / yellow */
  --white: #ffffff;

  /* Accent — brand */
  --accent: #ff2d55;        --accent-rgb: 255, 45, 85;   /* CTAs */
  --accent-hover: #ff5273;
  --brand-red: #d7192d;     --brand-red-rgb: 215, 25, 45; /* POPO wordmark */
  --yellow: #f3d630;        --yellow-rgb: 243, 214, 48;  /* selection, prices */
  --taste: #e8a96f;  --play: #ff4d5e;  --enjoy: #a6e05a;

  /* Atmosphere (lights only, never for text blocks) */
  --neon-pink: #ff3fa4;     --pink-rgb: 255, 63, 164;
  --neon-cyan: #2ee6ff;     --cyan-rgb: 46, 230, 255;
  --amber: #ffb347;

  /* Illustration */
  --felt: #0f6b3f; --felt-dark: #0a4a2c; --rail: #5a3216; --pocket: #050505;

  /* Semantic */
  --success: #2bd97c;       --success-rgb: 43, 217, 124;
  --error: #ff5468;
  --warning: #ffb547;
}
```

**Color Rules:**
- Every colour in components comes from a variable; translucency uses `rgba(var(--x-rgb), a)` or `color-mix()`.
- Red (`--accent`) is for actions (call, explore). Yellow (`--yellow`) is for "selected" and prices. Never two filled red buttons side by side.
- Neon pink / cyan only appear as light: glows, aurora blobs, 3D rim lights.
- Body copy never sits on a neon colour; it sits on `--bg` / `--surface`.

## 3. Typography Rules

**Font Stack:**
```css
@import url('https://fonts.googleapis.com/css2?family=Unbounded:wght@500;700;900&family=Manrope:wght@400;500;600;700;800&family=Caveat:wght@600;700&family=Playfair+Display:ital,wght@0,700;0,900;1,600&display=swap');
--font-display: "Unbounded", system-ui, sans-serif;
--font-body: "Manrope", system-ui, -apple-system, "Segoe UI", sans-serif;
--font-brand: "Playfair Display", Georgia, serif;
--font-script: "Caveat", "Segoe Script", cursive;
```
All four families include Cyrillic, so Mongolian text renders in the same fonts.

| Role | Font | Size | Weight | Line Height | Letter Spacing |
|------|------|------|--------|-------------|----------------|
| Hero H1 (POPO) | Playfair Display | clamp(104px, 19vw, 248px) | 900 | .82 | .01em |
| Hero eyebrow (ENTERTAINMENT) | Manrope | clamp(12px, 1.6vw, 18px) | 800 | 1 | .5em |
| Section H2 | Unbounded | clamp(34px, 5.6vw, 72px) | 900 | .98 | -.02em |
| H3 | Unbounded | 20–28px | 700 | 1.15 | -.01em |
| Body | Manrope | 16–18px | 500 | 1.6 | — |
| Label / eyebrow | Manrope | 12px | 800 | 1 | .22em, uppercase |
| Numbers / prices | Unbounded | 15–64px | 700 | 1 | -.01em, tabular-nums |
| Script notes | Caveat | 24–32px | 700 | .95 | — |

**Typography Rules:**
- Headings ≥ 700. Prices always Unbounded with `font-variant-numeric: tabular-nums`.
- Mongolian body copy: line-height ≥ 1.6.
- **NEVER use**: Inter, Roboto, Arial as a visible face; Comic-style fonts; more than these four families.

**Text Decoration (per text-decoration-rules):**
- Hero POPO: no gradient (brand red is fixed); subtle glow `text-shadow: 0 0 60px rgba(var(--brand-red-rgb), .45)` (dark bg, ≥ 80px).
- ENTERTAINMENT: ShinyText sweep (light band passing over).
- Section H2: no gradient; one `<em>` keyword per heading in `--yellow`.
- Body: no decoration.

## 4. Component Stylings

### Buttons
```css
.btn{display:inline-flex;align-items:center;gap:10px;min-height:48px;padding:0 24px;border-radius:999px;
  font:800 15px/1 var(--font-body);text-decoration:none;cursor:pointer;border:1px solid transparent;
  transition:transform .25s cubic-bezier(.2,.8,.2,1),background .2s,box-shadow .2s,border-color .2s,color .2s}
.btn-primary{background:var(--accent);color:var(--white);box-shadow:0 10px 30px -8px rgba(var(--accent-rgb),.7)}
.btn-primary:hover{background:var(--accent-hover);box-shadow:0 14px 40px -8px rgba(var(--accent-rgb),.9)}
.btn-primary:active{transform:scale(.97)}
.btn-ghost{background:rgba(var(--text-rgb),.04);border-color:var(--border);color:var(--text)}
.btn-ghost:hover{border-color:var(--border-hover);background:rgba(var(--text-rgb),.08)}
.btn:focus-visible{outline:2px solid var(--yellow);outline-offset:3px}
.btn:disabled,.btn[aria-disabled="true"]{opacity:.45;pointer-events:none;box-shadow:none}
```

### Cards (menu item — SpotlightCard)
```css
.item{position:relative;border-radius:20px;background:linear-gradient(160deg,var(--surface-hover),var(--surface));
  border:1px solid var(--border);transition:border-color .2s,transform .3s,box-shadow .3s}
.item::before{/* spotlight */content:"";position:absolute;inset:0;border-radius:inherit;pointer-events:none;opacity:0;
  background:radial-gradient(260px circle at var(--mx,50%) var(--my,50%),rgba(var(--yellow-rgb),.14),transparent 65%);transition:opacity .25s}
.item:hover{border-color:var(--border-hover);transform:translateY(-3px)}
.item:hover::before{opacity:1}
.item.on{border-color:var(--yellow);box-shadow:0 0 0 1px var(--yellow),0 18px 50px -18px rgba(var(--yellow-rgb),.55)}
.item-hit:focus-visible{outline:2px solid var(--yellow);outline-offset:2px}
```

### Navigation
```css
.nav{position:fixed;inset:0 0 auto;z-index:50;transition:background .3s,border-color .3s,height .3s}
.nav.scrolled{background:rgba(var(--bg-rgb),.72);backdrop-filter:blur(12px) saturate(140%);border-bottom:1px solid var(--border)}
.nav-links a{color:var(--text-secondary);position:relative}
.nav-links a::after{content:"";position:absolute;left:14px;right:14px;bottom:6px;height:2px;background:var(--yellow);transform:scaleX(0);transition:transform .3s}
.nav-links a:hover,.nav-links a[aria-current="true"]{color:var(--text)}
.nav-links a[aria-current="true"]::after{transform:scaleX(1)}
```
Mobile: burger → full-screen overlay with staggered large links.

### Links
```css
.link{color:var(--text);text-decoration:none;background:linear-gradient(var(--yellow),var(--yellow)) 0 100%/0 2px no-repeat;transition:background-size .3s}
.link:hover{background-size:100% 2px}
```

### Tags / Badges
```css
.tag{display:inline-flex;align-items:center;gap:8px;padding:7px 14px;border-radius:999px;font:800 11px/1 var(--font-body);
  letter-spacing:.22em;text-transform:uppercase;border:1px solid rgba(var(--cyan-rgb),.35);color:var(--neon-cyan)}
.tag .dot{width:8px;height:8px;border-radius:50%;background:var(--success);box-shadow:0 0 12px var(--success)}
```

### Tabs (menu sections)
Pill tabs on a scrollable rail with a sliding yellow indicator behind the active tab; arrow keys move between tabs.

### Search field
```css
.search{min-height:48px;border-radius:999px;border:1px solid var(--border);background:var(--surface)}
.search:focus-within{border-color:var(--yellow);box-shadow:0 0 0 4px rgba(var(--yellow-rgb),.15)}
```

### Drawer / Bottom sheet
Right drawer on desktop, bottom sheet on mobile; backdrop `rgba(var(--bg-rgb),.6)`; Esc closes; focus moves into the panel and back out.

## 5. Layout Principles

**Container:** max-width 1280px; padding `clamp(16px, 4vw, 32px)`. Narrow text: 720px.

**Spacing Scale:** 4 · 8 · 12 · 16 · 24 · 32 · 48 · 64 · 96 · 140
- Section padding: `clamp(80px, 11vw, 140px) 0`
- Component gap: 16–24px
- Card padding: 18–28px

**Grid:**
```css
.menu-layout{display:grid;grid-template-columns:minmax(0,1fr) 420px;gap:28px;align-items:start}
.grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(232px,1fr));gap:14px}
.bento{display:grid;grid-template-columns:1.15fr 1fr;grid-template-rows:auto auto;gap:20px}
.bento .billiards{grid-row:span 2}
```

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat | border `--border`, no shadow | tabs, contact cards |
| Subtle | `0 1px 0 rgba(var(--text-rgb),.04) inset` | surfaces |
| Elevated | `0 24px 60px -24px rgba(0,0,0,.7)` | detail panel, bento cards |
| Glow | `0 0 0 1px var(--yellow), 0 18px 50px -18px rgba(var(--yellow-rgb),.55)` | selected item |
| Overlay | `0 -20px 80px rgba(0,0,0,.6)` + backdrop | drawer, sheet, mobile nav |

## 7. Animation & Interaction

**Motion Philosophy**: transform + opacity only; one big moment per screen; everything settles within 1s.
**Tier**: L3

### Signature moments (8)
1. **Hero** — WebGL cold pint + pool balls (three.js, paused via IntersectionObserver when off-screen) · POPO letters rise through a mask (SplitText) · ENTERTAINMENT ShinyText · cursor spotlight.
2. **First scroll** — giant TASTE · PLAY · ENJOY marquee whose speed and skew follow scroll velocity, plus a real-logo loop.
3. **Manifesto** — words light up as you scroll (ScrollReveal) + count-up stats.
4. **Pin / swap story** — "taste / play / enjoy" pinned on the left, right side swaps scenes (menu picks, pool table + equaliser, live 24-hour dial).
5. **Menu** — SpotlightCards, sliding tab indicator, detail panel swap animation, instant search.
6. **My picks** — add button ClickSpark + badge bump, slide-in drawer with running total.
7. **Play bento** — 3D tilt cards + live price estimator with rolling total.
8. **Visit** — magnetic call buttons; copy-address sparkle (the hidden detail); footer "last call" reveal.

### Entrance Animation
```css
@keyframes rise{from{transform:translateY(105%)}to{transform:none}}
@keyframes fadeUp{from{opacity:0;transform:translateY(24px)}to{opacity:1;transform:none}}
[data-reveal]{opacity:0;transform:translateY(28px);transition:opacity .8s cubic-bezier(.2,.8,.2,1) var(--d,0s),transform .8s cubic-bezier(.2,.8,.2,1) var(--d,0s)}
[data-reveal].in{opacity:1;transform:none}
```

### Scroll Behavior
- IntersectionObserver adds `.in` to `[data-reveal]` and split H2s (chars rise with 18ms stagger).
- One rAF-throttled scroll loop drives: progress bar, nav scrolled state, manifesto words, pin progress, marquee velocity, hero fade.

### Hover & Focus States
Every interactive element: hover change (colour/border/translate) + `:focus-visible` yellow 2px ring. Touch devices get no tilt / magnet.

### Reduced Motion
```css
@media (prefers-reduced-motion: reduce){
  *,*::before,*::after{animation-duration:.01ms!important;animation-iteration-count:1!important;transition-duration:.01ms!important}
  [data-reveal]{opacity:1;transform:none}
  .marquee .track{animation:none}
  .pin{height:auto}.pin-stage{position:static;height:auto}
  html{scroll-behavior:auto}
}
```
The WebGL scene renders a single still frame under reduced motion.

## 8. Do's and Don'ts

### Do
- Use real menu data for every price, count and highlight (it all comes from `MENU` and `RES`).
- Keep the printed-menu identity: lockup, yellow script circle, taste/play/enjoy colours.
- Show bookings as phone calls (7780-8800 / 9536-8272).
- Pause WebGL when the hero is off-screen; cap pixel ratio at 2 (1.5 on phones).
- Give every card, tab, chip and button a visible focus state and ≥ 44px touch target.
- Keep "My picks" clearly labelled as a list to show staff, not an online order.

### Don't
- ❌ Fake, redraw or "approximate" any brand logo — real files or plain text only.
- ❌ Re-add the 3D product viewer, the monthly tournament or online booking (removed by the owner).
- ❌ Stock photos that pretend to be the venue.
- ❌ `filter: blur()` on moving elements; `backdrop-filter` above 14px.
- ❌ More than one WebGL scene or more than one pinned section.
- ❌ Hard-coded hex colours outside `:root` (three.js pool-ball colours are data, not UI).
- ❌ Emoji as icons — inline SVG only.
- ❌ Gradient text on body copy or prices.
- ❌ Scroll-jacking / smooth-scroll libraries.
- ❌ Hiding item details on mobile — they open in a bottom sheet.

## 9. Responsive Behavior

| Name | Width | Key Changes |
|------|-------|-------------|
| Desktop | > 1080px | menu list + sticky detail panel; bento 2 columns; pin story active |
| Tablet | 860–1080px | detail becomes bottom sheet; bento stacks to 1 column |
| Mobile | < 860px | burger nav overlay; pin story becomes stacked scenes; WebGL at 62% scale behind hero copy |
| Small | < 560px | menu header shrinks; tabs scroll horizontally; toolbar stacks |

**Touch Targets:** minimum 44 × 44px.
**Collapsing Strategy:** nav links → burger; detail panel → bottom sheet; picks drawer → bottom sheet; bento → single column; pin → static stack.

```css
@media (max-width:1080px){.menu-layout{grid-template-columns:minmax(0,1fr)}.bento{grid-template-columns:1fr}.bento .billiards{grid-row:auto}}
@media (max-width:860px){.nav-links,.nav .btn-call{display:none}.burger{display:grid}.pin{height:auto}.pin-stage{position:static;height:auto}}
@media (max-width:560px){.page-head{height:120px}.toolbar{flex-direction:column;align-items:stretch}}
```
