# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

815stats is a single-page data visualization tool for UNHCR refugee statistics focused on Thailand, hosted by the [Center for Asylum Protection](https://capthailand.org/) at 815stats.capthailand.org. The entire application is a **single HTML file** (`website/index.html`) with inlined CSS and JavaScript — no build step, no backend, no package manager.

## No Build/Test/Lint Commands

This project has no npm, Python, Makefile, or CI setup. To preview locally, serve the `website/` directory with any static HTTP server, e.g.:

```bash
python3 -m http.server 8000 --directory website
```

Deployment is via Cloudflare Pages, which auto-deploys on push to the main branch.

## Architecture

The app is a single `website/index.html` (≈1175 lines) with three sections:

- **`<head>`** — CDN imports (PapaParse, Chart.js, Leaflet, js-yaml, Bunny Fonts) + inlined CSS
- **`<body>`** — HTML structure (header, tab nav, 5 tab sections, footer)
- **`<script>`** — All application logic (inlined)

### Global State

```js
cfg = {}        // Parsed config.yaml (branding, labels, data paths)
raw = {}        // Raw CSV data: { poc, applications, decisions, departures, submissions }
F   = { myanmar: 'exclude'|'all'|'only', yearMin, yearMax }  // Active filters
charts = {}     // Chart.js instances keyed by canvas ID
maps = {}       // Leaflet map instances
```

### Initialization Flow

```
init()
  ├─ loadConfig()         → fetch & parse config.yaml
  ├─ applyConfig()        → apply branding (colors, fonts, logo, footer, popup)
  ├─ loadData()           → fetch & parse 5 CSVs in parallel (PapaParse)
  ├─ buildYearSelectors() → populate from/to year dropdowns from data
  └─ renderAll()          → render all 5 tabs
```

### Tab Rendering

Each tab has its own render function that reads from `raw.*`, applies `filter()`, and calls `mkChart()` and table-building helpers:

| Tab | Render Function | Chart Types |
|-----|----------------|-------------|
| Population (POC) | `renderPoc()` + `renderPocTrend()` + `renderPocTable()` | Bar, Line, Table |
| Asylum Applications | `renderApplications()` | Stacked bar, Horizontal bar, Table |
| Asylum Decisions | `renderDecisions()` | Stacked bar, Line (recognition rate), Table |
| Resettlement Departures | `renderDepartures()` | Bar, Sankey, Leaflet map, Table |
| Resettlement Submissions | `renderSubmissions()` | Stacked area, Table |

### Key Functions

- `filter(rows)` — Apply `F.yearMin`, `F.yearMax`, and `F.myanmar` to a dataset
- `mkChart(canvasId, type, config, options)` — Create or update a Chart.js instance; always destroys existing instance first
- `sortTable(data, keyFn, totFn, sortMode)` — Sort rows by name/total/delta
- `toggleSubRows(parentKey)` — Expand/collapse table detail rows
- `dlCSV(elementId, filename)` — Export a rendered HTML table to CSV
- `pctChange(curr, prev)` / `pctClass(val)` — Delta calculation and CSS class for color
- `dispOrigin(country)` / `safeKey(str)` / `n(val)` — Display formatting, DOM-safe key, number parsing

### Chart Color Palette

The 15-color array `CC` is used across all charts for consistent country/series coloring. When adding new chart series, index into `CC` rather than inventing colors.

## Configuration

All branding, labels, data paths, and footnotes are in `website/config.yaml` — no JS edits needed for customization. Key sections: `organisation`, `fonts`, `colours`, `citation`, `labels`, `notes`, `footer`, `popup`, `data`.

## Data Updates

1. Download fresh CSVs from https://www.unhcr.org/refugee-statistics/ (filter: Country of asylum = Thailand)
2. Replace files in `website/data/` keeping the same filenames
3. Commit and push — year range selectors auto-detect years from the data

The five datasets cover: persons of concern, asylum applications, asylum decisions, resettlement departures, and resettlement submissions.

## Conventions

- **One function per rendering concern** — each tab section has dedicated render functions; keep them separate.
- **Re-render on filter change** — `setMyanmar()` and `onYearChange()` call `renderAll()`; render functions must be idempotent (destroy and recreate charts/maps each call).
- **No external state mutations outside render** — DOM and `charts`/`maps` objects are only updated inside render functions or `mkChart`.
- **Myanmar exclusion is the default** — `F.myanmar = 'exclude'` on init; Myanmar is excluded from aggregate charts by default due to its separate national protection regime.
- **Dark mode via CSS variables** — Theme toggling sets `data-theme="dark"` on `<html>`; all colors use `var(--*)` properties. Never hardcode colors in JS; use `getComputedStyle` to read CSS variables when Chart.js needs color values.
