# CLAUDE.md — Raisa Letters Archive

## Project Overview

Digital archive of letters and diary of **Raisa Pogorelova** (Раїса Григорівна Погорелова), a Ukrainian Ostarbeiter (forced laborer) in Nazi Germany, 1942–1944. 180 handwritten pages — letters home and two diary notebooks — preserved by the Prokopov family and transcribed in 2026 with AI assistance.

**Live site:** https://raisa-diary-1942.org
**Repo:** https://github.com/mprokopov/raisa-letters

## Tech Stack

- **Hugo** static site generator (v0.142.0, extended)
- **Custom theme** at `themes/raisa/` — archival serif aesthetic (Georgia, sepia tones)
- **Trilingual:** English (default), Russian (`*.ru.md`), German (`*.de.md`)
- **Deployment:** GitHub Actions → Cloudflare Pages (`cloudflare/wrangler-action`)
- **Analytics:** Plausible (self-hosted at `analytics.prokopov.me`)
- **Maps:** Leaflet.js (OpenStreetMap) for the journey route

## Content Structure

```
content/
├── about/          # About page (_index.md, .ru.md, .de.md)
├── letters/        # 54 letters (×3 languages = 162 files)
├── diary/          # 7 diary entries (×3 languages = 21 files)
├── timeline/       # Hardcoded in layout (timeline/list.html)
├── people/         # Hardcoded in layout (people/list.html)
└── map/            # Leaflet map, hardcoded in layout (map/list.html)
```

### Content file conventions

- **Filename:** `YYYY-MM-DD-slug.md` (English), `YYYY-MM-DD-slug.ru.md` (Russian), `YYYY-MM-DD-slug.de.md` (German)
- **Undated items:** `YYYY-unknown-slug.md` or `YYYY-undated-slug.md`
- **Front matter:**
  ```yaml
  title: "May 11, 1942 — The Earliest Letter: Travel Route to Germany"
  date_written: "May 11, 1942"
  weight: 51          # Controls sort order in listing
  scans: ["SCAN0082", "SCAN0083"]   # References to static/scans/
  summary: "Short description for the letter list"
  ```
- **Body structure:** `## Transcription` (blockquoted original text) → `## Context` (historical notes, cross-references)

### Scans

180 JPG files in `static/scans/` (SCAN0003.JPG – SCAN0182.JPG). Referenced by filename stem in front matter `scans` array. Displayed via `scan-gallery` CSS grid in single.html.

### Photographs

4 WebP files in `static/scans/` (SCAN0187–SCAN0190) — the only known wartime photos of Raisa:
- **SCAN0187.webp** — Back of birthday portrait (inscription: date, location, "turned 18")
- **SCAN0188.webp** — Portrait of Raisa, age 18 (Sept 16, 1942, Ebrach)
- **SCAN0189.webp** — Raisa with companion (Sept 1944, Ebrach)
- **SCAN0190.webp** — Back of companion photo (inscription: "Ebrach IX 1944г.")

All photos use transparent backgrounds (WebP with alpha) and `filter: drop-shadow()` instead of `box-shadow` to follow the irregular card edges. Referenced directly in layout templates and About page content (not via front matter `scans` array).

## Theme Layout

All layouts in `themes/raisa/layouts/`:

| Layout | Purpose |
|--------|---------|
| `_default/baseof.html` | Base shell (head + header + nav + main + footer) |
| `_default/single.html` | Individual letter/diary entry (scan gallery + content) |
| `_default/list.html` | Letter/diary index (date + title + summary) |
| `index.html` | Homepage (hero images, story, stats, CTA links) |
| `timeline/list.html` | Timeline — **hardcoded HTML**, trilingual with `$lang` conditionals |
| `people/list.html` | People directory — **hardcoded HTML**, trilingual, card grid |
| `map/list.html` | Leaflet map with route polyline — **hardcoded JS** |
| `partials/header.html` | Site title + subtitle + language switcher + nav |
| `partials/head.html` | Meta tags + CSS + Plausible analytics |
| `partials/footer.html` | Footer with family credit |

## Internationalization

- **i18n files:** `i18n/en.toml`, `i18n/ru.toml`, `i18n/de.toml` — UI labels only
- **Content translations:** Separate files per language (`*.md` = EN, `*.ru.md` = RU, `*.de.md` = DE)
- **Layout translations:** Timeline, People, and Map pages use inline `{{ if eq $lang "ru" }}...{{ else if eq $lang "de" }}...{{ else }}...{{ end }}` blocks
- **Language switcher:** In header partial, links between translations of the same page

## Configuration

`hugo.toml` defines:
- Three languages with separate menus (7 nav items each: Home, Letters, Diary, Timeline, People, Map, About)
- Taxonomies: `person = "people"`, `tag = "tags"`
- Unsafe HTML rendering enabled (`markup.goldmark.renderer.unsafe = true`)

## Build & Deploy

```bash
# Local development
hugo server -D

# Production build
hugo --gc --minify -b https://raisa-diary-1942.org

# Deploy (automated via GitHub Actions on push to main)
# See .github/workflows/deploy.yml
```

## Key Design Decisions

- **Archival aesthetic:** Serif fonts, sepia palette, no modern UI frameworks — respects the historical material
- **Original language preserved:** Russian transcriptions kept as-is in blockquotes; translations provided separately
- **Scan-first:** Each entry shows original handwritten scans above the transcription
- **Hardcoded special pages:** Timeline, People, Map are in layouts (not content files) because they aggregate data from across the archive and need precise trilingual control
- **No JS frameworks:** Pure CSS + vanilla JS (only Leaflet for the map)

## Common Tasks

### Add a new letter
1. Create `content/letters/YYYY-MM-DD-slug.md` with front matter (title, date_written, weight, scans, summary)
2. Add `## Transcription` with blockquoted original text
3. Add `## Context` with historical notes
4. Create `.ru.md` and `.de.md` translations with matching front matter
5. Ensure scan files exist in `static/scans/`

### Add a new diary entry
Same as letters but in `content/diary/`

### Update timeline/people/map
Edit the corresponding layout file in `themes/raisa/layouts/` — these are hardcoded HTML with trilingual conditionals.

## Historical Context for AI Assistants

When working with this content:
- **Raisa** was 17 when deported (born September 16, 1924, Kyiv — turned 18 in Germany)
- Letters span May 1942 (arrival in Germany) to late 1943; photos extend to September 1944
- She worked on a farm near **Ebrach** (Upper Franconia) then a factory in **Hannover**
- Primary correspondents: sister **Anna**, grandmother **Бабушка**, children **Grishunok** and **Motik**
- The Walter family hosted her: Ludwig, Hedwig, Eduard (soldier), Schön (fond of Raisa)
- **Vladislav Kurbanbaiev** ("Kurban") — Serbian worker, her closest friend
- All mail was censored by Nazi authorities; only postcards allowed (letters returned "Zurück")
- Post-Stalingrad (Feb 1943) marks a tonal shift — masters nervous, workers emboldened
- Raisa's Russian contains Ukrainian elements and increasingly German words over time
