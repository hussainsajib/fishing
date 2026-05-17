# add-fish-guide

Create a new Ontario fishing guide page for a given species.

## Arguments

`$ARGUMENTS` — the fish name to create a guide for (e.g. "walleye", "northern pike", "channel catfish")

## Steps

### 1. Check for existing guide

Read `index.html` and glob `*-fishing-guide.html` to check whether a guide for `$ARGUMENTS` (or a clearly similar species by common name, scientific name, or nickname) already exists.

- If a guide already exists and its card is `live: true` in `index.html`, tell the user which file it is and stop.
- If the fish is already in the `fish[]` array in `index.html` with `live: false`, proceed and mark it live when done.
- If the fish is not in `index.html` at all, add a new card as well as the guide page.

### 2. Determine the guide filename

Use the pattern `{slug}-fishing-guide.html` where `{slug}` is the lowercase, hyphenated species name, e.g.:
- "walleye" → `walleye-fishing-guide.html`
- "northern pike" → `northern-pike-fishing-guide.html`
- "largemouth bass" → `largemouth-bass-fishing-guide.html`
- "channel catfish" → `channel-catfish-fishing-guide.html`
- "yellow perch" → `yellow-perch-fishing-guide.html`
- "lake trout" → `lake-trout-fishing-guide.html`
- "muskellunge" → `muskellunge-fishing-guide.html`
- "smallmouth bass" → `smallmouth-bass-fishing-guide.html`

### 3. Research the species

Before writing, think carefully about this species in **Ontario waters specifically**:

- What habitats does it prefer? (rivers, lakes, weedy bays, deep cold water, etc.)
- When are the best seasons and times of day to fish for it in Ontario?
- What rod, reel, line, and terminal tackle is most appropriate?
- What rigs and presentations are most effective?
- What bait and lures work best, and how should they be prepared or presented?
- Which specific Ontario locations are well-known for this species (with real place names, towns, rivers, lakes, and parks)?
- What techniques and skills separate consistent anglers from occasional ones?

### 4. Create the guide page

Create `{slug}-fishing-guide.html` as a **self-contained single HTML file** following the exact same architecture as `carp-fishing-guide.html` and `steelhead-fishing-guide.html`. Read one of those files if you need to verify the exact structure.

**Required structure:**
- Same `<style>` block (copy it, only change header gradient colours to fit the species theme)
- Same `<nav id="nav">` + `<div id="content">` skeleton
- Same `buildContent()` → `buildNav()` rendering pipeline
- Same `let imgSeed = 100` at top of script (never reset mid-page)

**Required sections array — 7 sections minimum:**

| id | label | what it covers |
|---|---|---|
| `reading` | Reading the Water / Habitat | How and where to locate this species |
| `timing` | Best Times | Seasons, time of day, weather triggers |
| `tackle` | Rod & Reel Setup | Species-appropriate gear with specific product examples |
| `rigs` | Rigs & Presentations | Terminal tackle setups with step-by-step descriptions |
| `bait` | Bait & Lures | Bait prep, lure selection, colour/size by condition |
| `locations` | Ontario Locations | 8–12 real, named Ontario spots with Google Maps links |
| `technique` | Technique | Execution details — casting, retrieve, playing fish, C&R |

**Extra panels:**
- `timing` section: add `extra: "seasons"` with a `seasons[]` array (4 entries: Spring/Summer/Autumn/Winter, rating 1–5, note)
- `tackle` section: add `extra: "checklist"` with a `checklist[]` flat string array (12–16 items)
- `bait` section: add `extra: "baittemp"` with a `baitTemps[]` array (4 temperature bands, species-appropriate bait recommendations)

**Tip depth:** Each tip must have a `title`, a substantive `detail` (3–6 sentences of real, practical, Ontario-specific advice), `imgs` (2–3 Unsplash keyword phrases), and `map` for location tips (Google Maps search URL using `https://www.google.com/maps/search/?api=1&query=...`).

**Header colours:** Pick a 2–3 colour gradient that suits the species visually. The primary `color` hex from the `fish[]` array in `index.html` should be the dominant accent.

**`bar.on` colour in the CSS:** Change from the carp guide's `#b5451b` or steelhead's `#7b2d8b` to the primary colour of this species' card.

### 5. Update index.html

Read `index.html`. Find the entry for this species in the `fish[]` array:
- If it exists: set `live: true`, set `href` to the new filename, update `desc` if the current description is a placeholder (under 100 characters or doesn't mention the guide content).
- If it doesn't exist: add a new object to the `fish[]` array with `id`, `icon`, `name`, `latin`, `color`, `desc`, `difficulty`, `href`, `live: true`. Pick an appropriate emoji icon and a colour that doesn't duplicate existing entries.

### 6. Commit and push

Stage only the new guide file and `index.html`. Commit with a message in this format:

```
Add {species name} fishing guide ({N} sections, {N} Ontario locations)

- New {filename}: list the 7 section names
- Seasons panel, bait temperature guide, and starter checklist
- index.html: {species} card set live
```

Then push to `origin master`.

## Quality checklist before committing

- [ ] All 7 sections present in `sections[]`
- [ ] Every section has at least 5 tips
- [ ] Every location tip has a `map` Google Maps URL
- [ ] `seasons[]`, `baitTemps[]`, and `checklist[]` arrays are declared and used
- [ ] `imgSeed` starts at 100, never reset
- [ ] `buildContent()` called before `buildNav()`
- [ ] `index.html` card is `live: true` with correct `href`
- [ ] File committed and pushed successfully
