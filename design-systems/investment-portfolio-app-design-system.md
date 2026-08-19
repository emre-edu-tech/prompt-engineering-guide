# MP Investment Portfolio — Visual Design System

A design spec for `finance.media-pons.de`, built for Tailwind CSS v3. This
document is meant to be handed directly to a coding agent — it gives exact
tokens, component markup patterns, and behavior, not just mood.

## 0. Direction, in one paragraph

Dark, ink-navy base (not pure black) with a **gold** accent, not the default
neon-on-black. Gold is the deliberate choice here, not decoration: it's
already your first widget (`Gold XAU→USD`), and gold is the traditional
household savings asset in Turkey — this app is a personal portfolio tool,
not a generic SaaS dashboard, and the palette should say so. Since the app shows current price only — no change percentage or direction — there is no
price-direction graphic or change indicator to design around; each card's job is simply
to present one current price cleanly, backed by a small always-visible "Last updated"
timestamp caption in the faint text color (§4.1). Numbers are still set in a monospace face with tabular
figures so digits align and don't jitter when the value ticks. The existing
"Next update in --:--" countdown becomes the
page's signature element: a small ring-and-pulse indicator, used consistently
across nav and cards, instead of being thrown away as a plain timer.

---

## 1. Design tokens

### 1.1 Color

| Name | Hex | Role |
|---|---|---|
| Ink | `#0B1220` | Page background |
| Surface | `#141F35` | Card / panel background |
| Surface Raised | `#1B2740` | Hover state, nested surfaces (e.g. table row hover) |
| Bullion Gold | `#C9A227` | Brand accent — active nav, focus rings, pulse ring, links |
| Live Green | `#22C55E` | Live/connection status indicator (nav dot) |
| Mist | `#E8ECF4` | Primary text |

Derived, not separately named — implement as opacity/utility variants of Mist and Ink:
- Muted text: `Mist` at ~60% → or flat `#8A93A6`
- Faint text (timestamps, captions): `#5B6478`
- Hairline borders: `rgba(255,255,255,0.06)` (on dark surfaces), `rgba(255,255,255,0.1)` for hover/emphasis

Do **not** use Tailwind's default `slate`/`gray`/`blue`/`green`/`red` scales directly —
define these as named tokens (below) so the palette stays intentional and
isn't quietly diluted by a stray `bg-blue-500` somewhere in the codebase.

### 1.2 Typography

Three roles, used with restraint:

- **UI / body — Inter.** All labels, nav, buttons, paragraph text.
- **Data — IBM Plex Mono.** Every number: prices, tickers, timestamps. Non-negotiable — this is what makes the page scannable
  and gives it a "trading terminal" legibility instead of a generic card
  layout. Use `font-variant-numeric: tabular-nums` (or the Plex Mono tabular
  figures) everywhere a price can update, so digit width never shifts.
- **Display — Space Grotesk.** Page titles only ("Live Prices", "Commodities",
  etc.) — used sparingly, not for body copy or card content.

Load via Google Fonts or self-host, e.g.:
```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600&family=IBM+Plex+Mono:wght@500;600&family=Space+Grotesk:wght@500;600&display=swap" rel="stylesheet">
```

Type scale:

| Use | Size / weight |
|---|---|
| Page title (Space Grotesk) | `text-2xl md:text-3xl font-medium` |
| Page subtitle | `text-sm text-muted` (Inter) |
| Card label (ticker name) | `text-xs font-medium uppercase tracking-wide` (Plex Mono) |
| Card ticker symbol | `text-[11px] text-faint` (Plex Mono) |
| Card price | `text-2xl md:text-3xl font-semibold tabular-nums` (Plex Mono) |
| Card last-updated caption | `text-[11px] text-faint` (Plex Mono) — e.g. "Last updated: Aug 18, 2026, 2:02 PM" |
| Nav links | `text-sm font-medium` (Inter) |

### 1.3 Spacing, radius, shadow

- Card radius: `rounded-xl` (12px) — deliberate, not the sharper `rounded-md`
  or the softer `rounded-2xl`. Consistent everywhere; don't mix radii.
- Card padding: `p-5` (mobile) / `p-6` (md+)
- Grid gap: `gap-4 md:gap-5`
- Card shadow: none by default (flat, sits on the ink background via the
  surface color + hairline border, not a shadow). On hover only:
  `shadow-[0_8px_24px_-12px_rgba(0,0,0,0.5)]`
- Border: `border border-white/[0.06]` default, `border-white/10` on hover

---

## 2. Layout

### 2.1 Page shell

```
┌──────────────────────────────────────────────────────────────┐
│ ◆ MP Portfolio     Home  Commodities  Currencies  Stocks   ●Live│  ← sticky nav
├──────────────────────────────────────────────────────────────┤
│                                                                  │
│  Live Prices                                    ◔ next: 0:42   │  ← page header
│  My Portfolio of Live prices                                    │
│                                                                  │
│  ┌────────────┐ ┌────────────┐ ┌────────────┐ ┌────────────┐ │
│  │ ◔ GOLD     │ │ ◔ USD/TRY  │ │ ◔ NVDA     │ │ ◔ XU100    │ │
│  │ XAU → USD  │ │            │ │ NASDAQ     │ │ BIST       │ │
│  │            │ │            │ │            │ │            │ │
│  │ $2,431.20  │ │ ₺34.18     │ │ $128.44    │ │ 9,842.10   │ │
│  │ ▲ +0.42%   │ │ ▼ -0.11%   │ │ ▲ +1.8%    │ │ ▲ +0.63%   │ │
│  │ ⌇⌇╱⌇╱⌇⌇   │ │ ╲⌇⌇⌇⌇⌇   │ │ ⌇╱╱╱⌇     │ │ ╱⌇╱⌇⌇     │ │
│  │ upd 14:02  │ │ upd 14:02  │ │ upd 14:02  │ │ upd 14:02  │ │
│  └────────────┘ └────────────┘ └────────────┘ └────────────┘ │
│                                                                  │
├──────────────────────────────────────────────────────────────┤
│  Copyright © 2026                                                │
└──────────────────────────────────────────────────────────────┘
```

- Page background: `bg-ink` full bleed.
- Container: `max-w-6xl mx-auto px-4 md:px-8`
- Nav: `sticky top-0 z-20 backdrop-blur bg-ink/80 border-b border-white/[0.06]`
- Home grid: `grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4 md:gap-5`
- Category pages (Commodities / Currencies / Stocks): same card component,
  grid sizes to however many instruments exist —
  `grid-cols-1 sm:grid-cols-2 lg:grid-cols-3` reads better once a category
  has more than 4 items (avoids 1 lonely stretched card on `/commodities`
  today with just Gold in it — cap card width with `max-w-sm` when the grid
  has fewer items than columns, or just let `sm:grid-cols-2` hold it).

### 2.2 Category page header

Each category page keeps the same header pattern as home: `text-2xl` title
(Space Grotesk) + one-line description (Inter, muted) + the pulse/countdown
in the top-right corner of the header — not just buried inside each card.

---

## 3. Signature element — the pulse ring

Replaces the current plain "Next update in --:--" text. A thin circular
progress ring around a small dot, gold, ticking down each second over the
update interval, resetting (with a brief flash) when new data lands. Used in
two places: small (20px) in the top-right corner of every instrument card,
and slightly larger (28px) once in the page header showing the *global*
next-refresh time.

```html
<!-- small, per-card version -->
<svg width="20" height="20" viewBox="0 0 20 20" class="shrink-0">
  <circle cx="10" cy="10" r="8" fill="none" stroke="rgba(201,162,39,0.2)" stroke-width="2"/>
  <circle cx="10" cy="10" r="8" fill="none" stroke="#C9A227" stroke-width="2"
          stroke-linecap="round" stroke-dasharray="50.24"
          style="stroke-dashoffset: var(--ring-offset); transform: rotate(-90deg); transform-origin: 50% 50%; transition: stroke-dashoffset 1s linear;"/>
  <circle cx="10" cy="10" r="2" fill="#C9A227" class="animate-pulse-dot"/>
</svg>
```

Behavior:
- `--ring-offset` goes from `0` (just refreshed, ring full) to `50.24`
  (circumference — ring empty, refresh imminent) over the update interval.
  Update it once per second from JS; the CSS transition smooths it.
- On refresh: briefly set the ring to full gold opacity + scale `1.15` for
  150ms, then settle back — a small "tick" so the user visually registers
  new data landed, not just a number that silently changed.
- The center dot pulses gently (opacity 100% → 55%, 2s ease-in-out infinite)
  independent of the countdown — it's the "alive" indicator, separate from
  "time until refresh."
- Respect `prefers-reduced-motion`: keep the ring (it's informational, not
  decorative) but drop the dot pulse and the refresh scale-flash.

---

## 4. Components

### 4.1 Instrument card

```html
<article class="rounded-xl border border-white/[0.06] bg-surface p-5 md:p-6
                 transition hover:border-white/10 hover:shadow-[0_8px_24px_-12px_rgba(0,0,0,0.5)]">
  <header class="flex items-start justify-between">
    <div>
      <p class="font-mono text-xs uppercase tracking-wide text-muted">Gold</p>
      <p class="font-mono text-[11px] text-faint mt-0.5">XAU &rarr; USD</p>
    </div>
    <!-- pulse ring, see §3 -->
  </header>

  <p class="font-mono text-2xl md:text-3xl font-semibold text-mist tabular-nums mt-4">
    $2,431.20
  </p>

  <p class="font-mono text-[11px] text-faint mt-2">Last updated: Aug 18, 2026, 2:02 PM</p>

</article>
```

States:
- **Loading:** replace the current price with a shimmer skeleton
  (`bg-white/5 animate-pulse rounded` block matching the price dimensions).
  Keep label + pulse ring visible — those aren't loading. The last-updated
  caption stays in place showing `Last updated: --`.
- **Last-updated caption (always visible):** every card carries a small
  mono timestamp caption under the price in `text-faint` (#5B6478), the
  palette's designated timestamp color (see §1.1). It shows the upstream
  quote time when the feed provides one, otherwise the time the client
  received the data. It is informational, not a status light — leave it
  quiet and neutral in every state.
- **Price refresh:** when the current price updates, a subtle neutral card
  refresh treatment may be used to make the update perceptible. Do not imply
  upward/downward direction or price change magnitude. The caption updates
  with the new quote time.
- **Stale / disconnected:** if the feed hasn't updated past a reasonable
  threshold, the pulse ring dot goes from gold to `text-faint` gray and stops
  pulsing, and a small caption appears under the ticker: `text-[11px]
  text-faint` reading "Last updated 4m ago" — plain statement, not an
  apology, no red error styling for something that isn't yet an error.

### 4.2 Nav bar

```html
<nav class="sticky top-0 z-20 backdrop-blur bg-ink/80 border-b border-white/[0.06]">
  <div class="max-w-6xl mx-auto px-4 md:px-8 h-14 flex items-center justify-between">
    <a href="/" class="font-mono text-sm font-medium text-mist">MP Portfolio</a>
    <div class="hidden sm:flex items-center gap-6">
      <a class="text-sm font-medium text-muted hover:text-mist transition data-[active]:text-gold data-[active]:border-b-2 data-[active]:border-gold">Home</a>
      <!-- Commodities / Currencies / Stocks, same pattern -->
    </div>
    <div class="flex items-center gap-1.5 text-xs font-mono text-muted">
      <span class="w-1.5 h-1.5 rounded-full bg-signal-green"></span> Live
    </div>
  </div>
</nav>
```

Active link: gold text + a 2px gold underline (`border-b-2 border-gold`),
not a filled pill — keep the nav flat and quiet, the gold accent is spent on
the underline and the pulse ring, not scattered everywhere.

### 4.3 Page header

```html
<div class="flex items-end justify-between mt-8 mb-6">
  <div>
    <h1 class="font-display text-2xl md:text-3xl font-medium text-mist">Live Prices</h1>
    <p class="text-sm text-muted mt-1">My Portfolio of Live prices.</p>
  </div>
  <!-- larger pulse ring + "next: 0:42" label, font-mono text-xs text-muted -->
</div>
```

### 4.4 Empty / error states

Written in the interface's voice — state what happened and what's next, no
apology, no raw exception text:

- No data yet on first load: skeleton cards (§4.1), not an empty-state
  message — the layout itself signals "loading."
- Feed unreachable: `Couldn't reach the price feed. Retrying in 12s.` —
  small, `text-sm text-muted`, centered where the grid would be, with the
  pulse ring's dot shown gray/static per §4.1.
- A single instrument's feed fails while others succeed: that one card shows
  the stale-state pattern (§4.1), the rest of the grid is unaffected.

### 4.5 Footer

Keep it minimal — `text-xs text-faint`, centered, current
`Copyright © 2026` content is fine as-is; no redesign needed here.

---

## 5. Motion

- Card entrance on page load: fade + `translateY(4px→0)`, ~200ms, staggered
  ~40ms per card — subtle, not a bouncy reveal.
- Price-tick flash: background tint, 500ms ease-out (§4.1).
- Pulse ring: see §3.
- Hover: border + shadow transition, 150ms.
- All of the above wrapped so `prefers-reduced-motion: reduce` disables the
  entrance stagger, the tick flash, and the dot pulse — keep the ring
  progress itself (it conveys real information, not decoration).

---

## 6. Accessibility

- Text contrast: `Mist` on `Ink`/`Surface` should remain clear at these sizes —
  don't lighten/darken the text tokens without rechecking.
- Focus-visible: `outline-2 outline-offset-2 outline-gold` on all
  interactive elements (nav links, any buttons) — don't suppress the
  default outline without replacing it.
- Countdown ring has an accessible label even though it's visual — e.g.
  `aria-label="Next update in 42 seconds"` on the wrapping element, updated
  as the value changes (or use `aria-live="polite"` region for the numeric
  version if you keep a text fallback for screen readers).

---

## 7. Tailwind v3 config additions

```js
// tailwind.config.js
module.exports = {
  content: ['./**/*.{html,js}'], // adjust to actual project structure
  theme: {
    extend: {
      colors: {
        ink: '#0B1220',
        surface: '#141F35',
        'surface-raised': '#1B2740',
        gold: {
          DEFAULT: '#C9A227',
          light: '#E0BE55',
          dark: '#9C7D1D',
        },
        mist: '#E8ECF4',
        muted: '#8A93A6',
        faint: '#5B6478',
      },
      fontFamily: {
        sans: ['Inter', 'ui-sans-serif', 'system-ui', 'sans-serif'],
        mono: ['"IBM Plex Mono"', 'ui-monospace', 'SFMono-Regular', 'monospace'],
        display: ['"Space Grotesk"', 'Inter', 'sans-serif'],
      },
      boxShadow: {
        card: '0 8px 24px -12px rgba(0,0,0,0.5)',
      },
      keyframes: {
        'pulse-dot': {
          '0%, 100%': { opacity: 1 },
          '50%': { opacity: 0.55 },
        },
      },
      animation: {
        'pulse-dot': 'pulse-dot 2s ease-in-out infinite',
      },
    },
  },
  plugins: [],
};
```

Body default, in your base CSS:
```css
body {
  background-color: theme('colors.ink');
  color: theme('colors.mist');
  font-family: theme('fontFamily.sans');
}
```

---

## 8. Explicitly avoid

- Tailwind's default `blue-500`/`indigo-600` as a primary/CTA color —
  everything accent-colored should be gold.
- Mixing border radii across cards, buttons, and the nav — one radius
  (`rounded-xl`) everywhere a container needs one.
- Gradients or glow/blur effects on cards — flat surfaces only, the pulse
  ring is the one animated/glowing-adjacent element and it should stay that
  way by contrast.
- Any non-monospace font for a number that can change at runtime.

---

## 9. Suggested implementation order

1. Tailwind config tokens (§7) + font loading.
2. Instrument card component (§4.1) incl. loading skeleton.
3. Pulse ring (§3) wired to the existing update-interval logic — this
   replaces the current `--:--` text directly.
4. Nav bar (§4.2) + page header (§4.3).
5. Price-tick flash + card entrance motion (§5).
6. Stale/error states (§4.4) last — lowest visual risk, easiest to layer on
   once the base card is solid.
