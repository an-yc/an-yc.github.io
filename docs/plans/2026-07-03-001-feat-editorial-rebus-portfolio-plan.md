---
artifact_contract: ce-unified-plan/v1
artifact_readiness: implementation-ready
execution: code
product_contract_source: ce-plan-bootstrap
type: feat
title: "feat: Editorial rebus single-page portfolio for Anissa Ng"
date: 2026-07-03
---

# feat: Editorial Rebus Single-Page Portfolio for Anissa Ng

**Product Contract preservation:** No upstream brainstorm; source is the user's product spec (copy is final, verbatim). Requirements below trace directly to that spec.

## Summary

Replace the current `an-yc.github.io` single-page site with a minimal editorial "rebus" portfolio. The one graphic trick: large grotesque headline sentences with small inline images set to cap height, sitting inside the text flow so they read as part of the sentence (see reference: NYT hiring card, breef.com card, Grayston Herriott). Warm off-white ground (`#F4F4F1`), near-black text, one restrained accent. Static HTML/CSS, no build step, deployable to GitHub Pages. Copy is final as written in the spec — do not rewrite it.

The site keeps the existing repo's zero-build, GitHub-Pages-native shape (`index.html` + `styles.css`) but is a full visual redesign — the old warm-card / orbiting-skills layout is replaced, not extended.

---

## Problem Frame

The current site (`index.html`, `styles.css`) is a friendly card-based layout with animated skills orbit, Halloween photobooth, and "people who inspire me" cards. It does not reflect Anissa's repositioning as a HK lawyer → AI product builder, and its aesthetic (rounded cards, emoji, Framer-style animation) undersells product seriousness to the target audience: recruiters, hiring managers, and legaltech collaborators evaluating product ability.

The new site must read as an editorial magazine object: refined type system used identically across sections, generous whitespace, no cards/shadows/gradients, and the inline-rebus device as the single memorable signature.

**In scope:** Full rebuild of the single page — hero, TOC, four content sections (Legal Tech, Auteur, Design & Dev, Legal), footer; rebus inline-image system; responsive reflow; smooth anchor scrolling; hover on inline images; SEO/OG meta preserved and updated for new positioning; project screenshots in keyline frames with live links.

**Out of scope (non-goals):** CMS, analytics beyond the existing GA tag decision (see KTD-6), dark mode, animation beyond hover, backend, framework/build tooling. Final project screenshots (user supplies) — placeholders acceptable.

---

## Requirements

Traceability IDs used by implementation units and test scenarios.

| ID | Requirement |
|----|-------------|
| R1 | Single static `index.html` + `styles.css`, no build step, GitHub-Pages deployable. |
| R2 | Rebus inline images sit at cap height, optically aligned to the headline text across all font-size breakpoints; consistent visual weight; grayscale/duotone. |
| R3 | Rebus sentences reflow on mobile without orphaning an inline image from its adjacent word. |
| R4 | Type system: grotesque for rebus headlines; Caslon-style serif for body + italic section names; monospace for indices/captions. Applied identically in every section. |
| R5 | Warm off-white ground `#F4F4F1`, near-black text, exactly one restrained accent color. No cards, shadows, or gradients. |
| R6 | Structure: Hero → TOC → 01 Legal Tech → 02 Auteur → 03 Design & Dev → 04 Legal → Footer, in that order. Copy verbatim from spec. |
| R7 | TOC is a minimal numbered index; each entry anchors (smooth-scrolls) to its section. |
| R8 | Inline images have subtle hover (slight tilt or swap); no other animation. |
| R9 | Every inline image has an explicit intrinsic width/height (or reserved box) so missing/slow images cause zero layout shift; alt text present. |
| R10 | CJK characters (貳鄉) render in a suitable CJK font. |
| R11 | Fully responsive; fast load; semantic HTML (`<header>`, `<nav>`, `<main>`, `<section>`, `<footer>`, headings in order). |
| R12 | SEO meta preserved from current site and updated to new positioning: title, description, keywords, canonical, Open Graph, Twitter card, JSON-LD Person. |
| R13 | Project screenshots/demos shown in thin keyline frames; live links where the spec gives them (Size Test Assistant → `size-test-assistant.vercel.app`). |
| R14 | Respect `prefers-reduced-motion`: disable hover tilt / smooth-scroll motion for users who opt out. |

---

## Key Technical Decisions

- **KTD-1 — Rebus glyphs are inline SVG, not scraped PNGs (first version).** Each rebus mark ships as an inline `<svg>` in an editorial single-weight line/engraving style, wrapped in a `.rebus` span. Rationale: (a) consistent visual weight is guaranteed by drawing them as one set, vs. the licensing + tonal-mismatch risk of public-domain photo cutouts sourced at build time; (b) crisp at any breakpoint, so cap-height alignment holds without per-image tuning; (c) zero external requests, zero layout shift, works offline. The markup is swap-friendly: a `.rebus` span can later hold a photographic `<img>` cutout instead of the SVG with no CSS change. Grayscale/duotone requirement (R2) is met via `currentColor` + a single accent-tinted variant. **Alternative considered:** source public-domain PNG cutouts (bust, gavel, skyline, Monet) — deferred; higher effort, inconsistent weight, and the user can supply finals later.
- **KTD-2 — Cap-height alignment via `height: 0.72em` (approx cap-height ratio) + `vertical-align` tuned per set, not per glyph.** Inline marks are sized in `em` so they scale with the headline at every breakpoint (R2). One shared rule; a single `--rebus-cap` variable tunes the whole set. Avoids the fragile per-image baseline nudging seen when mixing arbitrary raster sizes.
- **KTD-3 — Orphan prevention with `white-space: nowrap` on a word+glyph unit.** Each rebus image is wrapped together with its adjacent word in a `<span class="keep">` carrying `white-space: nowrap`, so the word and its image never break across lines (R3). The sentence still wraps between units.
- **KTD-4 — Fonts via Google Fonts with system fallbacks and `font-display: swap`.** Grotesque headline + Caslon-style serif body + monospace captions (R4). Caslon: `Libre Caslon Text` (Google) with `Georgia, serif` fallback. Grotesque: a neutral grotesque already loadable (e.g. `Instrument Sans` — already used in-repo — or `Space Grotesk`); pick one and use it identically. Monospace: a quiet mono (e.g. `Space Mono` / `IBM Plex Mono`) with `ui-monospace` fallback. CJK: `Noto Serif TC` (already referenced in current `index.html`) covers 貳鄉 (R10). Reserve exact family selection to the implementer per availability, but the three-role system is fixed.
- **KTD-5 — One accent color, used sparingly.** A single restrained accent (deep oxblood/ink-red or muted ink-blue) for links, the accent-tinted rebus variant, and the TOC/section numerals only. No gradients, no second accent (R5). Implementer picks the exact hex; document it as a CSS variable `--accent`.
- **KTD-6 — Keyline frames are 1px borders in near-black at low opacity, no shadow.** Project screenshots sit in `border: 1px solid` keyline frames (R13); dimension-reserved to avoid CLS (R9).
- **KTD-7 — Preserve GA tag decision explicitly.** The current site has a Google Analytics tag. Spec says "no analytics" as an acceptable-rough-edge omission. Decision: **keep** the existing GA snippet (behavior-neutral, already live, removing it is a separate product call) but do not add new analytics. Flag in PR for user override.
- **KTD-8 — Smooth scroll via CSS `scroll-behavior: smooth` on `:root`, gated by `prefers-reduced-motion`.** No JS scroll library (R7, R14). A tiny progressive-enhancement JS is acceptable only if needed for offset headers; prefer pure CSS.

---

## High-Level Technical Design

### Page structure

```
<header id="top">           Hero: rebus sentence + monospace dateline
<nav>                       Table of contents (numbered index → anchors)
<main>
  <section id="legal-tech"> 01 — headline (rebus) + intro + 3 projects
  <section id="auteur">     02 — headline (rebus) + body
  <section id="design-dev"> 03 — headline (rebus) + 2 projects
  <section id="legal">      04 — headline (rebus) + body
</main>
<footer>                    Say hello rebus + links
```

### Rebus unit anatomy (the one device)

```
<h1 class="rebus-headline">
  Anissa
  <span class="keep">is a lawyer <span class="rebus" aria-hidden="true"><svg …gavel…></svg></span></span>
  who builds
  <span class="keep"><span class="rebus"><svg …cursor…></svg></span> products</span>
  in
  <span class="keep">Hong Kong <span class="rebus"><svg …skyline…></svg></span></span>.
</h1>
```

- `.rebus` — inline-flex box, `height: var(--rebus-cap)`, `vertical-align` tuned, `border-radius` small (matches reference), hover tilt.
- `.keep` — `white-space: nowrap` binds word+image (R3).
- Decorative marks carry `aria-hidden="true"`; the sentence reads correctly to screen readers from the surrounding text. Marks that *replace* a word (rare here) instead carry `role="img"` + `aria-label`.

### Rebus glyph inventory (14 marks, one SVG set)

bust · gavel · cursor · skyline · book · terminal · phone · Monet-detail · pointer · serif-A · column · brick · wave · envelope · in(LinkedIn). Drawn as one cohesive line/engraving set (KTD-1).

---

## Output Structure

```
index.html          # rewritten: semantic sections, inline rebus SVGs, updated meta
styles.css          # rewritten: type system, rebus alignment, responsive, keylines
images/
  rebus/            # (optional) photographic cutout swaps, if user later supplies
  size-test.png     # placeholder project screenshot (keyline framed)
  chatbot.png       # placeholder
  auteur.png        # placeholder
  isto.png          # exists — reuse
  2home.png         # exists — reuse (large; see U6 note on optimization)
docs/plans/…        # this plan
```

Existing `images/` assets (`isto.png`, `2home.png`, `og-image.png`, `ani.jpeg`) are reused where relevant. The old JS-driven skills orbit and its assets (`sputnik.gif`, `studio_illustration.png`) are dropped from the page.

---

## Implementation Units

### U1. Design tokens, type system, and page skeleton

**Goal:** Establish CSS variables (ground, ink, accent, rebus-cap), load the three-role font system, and lay down semantic HTML skeleton with all sections/anchors in order.
**Requirements:** R1, R4, R5, R6, R10, R11.
**Dependencies:** none.
**Files:** `index.html`, `styles.css`.
**Approach:** Define `:root` variables (`--ground:#F4F4F1`, `--ink`, `--accent`, `--rebus-cap`, font stacks). Load Google Fonts (grotesque, Libre Caslon Text, mono, Noto Serif TC) with `preconnect` + `font-display: swap`. Build `<header><nav><main>` with the four `<section id>`s and `<footer>` empty-but-structured. CSS reset + editorial margins (generous max-width measure, large side gutters).
**Patterns to follow:** Current `index.html` head block for `preconnect`/font-link idiom; keep favicon data-URI.
**Test scenarios:**
- Page loads with `#F4F4F1` computed background on `body`.
- All four section anchors (`#legal-tech`, `#auteur`, `#design-dev`, `#legal`) exist and are unique.
- Heading order is semantic (single `<h1>` in hero, `<h2>` per section).
- `貳鄉` renders in a CJK serif (no tofu) — visual check.
- `Test expectation:` structural/style unit; verification is DOM presence + computed-style checks, not business logic.

### U2. Rebus inline-image system (SVG set + alignment CSS)

**Goal:** Build the 14-mark SVG glyph set and the `.rebus` / `.keep` CSS so marks sit at cap height, aligned, weight-consistent, with orphan prevention and hover tilt.
**Requirements:** R2, R3, R8, R9, R14.
**Dependencies:** U1.
**Files:** `index.html` (inline SVG symbols or inline `<svg>` per use), `styles.css`.
**Approach:** Draw glyphs as a single-weight set. Option: define once as `<symbol>`s in a hidden `<svg>` sprite at top of `<body>`, reference via `<use>` — keeps markup readable and guarantees one source per glyph. `.rebus { display:inline-flex; height:var(--rebus-cap); width:auto; vertical-align: <tuned>; border-radius: 3px; transition: transform .18s; }`. `.rebus:hover { transform: rotate(-3deg); }`. `.keep { white-space: nowrap; }`. Reserve intrinsic aspect via `aspect-ratio` on `.rebus` so a failed/late image holds its box (R9). `@media (prefers-reduced-motion: reduce)` disables the tilt.
**Patterns to follow:** Reference images (NYT/breef) for size ratio and corner radius.
**Execution note:** Get one headline (hero) pixel-right for cap-height alignment first, then reuse the exact rule for all others — alignment is the highest-risk detail.
**Test scenarios:**
- A `.rebus` mark's rendered height is within ±1px of the cap height of adjacent capital letters at desktop headline size. *(Covers R2.)*
- At 375px viewport, no `.keep` unit breaks between its word and image (word+image share a line). *(Covers R3.)*
- With an `<img>`-based rebus whose `src` is removed, the surrounding text does not shift (box reserved). *(Covers R9.)*
- `:hover` applies a transform; under `prefers-reduced-motion: reduce` it does not. *(Covers R8, R14.)*
- Decorative marks are `aria-hidden`; word-replacing marks expose `aria-label`.

### U3. Hero + Table of Contents

**Goal:** Ship the hero rebus sentence with monospace dateline, and the numbered TOC index that smooth-scrolls to sections.
**Requirements:** R6, R7, R4, R14.
**Dependencies:** U2.
**Files:** `index.html`, `styles.css`.
**Approach:** Hero `<h1>`: "Anissa [bust] is a lawyer [gavel] who builds [cursor] products in Hong Kong [skyline]." + `<p class="dateline">Hong Kong · 2026</p>` in mono. TOC `<nav>`: ordered list, `01–04` numerals in mono/accent, section names in italic serif, each an `<a href="#…">`. `scroll-behavior: smooth` on `:root`, gated by reduced-motion.
**Patterns to follow:** Reference for headline scale; keep it the dominant first-viewport element.
**Test scenarios:**
- Hero contains exactly the 4 specified rebus marks in order.
- Dateline "Hong Kong · 2026" is monospace.
- Clicking a TOC link navigates to the matching section id (anchor resolves).
- TOC numerals `01`–`04` present and map to the four sections.

### U4. Section 01 — Legal Tech (headline + intro + 3 projects)

**Goal:** Headline "Turning listing rules [book] into software [terminal].", intro paragraph, and three project entries (Size Test Assistant with live link + keyline screenshot; Listing Rules Chatbot; LegalQuants).
**Requirements:** R6, R13, R4, R5.
**Dependencies:** U2, U3.
**Files:** `index.html`, `styles.css`, `images/size-test.png` (placeholder), `images/chatbot.png` (placeholder).
**Approach:** `<section id="legal-tech">` with `<h2>` rebus headline, serif intro (verbatim), then three project blocks. Size Test Assistant: keyline-framed screenshot + "Try it live → size-test-assistant.vercel.app" link (accent). No cards — projects separated by whitespace + hairline rules or mono index labels, not boxes.
**Patterns to follow:** Keyline frame = 1px border, dimension-reserved (KTD-6, R9).
**Test scenarios:**
- Headline has `book` and `terminal` marks in order.
- Size Test Assistant link points to `https://size-test-assistant.vercel.app` and opens in new tab with `rel="noopener"`.
- All three project copy blocks present verbatim.
- Screenshots sit in keyline frames with reserved dimensions (no CLS on slow load). *(Covers R13, R9.)*

### U5. Section 02 — Auteur (headline + body)

**Goal:** Headline "An app [phone] for learning culture [Monet detail], slowly." + body paragraph verbatim.
**Requirements:** R6, R4.
**Dependencies:** U2, U3.
**Files:** `index.html`, `styles.css`, `images/auteur.png` (placeholder, optional).
**Approach:** `<section id="auteur">`. Monet-detail mark is the tonal centerpiece — duotone/grayscale per R2. Optional single keyline screenshot.
**Test scenarios:**
- Headline has `phone` and `Monet-detail` marks in order.
- Body copy present verbatim; no rewrite.

### U6. Section 03 — Design & Dev (headline + 2 projects)

**Goal:** Headline "Interfaces [pointer] with editorial [serif 'A'] bones." + ISTO and 2HOME Studio entries (2HOME includes 貳鄉).
**Requirements:** R6, R10, R13.
**Dependencies:** U2, U3.
**Files:** `index.html`, `styles.css`, reuse `images/isto.png`, `images/2home.png`.
**Approach:** `<section id="design-dev">`. Two project blocks. 貳鄉 rendered in `Noto Serif TC`. **Optimization note:** `images/2home.png` is 4.8MB — downscale/compress to a web-appropriate keyline-framed screenshot in this unit (or reserve dimensions and lazy-load) to protect fast-load (R11).
**Test scenarios:**
- Headline has `pointer` and `serif-A` marks in order.
- 貳鄉 renders without tofu in a CJK serif. *(Covers R10.)*
- ISTO and 2HOME copy present verbatim.
- 2HOME screenshot is compressed/lazy-loaded (network payload check or `loading="lazy"` present).

### U7. Section 04 — Legal + Footer

**Goal:** Headline "Five years [column] of capital markets, now the foundation [brick]." + body; footer "Say hello [wave] → email [envelope] · LinkedIn [in]." with real links.
**Requirements:** R6, R4.
**Dependencies:** U2, U3.
**Files:** `index.html`, `styles.css`.
**Approach:** `<section id="legal">` + `<footer>`. Footer envelope → `mailto:`, LinkedIn → profile URL (carry from current site if present, else placeholder flagged in PR). Wave mark inline.
**Test scenarios:**
- Legal headline has `column` and `brick` marks; body verbatim.
- Footer has `wave`, `envelope`, `in` marks; email is a `mailto:` link; LinkedIn is an absolute `https://` link with `rel="noopener"`.

### U8. SEO/meta migration + responsive polish + accessibility pass

**Goal:** Port and update all head meta for new positioning; final responsive breakpoints; reduced-motion; validate no layout shift.
**Requirements:** R12, R11, R14, R9.
**Dependencies:** U1–U7.
**Files:** `index.html`, `styles.css`.
**Approach:** Update `<title>`, description, keywords, canonical (`https://an-yc.github.io/`), OG (title/description/image/url), Twitter card, JSON-LD Person to reflect "lawyer who builds products" positioning while preserving the fields that exist today (KTD-7 keeps GA). Responsive: fluid headline type (`clamp()`), gutters shrink on mobile, TOC stacks. Final `prefers-reduced-motion` audit across hover + smooth-scroll.
**Test scenarios:**
- `<title>`, `meta[name=description]`, `link[rel=canonical]`, `og:*`, `twitter:*`, JSON-LD Person all present and reflect new copy. *(Covers R12.)*
- At 320px / 768px / 1440px the layout is intact: no horizontal scroll, headlines wrap without orphaned marks. *(Covers R11, R3.)*
- Reduced-motion disables tilt and smooth scroll. *(Covers R14.)*
- Lighthouse/visual: no cumulative layout shift from images. *(Covers R9.)*

---

## Verification Contract

- **Visual:** Open `index.html` in a browser (and via a local static server for correct font/anchor behavior). Confirm at 320/768/1440px: warm ground, rebus marks aligned to cap height, no orphaned marks, keyline frames render, TOC anchors scroll smoothly.
- **Structural:** Validate semantic HTML (headings in order, landmarks present, alt text on meaningful images, `aria-hidden` on decorative marks).
- **Copy fidelity:** Diff every headline/body/project string against the spec — must be verbatim.
- **Performance:** No layout shift on throttled load; total transferred page weight reasonable (compress `2home.png`).
- **SEO:** All meta tags present and updated; JSON-LD parses.
- **Deploy target:** Loads correctly as a static file / GitHub Pages root (`index.html` at repo root).

## Definition of Done

All of R1–R14 satisfied; every implementation unit's test scenarios pass; copy verbatim; site renders correctly at three breakpoints with reduced-motion honored; SEO meta updated; page committed with `index.html` + `styles.css` at repo root; PR opened flagging (a) placeholder screenshots for user finals, (b) GA-tag keep decision, (c) exact accent hex and font-family final choices, (d) LinkedIn/email URLs if placeholders were used.

---

## Scope Boundaries

**In scope:** everything in Requirements R1–R14.

### Deferred to Follow-Up Work
- Photographic public-domain rebus cutouts (swap for SVG marks) — markup is swap-ready (KTD-1).
- Final project screenshots (user supplies).
- Per-project detail pages / case studies.

### Non-goals (out of this product's identity)
- CMS, build tooling, frameworks, backend.
- Dark mode.
- Analytics beyond the existing GA tag.
- Any animation beyond inline-image hover.

---

## Open Questions

- **Exact accent hex** — implementer picks a single restrained accent (oxblood/ink-red or muted ink-blue); documented as `--accent`, flagged in PR for user sign-off. (Non-blocking; defaulting is fine.)
- **Final grotesque family** — `Instrument Sans` (already in repo) vs `Space Grotesk`. Non-blocking; pick one, use identically.
- **LinkedIn / email URLs** — reuse from current site if present; else placeholder flagged in PR.

---

## Risks & Dependencies

- **Cap-height alignment fragility (R2)** — mitigated by KTD-2 (single `em`-based rule, SVG marks) and U2's "hero-first" execution note.
- **Mobile orphaning (R3)** — mitigated by KTD-3 `.keep` nowrap units; explicitly tested at 375/320px.
- **`2home.png` is 4.8MB** — fast-load risk; U6 compresses/lazy-loads it.
- **Font FOUT** — `font-display: swap` + system fallbacks; acceptable for first version.
- **External dep:** Google Fonts + Google Analytics (existing). No other third-party runtime dependency.
