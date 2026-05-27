# POIVRE PARIS

**Website Product Requirements Document**
For the Shopify build — Dawn 15.4.1 fork
v1.0 · Working document

---

## Contents

1. [Overview & Goals](#01--overview--goals)
2. [Brand & Design System](#02--brand--design-system)
3. [Site Architecture](#03--site-architecture)
4. [Global Components — Header, Footer, Newsletter Pop-Up, Cart](#04--global-components)
5. [Homepage](#05--homepage)
6. [Product Page](#06--product-page)
7. [Collection Pages (New In / Shop)](#07--collection-pages)
8. [About Page](#08--about-page)
9. [Journal — Index & Article](#09--journal--index--article)
10. [Utility Pages — Shipping & Returns, FAQs](#10--utility-pages)
11. [Stub Pages — Contact, Privacy Policy, Terms & Conditions](#11--stub-pages)
12. [Responsive & Accessibility Requirements](#12--responsive--accessibility-requirements)
13. [Technical Notes — Dawn Fork, Shopify Configuration, Performance](#13--technical-notes)
14. [Open Questions & Decisions Needed](#14--open-questions--decisions-needed)

---

## 01 · Overview & Goals

### Purpose of this document

This document specifies the design and behavior of the Poivre Paris ecommerce website. It is written for a Shopify developer building a fork of the Dawn theme (v15.4.1) and contains layout, content, interaction, and acceptance criteria for every page in the v1 site. It does not contain code; it tells the developer what to build, why, and how it should behave.

This PRD draws directly on the Poivre Paris Brand Positioning Document (v1.0) for tone, voice, and aesthetic constraints, and on two visual references: Hyeja Skincare (hyejaskincare.com) for homepage and footer structure, and Lesse (lesseofficial.com) for product, about, journal, and utility pages.

### Business goals for v1

- **Convert.** Move first-time visitors from feed to first purchase through an editorial, low-friction site that feels like quiet luxury.
- **Build the list.** Capture email addresses early through a clean post-arrival newsletter pop-up offering a 10% first-order discount.
- **Tell the story.** Use the About and Journal pages to make the brand legible to a customer arriving cold from social.
- **Operate as a publisher.** Drop-based collections, not a permanent catalog. The site is structured to handle release cadence rather than a static SKU library.

### Out of scope for v1

- Subscription / recurring purchase functionality.
- Multi-currency and multi-region routing beyond Shopify defaults.
- Loyalty / referral platform integration (will be added in v2).
- Skin-analysis or quiz functionality (the Lesse "Skin Analysis" tab is intentionally not replicated).
- Stockist locator (will be added when stockists exist).
- Korean-language or any non-English localization.

### Reference sites

| Reference | Used for |
|---|---|
| Hyeja Skincare (hyejaskincare.com) | Homepage layout, section composition, footer structure |
| Lesse (lesseofficial.com) | Product page, about page, journal index, utility pages (shipping/returns, FAQs, contact) |
| Dawn 15.4.1 (Shopify base theme) | Foundation theme to fork — provides cart, checkout, collection logic, search infrastructure |

---

## 02 · Brand & Design System

Brand foundations are defined in the Poivre Paris Brand Positioning Document. This section captures the visual decisions the site must consistently honor.

### Typography

| Role | Specification |
|---|---|
| Display / headings (H1–H3) | Cormorant Garamond, Regular (400). Source: Google Fonts (free). |
| Body, paragraph, UI labels, navigation, buttons | Montserrat, Light (300). Source: Google Fonts (free). |
| Brand wordmark / logo | Existing Poivre script logo asset (PNG/SVG provided). |
| Fallback stack — headings | Cormorant Garamond, "Cormorant", Georgia, "Times New Roman", serif |
| Fallback stack — body | Montserrat, "Helvetica Neue", Helvetica, Arial, sans-serif |
| Loading strategy | Preload both font families. Use `font-display: swap` to prevent invisible text on slow connections. |

#### Type scale (working values, desktop)

| Element | Size / weight / line-height |
|---|---|
| Hero headline (H1, homepage / about) | 56–72 px · Cormorant Garamond Regular · line-height 1.1 |
| Section heading (H2) | 32–40 px · Cormorant Garamond Regular · line-height 1.2 |
| Sub-section heading (H3) | 22–26 px · Cormorant Garamond Regular · line-height 1.3 |
| Body large (editorial paragraphs) | 17–18 px · Montserrat Light · line-height 1.6 |
| Body default | 15 px · Montserrat Light · line-height 1.6 |
| Small / caption / nav label | 12–13 px · Montserrat Light · uppercase · letter-spacing 0.08em |
| Button label | 13 px · Montserrat Light · uppercase · letter-spacing 0.1em |

> **NOTE** — Mobile sizes step down ~20–25%. Hero on mobile uses 40–48 px instead of 56–72 px. Body remains 15 px to preserve legibility.

### Color palette

| Token | Value & usage |
|---|---|
| `--bg-primary` | `#F5EDE2` (warm cream) — default page background |
| `--bg-secondary` | `#FAF6F0` (lighter cream) — alternate sections, popup background |
| `--ink-primary` | `#1A1A1A` (soft black) — body text, headings, primary UI |
| `--ink-muted` | `#5C5C5C` — secondary text, form labels, footer fine print |
| `--accent-cocoa` | `#6B4423` (warm cocoa) — used sparingly: hover states, editorial accents |
| `--accent-bordeaux` | `#6E1F2A` (bordeaux) — capsule / seasonal accents only, never for default UI |
| `--rule` | `#D9CFC2` — hairline dividers, table borders, input underlines |
| `--surface-button` | `#1A1A1A` on hover `#6B4423` — primary CTA |

> **CRITICAL** — Do not introduce any color outside this palette without explicit approval. The references (Hyeja, Lesse) achieve their quiet-luxury feel through tonal restraint — that discipline must be preserved here.

### Spacing & layout

- Max content width: 1440 px. Beyond this, content stays centered with cream margins.
- Standard horizontal gutter: 40 px on desktop, 20 px on tablet, 16 px on mobile.
- Vertical section spacing: 120–160 px between major sections on desktop, 64–80 px on mobile.
- Generous whitespace is a brand requirement, not a design preference. Sections must breathe like Hyeja and Lesse — not feel dense.

### Imagery & photography

- All product and lifestyle imagery follows the Poivre photography direction documented in the brand positioning doc (natural light, raw materials, still-life-led, no overlit packshots).
- Default image format: WebP with JPEG fallback. PNG only for logo and UI assets requiring transparency.
- All product images must be available in at least three crops: square (1:1), portrait (4:5), and landscape (16:9). The product page hero uses portrait or square; collection grid uses square; homepage hero uses landscape.
- No stock photography. No AI-generated imagery in production.

### Interaction principles

- Hover states are subtle: 200–300 ms transitions. No bouncy springs, no scale-up effects.
- Link underlines on hover only, except where the brand reference deliberately keeps them visible (footer, journal "READ MORE").
- No carousels that auto-advance. If a slider is used, user-controlled only.
- No parallax, no entrance animations beyond a quiet fade-in on scroll where useful.

---

## 03 · Site Architecture

### Top-level sitemap

| URL | Page |
|---|---|
| `/` | Homepage |
| `/collections/new-in` | New In (newest drop / recently released SKUs) |
| `/collections/all` | Shop All |
| `/collections/tableware` | Shop > Tableware |
| `/collections/decor` | Shop > Decor |
| `/collections/lifestyle` | Shop > Lifestyle |
| `/collections/bundles` | Shop > Bundles |
| `/products/{handle}` | Product detail page |
| `/pages/about` | About |
| `/blogs/journal` | Journal index |
| `/blogs/journal/{article-handle}` | Journal article |
| `/pages/shipping-returns` | Shipping & Returns |
| `/pages/faqs` | FAQs |
| `/pages/contact` | Contact (stub for v1) |
| `/pages/privacy-policy` | Privacy Policy (stub for v1, populated in Shopify Pages) |
| `/pages/terms-conditions` | Terms & Conditions (stub for v1, populated in Shopify Pages) |
| `/search` | Search results |
| `/cart` | Cart (Dawn default, restyled to brand) |
| `/checkout` | Checkout (Shopify managed) |

### Primary navigation (top header)

Reading left to right after the wordmark:

- **New In** — links to `/collections/new-in`
- **Shop** — hover-reveals a dropdown with: Shop All, Tableware, Decor, Lifestyle, Bundles
- **About** — links to `/pages/about`
- **Journal** — links to `/blogs/journal`
- **Search** — icon plus inline expandable search input (specified in §04)
- **Account** — icon, links to `/account` (Shopify default)
- **Cart** — icon with item count badge, opens drawer (specified in §04)

> **CHANGE VS. HYEJA** — The third Hyeja header tab (the arrow) is removed. "Explore" is removed and replaced by a functional search bar. "About" and "Journal" are added. "New Arrival" becomes "New In". "Products" becomes "Shop" with the dropdown listed above. All header tab text is 20% larger than Hyeja's default for legibility.

### Footer navigation

Two-column structure mirroring Hyeja's footer, but with Poivre's links:

**Left column — primary site**
- New In
- Shop
- About
- Journal

**Right column — utility & social**
- Shipping & Returns
- Contact
- FAQs
- Privacy Policy
- Terms & Conditions
- Pinterest (icon link)
- Instagram (icon link)

> **REMOVED FROM HYEJA FOOTER** — Philosophy, Ethos, Stockist, TikTok. These either duplicate content already covered (Philosophy → About; Ethos → About sub-section) or are not relevant for v1 (no stockists yet; TikTok presence yes, but linked from social UGC posts, not footer).

---

## 04 · Global Components

The header, footer, newsletter pop-up, and cart drawer appear on every page and must be visually and behaviorally consistent throughout the site.

### 4.1 · Header

**Layout**
- Sticky on scroll, with a subtle shadow appearing once the user has scrolled past 80 px.
- Height: 80 px desktop, 64 px mobile.
- Background: `--bg-primary` (cream). On homepage hero, header background is transparent until scroll, with white/cream text overlaid on the hero image.

**Composition**
- Far left: Poivre wordmark logo (PNG/SVG asset). Click → returns to `/`. Logo height ~32 px desktop, ~24 px mobile.
- Center-left, after the logo: nav tabs in this order — New In, Shop, About, Journal. Tabs use Montserrat Light, uppercase, letter-spacing 0.1em, font size ~15 px (20% larger than Hyeja's default of ~12–13 px).
- Shop tab: on hover (desktop) or tap (mobile), reveals a dropdown panel with: Shop All, Tableware, Decor, Lifestyle, Bundles. Dropdown background: `--bg-secondary`. Items styled in the same uppercase tracked-out treatment as the nav.
- Far right: search icon + expandable input, account icon, cart icon (with badge).

**Search behavior**
- Default state: magnifying-glass icon only.
- On click: icon transforms into an inline text input with a hairline underline (`--rule`). The input expands leftward to take ~200 px width on desktop, full-row on mobile.
- Submitting the form routes to `/search?q={query}` (Shopify default predictive search may be enabled, but design must match the brand — no Dawn default styling).
- Esc key or clicking outside collapses the input back to icon-only.

**Mobile header**
- Wordmark centered.
- Hamburger menu icon on the left opens a full-screen overlay containing all nav links and the search input.
- Cart icon on the right.

> **ACCEPTANCE CRITERIA**
> - Header is consistent across every page (homepage, product, about, journal, all utility pages).
> - Search submits to `/search` and returns Shopify search results styled in brand (not Dawn default).
> - Shop dropdown reveals on hover (desktop, 200ms delay) and on tap (mobile).
> - Sticky behavior: header remains visible on scroll, with subtle elevation shadow after 80 px scroll.
> - On the homepage hero specifically, the header is transparent until scroll, then transitions to cream background.

### 4.2 · Footer

**Layout**
- Background: `--bg-primary` (cream). 1 px top border in `--rule`.
- Vertical padding: 80 px top and bottom on desktop, 48 px on mobile.
- Three-zone layout: left link column, right link column, newsletter block (right side or below on mobile).

**Structure**
- Left column: primary site links (New In, Shop, About, Journal). Stacked vertically. Montserrat Light, ~14 px.
- Middle column: utility/policy links (Shipping & Returns, Contact, FAQs, Privacy Policy, Terms & Conditions, then Pinterest and Instagram icons at the bottom).
- Right column: newsletter signup. Heading "NEWSLETTER" (uppercase, tracked-out), short line "Subscribe for first access to drops and 10% off your first order.", email input with hairline underline, "SUBMIT" button.
- Below the columns, a thin row: short legal paragraph (mirroring Hyeja: "By joining, you agree to receive email marketing of the contacts provided. By submitting, you agree to the Terms of Service and Privacy Policy.") in `--ink-muted`, then "© [current year] Poivre Paris." on the right.

**White announcement banner**
- A single-line white banner sits flush at the very bottom of every page (or top — match Hyeja's placement).
- Default text: "FREE SHIPPING on orders above €100." Editable via Shopify theme settings so this text can change per campaign.
- Centered, Montserrat Light, ~13 px, `--ink-primary` on white background.

> **ACCEPTANCE CRITERIA**
> - Footer renders identically on every page.
> - Newsletter input submits to the same email list as the post-arrival pop-up — single source of truth.
> - Pinterest and Instagram icons link to placeholder URLs editable in theme settings.
> - The free-shipping banner text is editable via Shopify customizer, not hardcoded.
> - Footer collapses gracefully to a single column on mobile, newsletter sits at the bottom.

### 4.3 · Newsletter Pop-Up

**Trigger**
- Appears 15 seconds after the user first enters the site, on any landing page.
- Suppressed if the user has already dismissed it (cookie, 30-day expiry).
- Suppressed if the user has already signed up (cookie, 365-day expiry).
- Does not appear during checkout, on cart drawer open, or on account pages.

**Visual**
- Modal centered on screen, ~440 px wide on desktop, full-width minus 32 px gutter on mobile.
- Background: `--bg-secondary`. 1 px border in `--rule`. No shadow, no rounded corners beyond 2 px.
- Page behind is dimmed with a 50% black overlay.
- Close affordance: a small "×" in the top-right corner. Pressing Esc or clicking the overlay also closes it.

**Content**
- Headline (Cormorant Garamond, ~28 px): "First in line."
- Subhead (Montserrat Light, ~14 px): "Subscribe for early access to drops and 10% off your first order."
- Email input: hairline underline, placeholder "Email address".
- Submit button: full-width black bar, "JOIN" in Montserrat Light uppercase, `--bg-primary` text on `--ink-primary` background. Hover: `--accent-cocoa` background.
- Small print below button (Montserrat Light, ~11 px, `--ink-muted`): "By subscribing you agree to our Privacy Policy. Unsubscribe anytime."
- No second CTA. No "no thanks I hate discounts" guilt-trip language. The "×" is the dismissal.

**Success state**
- On submit: form replaced inline with a short confirmation — "Welcome. Your code is on the way." — followed by an auto-close after 4 seconds.
- The 10% discount code is sent immediately via email (Shopify Email or Klaviyo automation — to be confirmed in §14).

> **ACCEPTANCE CRITERIA**
> - Pop-up appears exactly once per session if not previously dismissed or subscribed.
> - 15-second delay timer pauses if the tab is inactive.
> - Form submission writes to the same email list as the footer newsletter.
> - Pop-up does not appear on `/cart`, `/checkout`, `/account` paths.
> - Pop-up is fully keyboard-accessible (focus trap inside modal, Esc closes).

### 4.4 · Cart Drawer

**Style**
- Slides in from the right edge of the screen on add-to-cart or cart-icon click.
- Width: 420 px desktop, full-width on mobile.
- Background: `--bg-primary`. Visual treatment mirrors Hyeja: minimalist, line-item list, no promotional clutter.
- Header: "CART" (uppercase, tracked-out, Cormorant or Montserrat — match Hyeja's choice exactly), close × on the right.

**Line items**
- Square thumbnail (60 px), product name (Montserrat Light, ~14 px), variant if applicable, price.
- Quantity stepper: − N +.
- "Remove" link in `--ink-muted`, small, below price.

**Footer of drawer**
- Subtotal row.
- Free-shipping progress message (if order is below the €100 threshold): "You're €X away from free shipping." Once the threshold is reached: "Free shipping unlocked."
- "CHECKOUT" button: full-width black bar, Montserrat Light uppercase, `--bg-primary` text on `--ink-primary` background.
- No "Continue shopping" link — the × is sufficient.

> **ACCEPTANCE CRITERIA**
> - Cart drawer opens within 150ms of add-to-cart or icon click.
> - Quantity changes update the line and subtotal without a full page reload (Shopify AJAX cart API).
> - The free-shipping progress message uses the same threshold as the announcement banner — single source of truth (theme setting).
> - On mobile, the drawer takes full screen width and is scrollable.
> - Closing the drawer returns focus to the cart icon (accessibility).

---

## 05 · Homepage

The homepage is built section-by-section in the same vertical rhythm as Hyeja's. Each section earns its place; nothing is included for the sake of filling space. The job of the homepage is to introduce a first-time visitor to the brand, surface the current drop, build aesthetic affinity, and route them to the product or the about page.

### 5.1 · Hero — Featured editorial

**Layout**
- Full-bleed two-column on desktop: left column ~40%, right column ~60%. Stacks to single column on mobile.
- Left column: cream background, contains the editorial copy block.
- Right column: large lifestyle image of a Poivre product or styled scene.

**Content**
- Eyebrow label (optional, Montserrat Light uppercase tracked-out, ~12 px): e.g. "Drop 01" or a campaign tag.
- Headline (Cormorant Garamond, ~56–72 px): brand-defining sentence. Working placeholder: "Quiet luxury, for the way you live now."
- Short body (Montserrat Light, ~15 px, 2–3 sentences max): the editorial pitch. Replaceable per drop.
- Primary CTA: "SHOP" or "EXPLORE THE DROP" — uppercase tracked-out, with underline-on-hover treatment (no button box).

**Editability**
- All hero content — image, eyebrow, headline, body, CTA text, CTA link — editable in Shopify theme customizer.
- Image: portrait or landscape both supported via a single block setting.

> **REFERENCE** — Mirrors Hyeja's "Redefining Beauty (美) of Imperfection" hero block — proportions, type sizes, and section composition match. The Korean-language column and 美 character are removed; Poivre is not a Korean brand.

### 5.2 · Brand pillars — two-block introduction

**Layout**
- Two stacked editorial blocks below the hero, mirroring Hyeja's "Meticulous Formulations" and "Innovative Korean Skincare" blocks.
- Each block: left column has a heading + short paragraph; right column has a small accompanying image.

**Content for Poivre**
- Block 1 — "Curated Drops". Short paragraph (~40 words): how the brand operates by release rather than catalog, drawing from the product-strategy section of the Brand Positioning Doc.
- Block 2 — "Refined Rawness". Short paragraph (~40 words): the material philosophy — stone, wood, ceramic, glass, linen.
- Both block headings use Cormorant Garamond ~28 px, body uses Montserrat Light ~14 px.

### 5.3 · Featured product — full-bleed cinematic banner

**Layout**
- Full-width image or video background (cinematic, brand-aligned imagery — see brand doc photography direction).
- Centered text overlay in cream or white.

**Content**
- Left-aligned small label: "NEW IN" (tracked-out, ~12 px).
- Centered product name (Cormorant Garamond, ~40 px). Working placeholder: "Marble Pepper Mill — Bordeaux."
- Small subtitle: short evocative line, ~16 px Cormorant Garamond italic.
- Right-aligned column at far right: price + "VIEW PRODUCT" link.
- Background: hero image of the product in context.

> **REFERENCE** — Mirrors Hyeja's "Vegan Rice Probiotics Gentle Ferment Cream" banner — same proportions, same text placement, same tonal treatment.

### 5.4 · Best Sellers — product grid

**Layout**
- Section heading "BEST SELLERS" on the left (small, tracked-out, ~12 px).
- Product grid in the center: 4 products on desktop (one more than Hyeja's 3), 2 per row on tablet, 1 per row on mobile.
- "Gentle care for sensitive skin" / equivalent tagline on Hyeja — replaced with Poivre tagline, working placeholder: "The pieces our customers keep coming back to."
- "SHOP ALL" link on the right, leading to `/collections/all`.

**Per-product card**
- Square image (1:1 ratio).
- Product name (Montserrat Light, ~14 px) below the image.
- Short Cormorant Garamond subtitle (~13 px italic) — optional.
- Price (Montserrat Light, ~14 px).
- On hover: subtle dim or swap to a second product image (match Hyeja's behavior exactly).

**Editability**
- Products in this grid editable via theme customizer: pick collection (e.g. "best-sellers" collection) or manually select up to 4 products.

### 5.5 · The Set — featured bundle

**Layout**
- Mirrors Hyeja's "The Set" / Matcha Mud Cream Mask block: large lifestyle portrait on the right, a small product card on the lower-left, supporting copy beside it.

**Content**
- Eyebrow: "THE SET" (uppercase tracked-out).
- Bundle name (Cormorant Garamond, ~32 px): e.g. "Hosting Set", "Bedside Bundle".
- Short body: one or two lines describing the bundle.
- Star rating (out of 5) + count of reviews.
- Price.
- "VIEW PRODUCT" link.
- Below the card, a "products in this set" listing in a small grid (matches Hyeja's sub-listing).

### 5.6 · Philosophy / Ethos

**Layout**
- Two-column section mirroring Hyeja's "Philosophy" + "Ethos" block. On the left: Philosophy heading + short essay. On the right: a hero still-life image. Below image, an "Ethos" numbered list aligned to a bracket-style grid (match Hyeja's "ETHOS" tabular layout).

**Content**
- Philosophy heading: "Philosophy" in Cormorant Garamond ~40 px.
- Philosophy body: ~80–120 word essay drawing from the Brand Positioning Doc's purpose + vision statements. Working draft: "Poivre believes the everyday object deserves the same care as the heirloom. We release small, edited collections of home and tableware objects built around natural materials, sculptural restraint, and a Paris-rooted sensibility — at prices that don't require permission. We design for the way you live now, not the way a catalog imagines you do."
- Ethos heading: "Ethos" — placed on the right column, large, with vertical labels matching Hyeja's layout (e.g. "PURPOSE-DRIVEN PRACTICES").
- Ethos list (right side): "1. Sustainability · 2. Quiet by design · 3. Material honesty · 4. Accessible pricing · 5. Made to last."
- "Purpose-driven practices" paragraph (below ethos list): short 2-sentence block from the brand doc, on responsibility and curation.

### 5.7 · Pre-footer wordmark

- A simple block matching Hyeja's final treatment: the Poivre wordmark large and centered, with the location "Paris" beneath it.
- Quietly closes the page before the footer.

> **ACCEPTANCE CRITERIA**
> - Every section in §5.1–5.7 is implemented as a separate Dawn section, individually orderable in the theme customizer.
> - Each section's content (text, images, links) is editable without touching code.
> - Page loads with LCP under 2.5 seconds on a standard 4G connection.
> - No Korean text, no 美 character, no Hyeja-specific labels remain anywhere.
> - Hero section gracefully degrades on screens narrower than 768 px (stacks to single column, image moves above text).

---

## 06 · Product Page

The product page is modeled closely on Lesse's product page (lesseofficial.com/products/ritual-serum) with brand-specific adaptations. Every visitor who clicks a product anywhere on the site lands here; this is the single most important conversion page on the site.

### 6.1 · Above the fold

**Left column — product imagery**
- Primary product image: large, ~50% page width on desktop, square aspect ratio.
- Image thumbnails (4–6 images): horizontal row beneath the primary, ~80 px square each. Click swaps the primary image.
- Image zoom on hover (Lesse-style: subtle, not aggressive).
- Background of the imagery column: `--bg-secondary` or product's native styled background (no white seamless cutouts).

**Right column — product info**
- Product name (Cormorant Garamond, ~32–36 px).
- Italic subtitle (Cormorant Garamond italic, ~16 px): short evocative line, e.g. "Hand-turned. Honed marble. Weighted right."
- One-line material/composition note (Montserrat Light, ~13 px, `--ink-muted`): e.g. "100% Carrara marble."
- Star rating + reviews count (mirrors Lesse: ~14 px, e.g. "★★★★★ 4.97 (149 Reviews)").
- Price (Montserrat Light, ~16 px).
- Quantity stepper + "ADD TO CART" button on the same row. Stepper: − N +, default 1. Button: full-width within the right column, `--ink-primary` background, `--bg-primary` text, uppercase, tracked-out. Hover: `--accent-cocoa`.
- Below the button: "Free shipping on orders above €100." in `--ink-muted`, ~13 px.

> **CHANGE VS. LESSE** — The "Just once" / "Subscribe & Save" radio selector is removed. Poivre does not sell subscription products. Replace with a clean quantity stepper next to "ADD TO CART" — see Lesse's post-add-to-cart layout but without the subscription toggle.

**Information accordion (below add-to-cart)**
- Tabbed or accordion layout mirroring Lesse's "Information / Application / Ingredients" — but with Poivre-relevant tabs only.
- Tab 1 — Details: longer-form product description, ~80–120 words. Sensorial, material-led writing per brand voice.
- Tab 2 — Dimensions & Materials: structured spec — height, width, weight, material, finish, country of origin.
- Tab 3 — Care: how to clean, store, and maintain the object.

> **CHANGE VS. LESSE** — "Ingredients" and "Application" tabs are removed — not relevant for tableware/home. The three tabs above replace them. Tab labels use the same uppercase tracked-out treatment Lesse uses.

### 6.2 · Founders Note section

**Layout**
- Mirrors Lesse's "Founders Note" block exactly: left column has the quote, right column has two stacked supporting images (Lesse uses before/after — for Poivre these are detail shots, lifestyle moments, or material-source imagery).

**Content**
- "FOUNDERS NOTE" eyebrow label.
- Multi-line quote in Cormorant Garamond italic, ~20 px, treated as a pull-quote.
- Signature line: "— [Founder name], Founder" in Montserrat Light, ~13 px.
- "OUR STORY" link below the quote, routing to `/pages/about`.
- Right column: two square images stacked, with a small caption label on each (e.g. "Source." / "Studio.").

### 6.3 · Hero details / inspiration section

**Layout**
- Mirrors Lesse's "Hero Ingredients" / "Explore Ingredients" block — left column has a vertical list of features or materials, right column has a hero still-life image with an editorial overlay.

**Content**
- "HERO DETAILS" or "MATERIAL STORY" eyebrow.
- Left column: 2–4 vertical feature blocks. Each has a small thumbnail icon or material swatch, a name (e.g. "Carrara Marble"), and a 2–3 line description.
- "EXPLORE THE COLLECTION" link at the top.
- Right column: large editorial image (e.g. a marble vein close-up).
- Below the right image: a "Full materials list" small text block (mirrors Lesse's ingredients list treatment — densely set in Montserrat Light, ~12 px).

### 6.4 · Application / usage video or scene

**Layout**
- Full-bleed cinematic block mirroring Lesse's "Application" video band.
- Background: looping video of the object in use, or a static cinematic image if no video is available yet.
- Overlay text on the left: "APPLICATION" or "IN USE" eyebrow, short 2-line description, optional numbered step labels at the bottom for multi-step rituals (e.g. for a candle: "Trim wick. Light. Burn for 2 hours.").

### 6.5 · Reviews section

**Layout**
- Mirrors Lesse's reviews layout below the application section.
- Top: large pull-quote in Cormorant Garamond italic, ~20 px, with a "— [Customer first name + last initial]" attribution.
- Below: average star rating, total review count, and a "WRITE A REVIEW" link.
- Individual reviews listed vertically — star rating, review title in caps, body text, customer attribution, date.
- "NEXT" pagination link at the bottom right.

**Implementation**
- Use a Shopify-compatible reviews app — Judge.me, Yotpo, or Loox. The app's default styling must be overridden to match Lesse's layout exactly.
- Reviews must include name, star rating, optional title, body, and submission date.

### 6.6 · You may also like

**Layout**
- "YOU MAY ALSO LIKE" eyebrow (left-aligned, tracked-out).
- Three product cards in a row mirroring Lesse's treatment.
- Each card: square product image, optional small label (e.g. "FOUNDERS FAVORITE", "BEST SELLER") in Cormorant Garamond italic, product name in Montserrat Light, short subtitle, star rating, price.

**Logic**
- Recommendations driven by Shopify's product-recommendations API (`related_products`) with manual override capability per product (via product metafield).

> **ACCEPTANCE CRITERIA**
> - Subscription radios and beauty-specific tabs are absent.
> - Quantity stepper + add-to-cart functions correctly with Shopify AJAX cart.
> - All five sections (founders note, hero details, application, reviews, you may also like) render in order below the fold.
> - Reviews app is integrated and styled to match Lesse layout — no default app styling visible.
> - Page is fully responsive: founders-note layout stacks vertically on mobile, hero-details image moves below the feature list, reviews remain readable.
> - Cormorant Garamond is used for product name, subtitles, and quote pull-outs; Montserrat Light for everything else.

---

## 07 · Collection Pages

Collection pages — `/collections/new-in`, `/collections/all`, `/collections/tableware`, `/collections/decor`, `/collections/lifestyle`, `/collections/bundles` — all share a single template. They are the bridge between the homepage / nav and the product page.

### 7.1 · Layout

- Page header: collection title in Cormorant Garamond, ~48 px, left-aligned, with a short collection description (~30–50 words) beneath in Montserrat Light, ~15 px.
- Optional editorial hero image below the title, full-width or two-column (theme-customizer toggle).
- Product grid: 3 columns desktop, 2 columns tablet, 1 column mobile. Generous gutters (40 px).
- No sidebar filters in v1. (Filters added in v2 once SKU count justifies them.)
- Sort dropdown at the top right: "Newest", "Price: Low to high", "Price: High to low", "Best selling". Styled as a minimal text dropdown, not a boxed select.

### 7.2 · Product card

- Image: 1:1 square, fills the column width.
- On hover: swap to a secondary image (lifestyle / detail shot).
- Below image: product name (Montserrat Light, ~14 px), optional Cormorant Garamond italic subtitle (~13 px), price (Montserrat Light, ~14 px).
- Click anywhere on the card → product page.
- Sold-out products: greyed out, with a "SOLD OUT" overlay in tracked-out tiny caps. Sold-out is a feature for the drop model — leave them visible.

### 7.3 · Pagination

- Infinite scroll preferred. Fallback to a "LOAD MORE" link at the bottom.
- No numbered pagination.

> **ACCEPTANCE CRITERIA**
> - A single collection template renders all collection URLs correctly.
> - Shop dropdown in the header routes to the matching collection page.
> - Sold-out products display with a "SOLD OUT" overlay and are not removed from the grid.
> - Sort dropdown re-orders products without a full page reload.
> - Collection title and description are editable per collection via Shopify admin.

---

## 08 · About Page

The About page is modeled on Lesse's About page (lesseofficial.com/pages/about) with two specific changes called out in the spec.

### 8.1 · Hero section

**Layout**
- Full-bleed image as background (NOT a video — explicit spec).
- Centered text overlay in cream or white.
- Headline (Cormorant Garamond, ~48–56 px). Working placeholder: "Poivre redefines home through curated, considered, and quietly luxurious objects."
- Italic emphasis on key phrases — mirrors Lesse's "quality, efficacy, and simplicity" italicization. For Poivre: "curated, considered, and quietly luxurious" in italic.

> **CHANGE VS. LESSE** — The "WATCH VIDEO" button beneath the headline is REMOVED. The background video is REPLACED with a still photo. The "OUR VALUES" button (further down the page, beneath the founder quote) is also REMOVED.

### 8.2 · Process / Approach section

**Layout**
- Mirrors Lesse exactly: left side has an eyebrow label "PROCESS" or "APPROACH"; center holds a paragraph in Cormorant Garamond, ~22 px, centered.
- Italicization of key phrases (e.g. "years of research" in Lesse).

**Content**
- Adapt Lesse's structure with Poivre content drawn from the Brand Positioning Doc's mission and approach sections.
- Working placeholder: "Poivre objects are the result of *deliberate curation*, refined sourcing, and rigorous selection — each chosen to combine sculptural form, natural material, and accessible price. Our approach is defined by restraint and material honesty: we say no more than we say yes, so every piece we release has earned its place."

### 8.3 · Sourcing / materials section

**Layout**
- Mirrors Lesse's ingredients-sourcing block: left column has a paragraph with an "EXPLORE [X]" link below; right column has a hero image and a small caption beneath it.

**Content**
- Left paragraph: ~80–100 words on material sourcing — stone from Italy, ceramic from Portugal, etc. (Placeholder copy; replace with actual sourcing once confirmed.)
- "EXPLORE THE COLLECTION" link below.
- Right column: large editorial image (material close-up, source landscape, atelier scene).
- Caption beneath image: short paragraph in Montserrat Light, ~13 px, `--ink-muted`: e.g. "Poivre is an independent, founder-led home brand — allowing us to make uncompromising decisions on material, design, and integrity."

### 8.4 · Origins / quote section

**Layout**
- Mirrors Lesse's "ORIGINS" block: large full-bleed image of a person or scene; a centered quote overlay in Cormorant Garamond italic, ~20–24 px.
- "OUR VALUES" button is REMOVED (per spec).

**Content**
- Eyebrow: "ORIGINS".
- Quote (placeholder): "I started Poivre because the objects that surround you should be as considered as the meals you eat off them. Beauty in the everyday — that's the brand."
- Attribution below quote: "— [Founder name], Founder."

### 8.5 · Founder bio section

**Layout**
- Mirrors Lesse's Neada Deters founder bio block: left side has founder name + title (small, tracked-out) and a short bio paragraph; right side has a portrait photo.

**Content**
- Founder name in small caps tracked-out: "[FOUNDER NAME] — Poivre Founder."
- Bio paragraph: ~60–80 words. Placeholder draft: "Poivre was founded in 2026 by [Founder Name]. After [X years] in [adjacent field] and a personal hunt for home objects that felt designed rather than decorated, [she] set out to create [her] own. Poivre is the result: a curated, drop-based home brand bringing quiet luxury within reach."
- Right column: portrait photograph (square or portrait crop) in brand-aligned styling.

> **ACCEPTANCE CRITERIA**
> - No video element anywhere on the page — hero uses a still image.
> - No "WATCH VIDEO" button, no "OUR VALUES" button.
> - Typography uses Cormorant Garamond for all headings and editorial paragraphs; Montserrat Light for body and labels.
> - Section structure matches Lesse's exactly: hero → process → sourcing → origins → founder bio.
> - Page is responsive: two-column blocks stack vertically below 768 px, founder portrait moves below bio text.

---

## 09 · Journal — Index & Article

The Journal is Poivre's editorial home — drop stories, founder notes, sourcing diaries, hosting guides. It does the brand-building work the social channels can't alone. Modeled on Lesse's journal layout with two specific simplifications for v1.

### 9.1 · Journal index

**Layout**
- Page header: "Journal" in Cormorant Garamond, ~48 px, centered or left-aligned to match Lesse.
- Article cards laid out in Lesse's alternating layout: right-aligned image with text block, left-aligned image with text block, alternating down the page. Each block has generous vertical space.
- Five articles only for v1. (Lesse shows more; Poivre has fewer at launch.)

> **CHANGE VS. LESSE** — The filter row at the top ("All", "Skin Science", "Interviews", "Culture & Curiosity") is REMOVED for v1. Poivre does not yet have enough content to justify filtering. Filters can be added in v2.

**Per-article card**
- Eyebrow label (tracked-out, ~12 px): article category — e.g. "INTERVIEW", "JOURNAL", "GUIDE". (Categories are content tags only, not visible filters in v1.)
- Date in Montserrat Light, ~13 px (e.g. "09 / 29 / 2025").
- Article title in Cormorant Garamond, ~28–32 px.
- Short excerpt in Montserrat Light, ~14 px (2–3 lines).
- "READ MORE" link in tracked-out small caps with a small square bullet to its left (matches Lesse).
- Featured image: large, vertical or landscape crop.

**Pagination**
- "LOAD MORE" link at the bottom (matches Lesse). For v1 with only 5 articles, this link is hidden.

### 9.2 · Journal article page

**Layout**
- Single-column long-form layout, max content width ~720 px, centered.
- Top: article category eyebrow, date, title in Cormorant Garamond ~48 px, optional subtitle / dek in Cormorant Garamond italic.
- Featured hero image full-width below the title.
- Body: long-form rich text. Supports paragraphs, pull-quotes (Cormorant Garamond italic, larger size), in-line images, image-with-caption blocks, and multi-image grids.
- Bottom of article: author byline (if any) in small caps; "← Back to Journal" link.

**Sharing**
- Social share row below the article body: Pinterest, Instagram story share (link), copy-link. Minimal icons, no platform-colored buttons.

**Related articles**
- "More from the Journal" — 2–3 related article cards below the share row.

> **ACCEPTANCE CRITERIA**
> - Journal index displays 5 articles in alternating-image layout.
> - No filter row visible.
> - Article page supports rich-text body content with headings, pull-quotes, in-line images, and image grids.
> - Both index and article pages use Cormorant Garamond for titles and pull-quotes, Montserrat Light for body.
> - Articles are entered as Shopify blog posts (`/blogs/journal/`) — editable in Shopify admin without code.

---

## 10 · Utility Pages

Shipping & Returns and FAQs share Lesse's utility-page layout — minimalist three-column structure with a left side-nav, a centered Q&A list, and the standard footer.

### 10.1 · Shared utility-page layout

- Left column: side-nav listing all utility pages (Shipping & Returns, FAQs, and any future stub pages). Active page is indicated with a small filled square to the left of the label. Items in tracked-out uppercase, Montserrat Light ~12 px.
- Center column: page content (Q&A list or policy body), max width ~720 px.
- Right column: empty / generous whitespace on desktop; on mobile, the side-nav collapses to a top dropdown.

### 10.2 · Shipping & Returns

**Structure**
- Section heading "SHIPPING & RETURNS" in tracked-out uppercase, Montserrat Light ~14 px.
- Q&A list mirroring Lesse: each row is a two-column structure — question on the left (Montserrat Light, ~14 px), answer on the right (same size, `--ink-primary`). 1 px divider (`--rule`) between rows.
- Below the Q&A list, a "RETURNS" section with longer-form prose (two short paragraphs).
- "CONTACT US" link at the bottom routing to `/pages/contact`.

**Content placeholders**

To be written by the brand team. Suggested questions:
- "Do you offer free shipping?"
- "Where do you ship from?"
- "Do you ship internationally?"
- "How soon will my order ship?"
- "How will I know when my order has shipped?"
- "How long does delivery take?"
- "What is your return policy?"
- "What if my order arrives damaged?"

### 10.3 · FAQs

**Structure**
- Identical layout to Shipping & Returns: side-nav on the left, two-column Q&A list in the center.
- Section heading "FAQS".
- Suggested categories (grouped within the page using small tracked-out subheads): About Poivre, Products & Care, Orders, Drops, Sustainability.

**Content placeholders**

To be written. Suggested questions:
- "What does Poivre mean?" (Answer: Poivre is French for pepper — a nod to the brand's first object, and to the daily rituals our pieces are made for.)
- "Where are your products made?"
- "How should I care for marble / ceramic / wood pieces?"
- "How often do you release new drops?"
- "Will sold-out pieces be restocked?"
- "Are your packaging materials recyclable?"

> **ACCEPTANCE CRITERIA**
> - Both utility pages share a single template (one set of theme files).
> - Side-nav is sticky on scroll, highlighting the current page.
> - Q&A two-column layout matches Lesse exactly on desktop; collapses to single column on mobile.
> - Content is editable as Shopify Pages (rich text), not hardcoded.
> - Typography uses Cormorant Garamond for any section heading > 18 px; Montserrat Light for all body and labels.

---

## 11 · Stub Pages

Contact, Privacy Policy, and Terms & Conditions are present in the navigation and footer but are intentionally minimal in v1. They are set up as Shopify Pages and editable in the admin without code changes. The brand team will populate them post-launch.

### 11.1 · Contact

**Implementation**
- Shopify Page at `/pages/contact`. Linked from the footer "Contact" link.
- Blank rich-text body in v1 — to be filled with a contact form, support email, and any social links.
- Inherits the global header, footer, and brand styling.

### 11.2 · Privacy Policy

**Implementation**
- Shopify Page at `/pages/privacy-policy`. Linked from footer and from the newsletter pop-up small print.
- Blank rich-text body in v1 — the brand team will populate using a generated legal policy or by working with counsel.
- Inherits global header, footer, and styling.

### 11.3 · Terms & Conditions

**Implementation**
- Shopify Page at `/pages/terms-conditions`. Linked from footer.
- Blank rich-text body in v1.
- Inherits global header, footer, and styling.

> **ACCEPTANCE CRITERIA**
> - All three stub pages exist at the documented URLs.
> - All three are linked from the footer.
> - All three inherit the global header, footer, free-shipping banner, and newsletter pop-up.
> - All three are editable as Shopify Pages without code changes.

---

## 12 · Responsive & Accessibility Requirements

### 12.1 · Breakpoints

| Breakpoint | Behavior |
|---|---|
| ≥ 1440 px (large desktop) | Max content width caps at 1440 px. Cream margins on either side. |
| 1024–1439 px (desktop) | Standard desktop layout. All multi-column blocks render side-by-side. |
| 768–1023 px (tablet) | Two-column blocks remain two-column where feasible. Product grids reduce from 3 to 2 columns. |
| < 768 px (mobile) | All multi-column blocks stack to single column. Hamburger menu replaces inline nav. Product grid becomes 1 column. Footer collapses to single-column stacked. |

### 12.2 · Mobile-specific requirements

- Touch targets minimum 44 × 44 px.
- Cart drawer takes full screen width.
- Newsletter pop-up width: full screen minus 32 px gutter.
- Header height reduces to 64 px.
- Hero headline drops from 56–72 px to 40–48 px.
- Hover states are replaced with tap behaviors (e.g. Shop dropdown opens on tap, not hover).

### 12.3 · Accessibility

- All images must have meaningful alt text. Decorative images use `alt=""`.
- Color contrast: `--ink-primary` on `--bg-primary` meets WCAG AA. The brand's tonal palette must be tested — cocoa-on-cream and cream-on-cocoa combinations may fall below AA at small sizes. If so, restrict cocoa to larger text only.
- Keyboard navigation: every interactive element must be reachable via Tab and operable via Enter/Space. Visible focus rings — brand-styled (a thin `--accent-cocoa` outline), not the browser default.
- Newsletter pop-up: focus trap inside modal, Esc closes, focus returns to the trigger on close.
- Cart drawer: same focus-trap behavior.
- Search input: `aria-label="Search products."`
- Form fields: label associated with input via `for`/`id`. Inline error states with `aria-describedby`.
- Skip-to-main-content link at the top of every page, hidden until focused.
- Reviews section, accordions, and tabs must use proper ARIA attributes (`aria-expanded`, `role="tab"`, etc.).

### 12.4 · Performance targets

- Largest Contentful Paint (LCP): < 2.5 s on a 4G connection.
- Cumulative Layout Shift (CLS): < 0.1.
- First Input Delay (FID) / Interaction to Next Paint (INP): < 200 ms.
- Total page weight on the homepage: < 2 MB compressed.
- Images: served as WebP with JPEG fallback, with `srcset` for responsive sizing.
- Fonts: preloaded, with `font-display: swap`.
- JavaScript: deferred where possible. Avoid render-blocking scripts.

---

## 13 · Technical Notes

### 13.1 · Theme foundation

- Fork Dawn 15.4.1 from Shopify's official theme repository.
- Maintain Dawn's file structure: `layout/`, `templates/`, `sections/`, `snippets/`, `assets/`, `config/`, `locales/`.
- Override Dawn's default sections rather than building parallel ones where possible. Custom sections (e.g. the Hyeja-style featured-item, Lesse-style product-page, journal-index) are net-new.
- Use Shopify's section schema JSON so the brand team can edit content via the theme customizer.

### 13.2 · Sections to build (new, beyond Dawn defaults)

- `section-featured-editorial.liquid` — homepage hero (two-column, mirrors Hyeja).
- `section-brand-pillars.liquid` — two stacked editorial blocks.
- `section-featured-product-banner.liquid` — full-bleed cinematic product banner.
- `section-best-sellers-grid.liquid` — 4-up product grid with eyebrow + shop-all link.
- `section-featured-bundle.liquid` — "The Set" block.
- `section-philosophy-ethos.liquid` — combined philosophy + ethos block.
- `section-pre-footer-wordmark.liquid` — large centered wordmark before footer.
- `section-product-founders-note.liquid` — product-page founders quote with images.
- `section-product-hero-details.liquid` — product-page material-story block.
- `section-product-application.liquid` — product-page cinematic video/image block.
- `section-product-reviews.liquid` — Lesse-style reviews (powered by a reviews app).
- `section-product-related.liquid` — "you may also like" 3-up.
- `section-about-hero.liquid` through `section-about-founder.liquid` — five about-page sections.
- `section-journal-index.liquid` — alternating-image article list.
- `section-utility-page.liquid` — shared layout for Shipping & Returns, FAQs.

### 13.3 · Snippets to build

- `snippet-header.liquid` — global header with sticky behavior.
- `snippet-footer.liquid` — global footer.
- `snippet-newsletter-popup.liquid` — 15-second-delay modal.
- `snippet-cart-drawer.liquid` — right-side cart drawer.
- `snippet-product-card.liquid` — reusable product card for grids.
- `snippet-shipping-banner.liquid` — top white announcement bar.

### 13.4 · Apps and integrations

- **Reviews:** Judge.me, Yotpo, or Loox — to be selected. Must support custom styling and pull review data into Liquid templates.
- **Email:** Shopify Email (v1) or Klaviyo (recommended for v1.5). Newsletter pop-up + footer signup feed the same list. The 10% discount code is auto-generated and sent on signup.
- **Analytics:** Shopify Analytics + Google Analytics 4. Optional: Hotjar or Microsoft Clarity for session recording in the first 3 months post-launch.
- **Search:** Shopify's built-in search and predictive-search APIs. Custom front-end styling required.

### 13.5 · Theme settings to expose in the customizer

The brand team must be able to edit the following without touching code:

- Free-shipping threshold and announcement banner text.
- Newsletter pop-up: headline, body text, button label, discount code, delay timer.
- Header logo asset, nav order, Shop dropdown items.
- Footer columns: link lists, social URLs.
- Homepage section order and content (every section must be individually editable).
- About page section content.
- Per-product: founders-note quote, hero-details features, application video URL.
- Color tokens (for future palette tweaks).

### 13.6 · Browser support

- Chrome, Safari, Firefox, Edge — last two major versions.
- Mobile Safari (iOS 15+) and Chrome on Android (last 2 major versions).
- No requirement to support Internet Explorer.

---

## 14 · Open Questions & Decisions Needed

These items must be resolved before or during the build. Owners are noted in parentheses.

### Content & copy

- Founder name and bio for the About page and product founders-note quotes. (Brand)
- Final hero headline and editorial copy for the homepage. (Brand)
- Philosophy + Ethos final text. (Brand)
- Five Journal articles for v1 launch. (Brand)
- Shipping & Returns and FAQs answers. (Brand)
- Privacy Policy and Terms & Conditions text. (Brand + counsel)

### Product & catalog

- Final product list for v1 launch — which SKUs go into New In, Best Sellers, The Set, and each collection. (Brand)
- Product photography — primary, thumbnails (4–6 per product), lifestyle, founders-note imagery, material close-ups. (Brand + photographer)
- Bundle composition — which products make up each Set offering. (Brand)
- Pricing per SKU and bundle. (Brand)

### Operational

- Final free-shipping threshold — €100 is placeholder. (Brand)
- Reviews app selection. (Brand + developer)
- Email platform selection — Shopify Email vs. Klaviyo for v1. (Brand)
- Social URLs (Pinterest, Instagram) — confirmed and added to theme settings. (Brand)
- Shipping zones, rates, and carriers configured in Shopify admin. (Brand + ops)

### Design assets

- Final logo files in SVG format (wordmark + monogram) for crisp rendering at all sizes. (Brand — current PNGs are usable but SVG preferred.)
- Favicon — derived from the monogram, exported at 32×32 and 16×16. (Designer)
- Open Graph / Twitter Card image — 1200 × 630 px, branded composition. (Designer)
- All photography asset deliveries finalized in WebP-compatible source format. (Photographer)

### Future / v2 considerations (not blocking v1)

- Stockist locator page once retail partnerships exist.
- Loyalty program integration.
- Multi-currency / multi-region routing.
- Filter UI on collection pages once SKU count > ~20.
- Subscription model — if introduced for soaps, candles, or consumables.
- Skin-quiz or product-finder tool (currently out of scope).

---

*— end of PRD —*
