# 815stats
 Data visualisation tool for UNHCR statistics for Thailand emphasising the urban refugee population.

This tool is hosted by the [Center for Asylum Protection](https://capthailand.org/) on [815stats.capthailand.org](https://815stats.capthailand.org]).

## Folder structure

```
website/
├── index.html          ← the entire app (single file)
├── config.yaml         ← branding, fonts, colours, labels — edit this
├── assets/
│   └── logo.png        ← place your logo here
└── data/
    ├── persons_of_concern.csv
    ├── asylum_applications.csv
    ├── asylum_decisions.csv
    ├── solutions_departures.csv
    └── solutions_submissions.csv
```

## About the data

The data are from UNHCR's [Refugee Data Finder](https://www.unhcr.org/refugee-statistics/) tool. The data is published by UNHCR under a [Creative Commons Attribution 4.0 International Public License] (CC-BY 4.0)(https://creativecommons.org/licenses/by/4.0/).
The following datasets are visualised:

- **Dateset Group: Forced displacement**

	- *Dataset: Population figures*
		- Display type: Totals
		- Population types: All
		- Year: 1951—2025 | *Note: Data for Thailand begins in 1975.*
		- Country of Origin: All countries
		- Country of Asylum: Thailand
	- *Dataset: Asylum applications*
		- Year: 1951—2025 | *Note: Data for Thailand begins in 2000.*
		- Country of Origin: All countries
		- Country of Asylum: Thailand 
	- *Dataset: Asylum decisions*
		- Year: 1951—2025 | *Note: Data for Thailand begins in 2000.*
		- Country of Origin: All countries
		- Country of Asylum: Thailand

- **Dataset Group: Solutions**
	- *Dataset: UNHCR-assisted refugee resettlement submissions*
		- Display type: Totals
		- Year: 1951—2025 | *Note: Data for Thailand begins in 2003.*
		- Country of Origin: All countries
		- Country of Asylum: Thailand
		- Country of Resettlement: All countries
	- *Dataset: UNHCR-assisted refugee resettlement departures*
		- Year: 1951—2025 | *Note: Data for Thailand begins in 2003.*
		- Country of Origin: All countries
		- Country of Asylum: Thailand
		- Country of Resettlement: All countries

### Updating data (ideally every 6 months)

1. Download fresh CSVs from https://www.unhcr.org/refugee-statistics/
   (filter as above)
2. Replace the files in `data/` with the new exports, keeping the same filenames
3. Commit and push to GitHub — Cloudflare Pages deploys automatically

No code changes needed. The app detects years present in the data automatically.

## Customising branding

Edit `config.yaml`. Every field is commented. Key things to change:
- `organisation.name` — your org name
- `organisation.logo` — path to your logo in assets/
- `colours.primary` and `colours.accent` — brand colours in hex
- `citation.processed_by` — your org name for the footer credit
- `labels.unknown_origin_note` — update after clarifying with UNHCR

## Embedding on WordPress or elsewhere

Add an iframe to any WordPress page:

```html
<iframe
  src="https://815stats.capthailand.org/data-tool/"
  width="100%"
  height="800"
  frameborder="0"
  style="border:none">
</iframe>
```

## Dependencies (all open source, no accounts needed)

- [PapaParse](https://www.papaparse.com/) — CSV parsing
- [Chart.js](https://www.chartjs.org/) — charts
- [Leaflet](https://leafletjs.com/) — maps
- [js-yaml](https://github.com/nodeca/js-yaml) — config file parsing
- [Bunny Fonts](https://bunny.net/fonts) — privacy-respecting font CDN

All loaded from Cloudflare's public CDN. No API keys required.
