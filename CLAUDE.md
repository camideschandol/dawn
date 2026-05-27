# CLAUDE.md — Poivre Paris

Working context for Claude on the Poivre Paris Shopify build.
Read this before touching the repo.

---

## What this project is

A Shopify storefront for **Poivre Paris** — a Paris-rooted home and tableware
brand selling quiet-luxury objects (pepper mills, carafes, glassware, candles,
linens) via **curated drops**, not a permanent catalog. The site operates as
an editorial publisher with a tabletop business attached.

**The codebase is a fork of Shopify's Dawn theme, v15.4.1.**

Two visual references the design is built against:

- **Hyeja Skincare** (`hyejaskincare.com`) — homepage and footer structure.
- **Lesse** (`lesseofficial.com`) — product, about, journal, and utility pages.

When a spec says "mirrors Hyeja" or "mirrors Lesse," it means layout
proportions, type sizes, and section composition — not their copy or color.

Source documents (at the repo root):

- [brand positioning.md](brand%20positioning.md) — brand foundations, voice, palette.
- [PRD.md](PRD.md) — full PRD, the source of truth for behavior.

If a question about behavior, layout, or content can be answered by the PRD,
quote the PRD section (e.g. "PRD §6.1"). Don't invent specs.

---

## What I'm building (v1 scope)

A Shopify storefront with these page types:

- Homepage (7 sections — PRD §5)
- Product page (6 sections — PRD §6)
- Collection pages (single template, 6 URLs — PRD §7)
- About page (5 sections — PRD §8)
- Journal index + article (PRD §9)
- Utility pages — Shipping & Returns, FAQs (shared template — PRD §10)
- Stub pages — Contact, Privacy Policy, Terms & Conditions (PRD §11)

**Out of scope for v1** (do not build, even if asked unless the user confirms
scope has changed):

- Subscriptions / recurring purchase
- Multi-currency / multi-region routing beyond Shopify defaults
- Loyalty / referral platform integration
- Skin-analysis or quiz functionality (the Lesse "Skin Analysis" tab is
  intentionally not replicated)
- Stockist locator
- Any non-English localization
- Filter UI on collection pages (v2 once SKU count grows)

---

## Theme foundation rules

- Fork is **Dawn 15.4.1**. Stay on this version unless the user explicitly
  approves an upgrade — Dawn's section schema changes between majors.
- **Preserve Dawn's file structure**: `layout/`, `templates/`, `sections/`,
  `snippets/`, `assets/`, `config/`, `locales/`.
- **Override Dawn's default sections rather than building parallel ones**
  where possible. Net-new sections only when Dawn has no equivalent
  (Hyeja-style featured-editorial, Lesse-style product page blocks,
  journal-index, etc.).
- **Use Shopify section schema JSON** for every editorial block — the brand
  team must edit content through the theme customizer, not the code.
- **Liquid first.** Avoid heavy JS frameworks. Dawn ships with minimal JS;
  follow that pattern. Web components are fine where Dawn already uses them.

---

## Design tokens

Define these as CSS custom properties in `assets/base.css` (or a dedicated
`assets/tokens.css`) and reference them everywhere. **Do not hardcode hex
values in sections or snippets** — always go through the token.

Approximate values (confirm with the PRD's color palette table — the exact
hexes are in PRD §02 and the Brand Positioning Doc §07):

```css
:root {
  /* Surfaces */
  --bg-primary:    #FFFFFF;  /* white — default page background */
  --bg-secondary:  #FAF6F0;  /* warm off-white — accent sections, popup */

  /* Ink */
  --ink-primary:   #1A1A1A;  /* soft black — body text */
  --ink-muted:     #5C5C5C;  /* muted grey for secondary text and small print */

  /* Accents — use sparingly, never as full backgrounds */
  --accent-cocoa:  #6B4423;  /* button hover, focus rings, dark editorial */
  --accent-bordeaux: #6E1F2A; /* capsule / seasonal use only */

  /* Rules */
  --rule:          #D9CFC2;  /* hairline divider color */
}
```

**Critical palette rule (PRD §02):** do not introduce any color outside this
palette without explicit approval. Trend-coded accent colors, gradients, and
shadows are off-brand. The quiet-luxury feel comes from tonal restraint —
white surface, soft black ink, cocoa accents used sparingly.

### Typography

- **Editorial display:** Cormorant Garamond — headings, product names, pull
  quotes, hero copy. Used at large sizes and italic.
- **Body / UI:** Montserrat Light — body, labels, nav, buttons, captions.
  Tracked-out uppercase for labels (`letter-spacing: 0.1em`).
- **Logo:** custom hand-drawn script (SVG asset, never re-set in CSS).

Type scale (working values, desktop — confirm against PRD §02):

| Use                     | Font                  | Size desktop | Size mobile  |
| ----------------------- | --------------------- | ------------ | ------------ |
| Hero headline           | Cormorant Garamond    | 56–72 px     | 40–48 px     |
| Section heading         | Cormorant Garamond    | ~40–48 px    | step ~25%    |
| Product name (PDP)      | Cormorant Garamond    | 32–36 px     | step ~25%    |
| Italic subtitle / dek   | Cormorant Garamond it.| 16–22 px     | step ~20%    |
| Body                    | Montserrat Light      | 15 px        | 15 px (hold) |
| Card name, nav, labels  | Montserrat Light      | 13–15 px     | 13–14 px     |
| Small print / muted     | Montserrat Light      | 11–13 px     | 11–13 px     |

Mobile sizes step down 20–25% **except body, which stays at 15 px** for
legibility (PRD §02).

### Spacing & layout

- **Max content width:** 1440 px. Beyond this, cream margins, content centered.
- **Horizontal gutters:** 40 px desktop, 20 px tablet, 16 px mobile.
- **Vertical section spacing:** 120–160 px desktop, 64–80 px mobile.
- **Whitespace is a brand requirement, not a design preference.** Sections
  must breathe like Hyeja and Lesse. If a section looks dense, it is wrong.

### Interaction

- Hover transitions: 200–300 ms. No bouncy springs, no scale-up effects.
- Link underlines on hover only, except where the reference deliberately
  keeps them visible (footer, journal "READ MORE").
- **No auto-advancing carousels.** Sliders are user-controlled only.
- **No parallax. No entrance animations** beyond a quiet fade-in on scroll
  where useful.
- Focus rings: thin `--accent-cocoa` outline. Never the browser default.

---

## Sections to build (new, beyond Dawn defaults)

Use these exact filenames. They appear in PRD §13.2 and elsewhere as
acceptance criteria.

```
sections/
  section-featured-editorial.liquid         # homepage hero, 2-col (Hyeja)
  section-brand-pillars.liquid              # 2 stacked editorial blocks
  section-featured-product-banner.liquid    # full-bleed cinematic banner
  section-best-sellers-grid.liquid          # 4-up product grid
  section-featured-bundle.liquid            # "The Set" block
  section-philosophy-ethos.liquid           # Philosophy + Ethos combined
  section-pre-footer-wordmark.liquid        # large centered wordmark

  section-product-founders-note.liquid      # PDP founders quote + images
  section-product-hero-details.liquid       # PDP material story
  section-product-application.liquid        # PDP cinematic video/image
  section-product-reviews.liquid            # Lesse-style reviews (app-powered)
  section-product-related.liquid            # "you may also like" 3-up

  section-about-hero.liquid                 # About hero (still image, NOT video)
  section-about-process.liquid
  section-about-sourcing.liquid
  section-about-origins.liquid
  section-about-founder.liquid

  section-journal-index.liquid              # alternating-image list
  section-utility-page.liquid               # shared S&R + FAQs layout
```

Every section must be **individually orderable in the theme customizer** and
have **content editable without touching code** (PRD §5 AC).

## Snippets to build

```
snippets/
  snippet-header.liquid                     # sticky header, search expand
  snippet-footer.liquid                     # 3-zone footer + newsletter
  snippet-newsletter-popup.liquid           # 15s-delay modal
  snippet-cart-drawer.liquid                # right-side drawer
  snippet-product-card.liquid               # reusable card for grids
  snippet-shipping-banner.liquid            # white announcement bar
```

---

## Global behavior rules (PRD §4)

These apply on every page. If you change the spec, change the PRD first.

**Header**

- Sticky on scroll, subtle shadow after 80 px scroll.
- Height 80 px desktop / 64 px mobile.
- **On the homepage hero only:** transparent background until scroll, then
  cream. Other pages: cream from load.
- Shop dropdown: hover-reveal on desktop (200 ms delay), tap on mobile.
- Search: icon collapses to inline input on click, expands ~200 px leftward,
  submits to `/search?q={query}`. Esc or outside click collapses it.

**Newsletter pop-up**

- Trigger: **exactly 15 s after first landing** on any page.
- Suppress if dismissed (cookie, 30-day expiry) or signed up (365-day expiry).
- **Never appears on** `/cart`, `/checkout`, `/account*`.
- 15-second delay timer **pauses if the tab is inactive**. Do not use raw
  `setTimeout` against wall time without a visibility check.
- Focus-trap inside modal. Esc closes. Returns focus to the trigger on close.
- The "×" is the only dismissal. **No "no thanks I hate discounts" CTA.**
- Same email list as the footer signup — single source of truth.

**Cart drawer**

- Slides in from the right within 150 ms.
- 420 px desktop, full-width mobile.
- Quantity changes use Shopify AJAX cart API, no full page reload.
- Free-shipping progress message uses the **same threshold as the
  announcement banner** — single theme setting, not two.
- **No "Continue shopping" link.** The × is sufficient.

**Free-shipping banner**

- One line, flush top or bottom (match Hyeja's placement).
- Editable via Shopify customizer — never hardcode the threshold or copy.

---

## Theme customizer settings (PRD §13.5)

The brand team must be able to edit all of the following without touching code:

- Free-shipping threshold and announcement banner text.
- Newsletter pop-up: headline, body, button label, discount code, delay timer.
- Header: logo asset, nav order, Shop dropdown items.
- Footer: link lists, social URLs.
- Homepage: section order and content (every section individually editable).
- About page: section content.
- Per-product metafields: founders-note quote, hero-details features,
  application video URL.
- Color tokens (for future palette tweaks).

If you add a new section, **expose every piece of copy, image, and link as a
schema setting**. No hardcoded marketing copy in `.liquid` files.

---

## Voice & microcopy rules

Most copy lives in the Shopify admin, but the codebase produces some itself:
form labels, button text, empty states, validation messages, alt text
fallbacks. Match the brand voice in all of these.

The voice is **considered, not chatty. Warm, not casual. Sensorial, not
technical. Quietly confident.** Short, declarative sentences. Few adverbs.
No emoji. No exclamation marks.

Microcopy quick reference:

| Context           | ✅ Do                                  | ❌ Don't                                  |
| ----------------- | -------------------------------------- | ----------------------------------------- |
| Add-to-cart label | `ADD TO CART`                          | `Add to Bag ✨`                            |
| Sold-out state    | `SOLD OUT`                             | `Sorry, out of stock!`                    |
| Empty cart        | `Your cart is empty.`                  | `Oops, nothing here yet!`                 |
| Newsletter heading| `First in line.`                       | `Join our amazing community 💕`            |
| Submit button     | `JOIN`                                 | `Subscribe now!`                          |
| Success state     | `Welcome. Your code is on the way.`    | `Thanks!! Check your inbox 🎉`             |
| Free-ship banner  | `FREE SHIPPING on orders above €100.`  | `🔥 Free shipping over €100! 🔥`           |

UI labels are **uppercase, tracked-out** (`text-transform: uppercase;
letter-spacing: 0.1em`). Sentence-case for marketing copy.

---

## Accessibility (PRD §12.3) — non-negotiable

- Touch targets **≥ 44 × 44 px**.
- All images have meaningful alt text; decorative images use `alt=""`.
- Test contrast: cocoa-on-cream and cream-on-cocoa may fall below WCAG AA at
  small sizes. **If so, restrict cocoa to larger text only.**
- Every interactive element reachable via Tab, operable via Enter/Space.
- Visible focus rings in `--accent-cocoa`. Never `outline: none` without a
  replacement.
- Newsletter pop-up and cart drawer: **focus trap, Esc closes, focus returns
  to trigger on close.**
- Search input: `aria-label="Search products."`
- Form fields: label associated with input via `for`/`id`. Inline error
  states use `aria-describedby`.
- Skip-to-main-content link at the top of every page, hidden until focused.
- Accordions, tabs, reviews: proper ARIA (`aria-expanded`, `role="tab"`).

---

## Performance targets (PRD §12.4)

- LCP < 2.5 s on a 4G connection.
- CLS < 0.1.
- INP < 200 ms.
- Homepage page weight < 2 MB compressed.
- Images: **WebP with JPEG fallback**, `srcset` for responsive sizing.
  PNG only for logo and UI assets requiring transparency.
- Fonts: preloaded, `font-display: swap`.
- JavaScript: deferred where possible. No render-blocking scripts.

Every product image must be available in at least three crops: **square
(1:1), portrait (4:5), landscape (16:9)**. PDP hero uses portrait or square;
collection grid uses square; homepage hero uses landscape.

---

## Apps and integrations (PRD §13.4)

- **Reviews:** Judge.me, Yotpo, or Loox (TBD). Whichever is chosen, **default
  app styling must be fully overridden** to match the Lesse reviews layout.
  No default badges, no app-branded colors, no default fonts.
- **Email:** Shopify Email for v1, Klaviyo recommended for v1.5. Newsletter
  pop-up and footer signup feed the **same list**. 10% discount code is
  auto-generated and sent on signup.
- **Search:** Shopify's built-in search and predictive-search APIs, with
  custom front-end styling — no Dawn default search UI visible.
- **Analytics:** Shopify Analytics + GA4. Optionally Hotjar / Clarity for
  the first 3 months post-launch.

---

## Things to push back on

If the user asks for any of these, stop and confirm — they conflict with the
brand or the PRD:

- Pop-ups with "no thanks" guilt-trip CTAs.
- Auto-advancing carousels or hero sliders.
- Parallax, scroll-jacking, or showy entrance animations.
- Trend-coded accent colors (electric blue, neon, pastel pink) outside the
  defined palette.
- Promotional language with multiple exclamation marks, emoji, or shouty caps
  outside the tracked-out label treatment.
- Subscription / "subscribe & save" UI (explicitly removed from the Lesse
  reference per PRD §6.1).
- Korean text, the 美 character, or any Hyeja-specific labels left in the
  homepage (PRD §5 AC).
- A "WATCH VIDEO" button or background video on the About hero (PRD §8.1 —
  explicit spec: still image only).
- Filters on collection pages (deferred to v2 — PRD §7.1).
- Numbered pagination on collections (infinite scroll or "LOAD MORE" only —
  PRD §7.3).

---

## How I work in this repo

- **Cite the PRD section** when implementing a spec (`/* PRD §5.3 */` in
  comments is fine).
- **Read before writing.** If a section already exists in Dawn, override it
  rather than building a new one alongside.
- **Token-first.** No hex literals, no hardcoded sizes, no inline copy.
  Tokens, schema settings, locale files.
- **Test the customizer.** After building a section, open the theme editor
  and confirm every piece of content is editable from there.
- **One section, one schema.** Keep section schemas tight; don't dump every
  setting into one section.
- **Mobile is not an afterthought.** Build mobile and desktop layouts
  together; the breakpoints in PRD §12.1 are the contract.

When unsure about a spec detail, ask before assuming. The PRD is the source
of truth; the Brand Positioning Doc explains *why*; this file is the
operating manual.

---

*Last updated against PRD v1.0 and Brand Positioning Doc v1.0.*
