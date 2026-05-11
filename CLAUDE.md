# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

A static HTML fishing guide site targeting Ontario waters. No build step, no dependencies, no server required — open any file directly in a browser.

**File structure:**
- `index.html` — Home page listing all species guides as cards. Fish data lives in the `fish[]` array in the script; add an entry there to add a new species card. Set `live: true` and `href` once the guide page exists.
- `carp-fishing-guide.html` — Full carp guide (single scrolling page, all sections visible). Links back to `index.html` via the header back-link.

## Architecture

Everything lives in one file with three logical sections:

**CSS** (inside `<style>`) — dark green/earth-tone theme. Key classes: `.tip-card` (per-tip bordered card), `.tip-map-link` (Google Maps pill button), `.video-section` / `.video-thumb` (YouTube embed grid), `.extra-panel` (season/bait/checklist panels).

**Data** (top of `<script>`) — all content is declared as plain JS objects before any rendering logic:
- `sections[]` — each tab: `{ id, icon, label, color, intro, videos[], tips[], extra? }`. Tips support `{ title, detail, map?, imgs[] }`. The `detail` field is injected as `innerHTML` so it can contain HTML tags (`<strong>`, `<br>`, bullet `•`).
- `seasons[]`, `baitTemps[]`, `checklist[]` — data for the three `extra` panel types (`"seasons"`, `"baittemp"`, `"checklist"`).

**Rendering** (bottom of `<script>`) — two functions rebuild the DOM on every tab switch:
- `buildNav()` — renders nav buttons, wires click handlers, sets `active` section id.
- `buildContent()` — renders the active section's header, tip cards, YouTube video grid, and optional extra panel entirely via `innerHTML`.

Images are fetched from `source.unsplash.com` using keyword strings stored in `tip.imgs[]`. The `imgSeed` counter increments per image to avoid duplicate Unsplash results; it resets to 100 on each tab switch.

## Adding content

**New tab/section:** add an object to `sections[]` following the existing shape. The `color` hex drives the left border, icon background, and nav highlight. Set `extra` to `"seasons"`, `"baittemp"`, or `"checklist"` to append one of the pre-built panels, or omit it for none.

**New tip inside a section:** add to that section's `tips[]`. Include `map` with a `https://www.google.com/maps/search/?api=1&query=` URL to show the Google Maps pill. The `detail` string renders as HTML.

**New extra panel type:** add a new `if (s.extra === "yourtype")` block inside `buildContent()` and declare the data at the top of the script alongside `seasons`, `baitTemps`, and `checklist`.
