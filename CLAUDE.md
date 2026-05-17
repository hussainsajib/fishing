# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

A static HTML fishing guide site targeting Ontario waters. No build step, no dependencies, no server required — open any file directly in a browser.

**File structure:**
- `index.html` — Home page listing all species guides as cards. Fish data lives in the `fish[]` array in the script; add an entry there to add a new species card. Set `live: true` and `href` once the guide page exists.
- `carp-fishing-guide.html` — Carp guide; the reference implementation for all future species guide pages.
- Future species pages follow the naming pattern `{species}-fishing-guide.html`.

---

## Species guide page architecture

Every species guide is a **self-contained single HTML file** with three logical sections: CSS, data, and rendering. The page is a single continuous scroll — all sections render at once; there are no tabs.

### CSS (`<style>`)

Dark green / earth-tone theme shared across all guide pages. Key classes:

| Class | Purpose |
|---|---|
| `.header` | Hero banner with gradient background; holds `.back-link` (top-left, links to `index.html`) |
| `.nav` | Sticky horizontal scroll-to bar; auto-highlights the section currently in view |
| `.nav-btn` | `<a>` anchor tag styled as a pill; `active` class + inline `background` colour set by JS |
| `.fish-section` | Wraps one content section; has an `id` matching `sections[].id` for anchor scrolling |
| `.section-break` | Full-width horizontal rule between sections |
| `.tip-card` | Individual tip card with a coloured left border driven by `sections[].color` |
| `.tip-map-link` | Green pill anchor; only rendered when `tip.map` is set |
| `.video-section` / `.video-thumb` | YouTube embed grid with thumbnail + play button overlay |
| `.extra-panel` | Optional panel appended after the tip list (seasons, bait-temp table, checklist) |

### Data (top of `<script>`)

All content is declared as plain JS objects **before** any rendering logic.

**`sections[]`** — one object per page section:
```js
{
  id: "string",        // used as the anchor id and nav data-id
  icon: "emoji",
  label: "string",     // nav button text
  color: "#hex",       // drives left border, icon bg, nav highlight
  intro: "string",     // subtitle shown under the section heading
  videos: [{ id: "youtubeId", label: "string" }],  // omit or empty array for none
  tips: [ /* see tip shape below */ ],
  extra: "seasons" | "baittemp" | "checklist"  // optional; omit for no panel
}
```

**Tip shape inside `tips[]`:**
```js
{
  title: "string",
  detail: "string",    // rendered as innerHTML — HTML tags are safe here
  map: "https://www.google.com/maps/search/?api=1&query=...",  // optional
  imgs: ["keyword phrase", ...]   // Unsplash search keywords, 1–3 per tip
}
```

**Supporting data arrays** (declared once, referenced by `renderExtra()`):
- `seasons[]` — `{ season, rating (1–5), note }` — used by `extra: "seasons"`
- `baitTemps[]` — `{ temp, baits, bg }` — used by `extra: "baittemp"`
- `checklist[]` — flat string array — used by `extra: "checklist"`

### Rendering (bottom of `<script>`)

Three functions, called once on load in this order: `buildContent()` then `buildNav()`.

**`buildContent()`** — iterates `sections[]` and sets `content.innerHTML` to the full page in one pass. Each section becomes a `<section class="fish-section" id="{s.id}">` block containing: section header, divider, tip cards, optional video grid, optional extra panel. Sections are separated by `<div class="section-break">`.

**`renderExtra(s)`** — pure function; returns the extra panel HTML string for a given section, or `""`. Add new panel types here with a new `if (s.extra === "yourtype")` branch and a matching data array.

**`buildNav()`** — renders `<a class="nav-btn">` anchor links, then attaches an `IntersectionObserver` to every `.fish-section`. As sections enter the viewport (`rootMargin: "-20% 0px -70% 0px"`), the matching nav link gets the `active` class and the section's `color` set as its inline background. The nav also calls `scrollIntoView` on the active button to keep it visible in the horizontal scroll bar.

**`imgSeed`** — module-level counter starting at 100; increments once per image call to `unsplashUrl()`. Prevents Unsplash from returning the same image for similar keywords on the same page load. Never reset it mid-page.

---

## Adding content

**New species guide page:** copy `carp-fishing-guide.html`, replace the `sections[]` / `seasons[]` / `baitTemps[]` / `checklist[]` data, update the `<title>` and header text. Then add an entry to the `fish[]` array in `index.html` with `live: true` and the correct `href`.

**New section inside a guide:** add an object to `sections[]`. The `color` hex drives all accent colours for that section automatically.

**New tip:** add to `tips[]` inside the relevant section. Set `map` to a `https://www.google.com/maps/search/?api=1&query=` URL to show the Google Maps pill button.

**New extra panel type:** add a data array at the top of the script, then add an `if (s.extra === "yourtype")` branch inside `renderExtra()`.
