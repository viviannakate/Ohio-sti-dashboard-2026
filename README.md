# Ohio STI Surveillance Dashboard (2020–2024)

An interactive, **fully static** dashboard for sexually transmitted infection (STI)
surveillance across Ohio — county-level incidence rates, major-city case loads, longitudinal
trends, and population stratification by sex and race. It runs entirely in the browser with no
server or Python kernel, so it can be hosted directly on **GitHub Pages**.

This replaces the `ipywidgets` version in `STI_analysis.ipynb`, which cannot run on GitHub
Pages because `ipywidgets` requires a live Python kernel.

Link to web app: https://viviannakate.github.io/Ohio-sti-dashboard-2026/

---

## What's in this repository

| File | Purpose |
|------|---------|
| `index.html` | The complete dashboard (HTML + CSS + JavaScript, Plotly.js) |
| `ohio-counties.geojson` | Bundled boundaries for all 88 Ohio counties (U.S. Census TIGER) |
| `topojson/` | Bundled base map so the maps render with **no external runtime fetch** |
| `sample_data/` | Placeholder CSVs so the page works out of the box for a demo |

This repository **already contains the real 2020–2024 surveillance data** at the root
(`Ohio_Merged_STI_Demographics_2020_2024.csv` and `Ohio_City_STIs_Clean.csv`), so it is ready to
publish as-is. The `sample_data/` folder is only a fallback used if the root files are ever
removed.

The dashboard reads two data files. It looks for them at the **repository root first**, and
falls back to `sample_data/` only if they are missing (showing a yellow "sample data" banner so
placeholder numbers are never mistaken for real ones):

- `Ohio_Merged_STI_Demographics_2020_2024.csv` — produced by cell 1 of the notebook
- `Ohio_City_STIs_Clean.csv` — the long-format city file (cell 2 of the notebook reshapes the
  raw `City_rates.xlsx` / `City_rates.csv` into this layout)

---

## Deploy to GitHub Pages (about 3 minutes)

1. **Create a repository** on GitHub (for example `ohio-sti-dashboard`).
2. **Add these files** to the repository root: `index.html`, `ohio-counties.geojson`, the
   `topojson/` folder, and — once ready — your two real CSVs. Commit and push.
3. **Turn on Pages:** repository **Settings → Pages → Build and deployment**, set
   **Source = Deploy from a branch**, **Branch = `main`**, **Folder = `/ (root)`**, then **Save**.
4. Wait about a minute, then open the published URL
   (`https://<your-username>.github.io/<repo-name>/`).

> The page must be opened over **http(s)** (GitHub Pages or a local server) — not by
> double-clicking the file. Browsers block data loading from `file://` URLs.

### Preview locally first
```bash
cd <this-folder>
python -m http.server 8000
# open http://localhost:8000
```

---

## Swapping in the real data

The repository ships with **sample numbers** so the dashboard renders immediately. To publish
real surveillance figures:

1. Run cells 1 and 2 of `STI_analysis.ipynb` to regenerate the two cleaned CSVs.
2. Copy both files to the **repository root** (next to `index.html`), keeping the exact
   filenames above.
3. Commit and push. The yellow banner disappears automatically once real files are present.

No code changes are needed. The dashboard reads column names, diseases, years, counties, and
cities directly from the CSVs, so it adapts to the real data on its own.

### Expected columns

`Ohio_Merged_STI_Demographics_2020_2024.csv`
```
County, STI_Disease, Year, Cases, Rate, TOT_POP, TOT_MALE, TOT_FEMALE,
WA_MALE, WA_FEMALE, BA_MALE, BA_FEMALE, H_MALE, H_FEMALE
```
Any extra numeric population columns are picked up automatically as additional demographic
strata. The `County` values must match standard Ohio county names (e.g. `Hamilton`, not
`Hamilton County`) so they join to the map.

`Ohio_City_STIs_Clean.csv`
```
City, STI_Disease, Year, Cases
```
City coordinates for the bubble map are defined in `index.html` (`CITY_COORDS`). Add an entry
there if a new city appears in the data.

---

## Features

- **Scale toggle** — county choropleth (rate per 100k) or major-city bubble map (case counts).
- **Disease, year, and location selectors** that update every panel together.
- **KPI strip** — statewide/total cases with year-over-year change, median rate or top city,
  and the highest-burden location.
- **Longitudinal trend** — dual-axis cases and rate for the selected county, single-axis cases
  for cities.
- **Demographic strata** — grouped population bars for any combination of sex/race subgroups
  (county scale).
- Responsive down to mobile; keyboard-focusable controls.

---

## Notes

- The county file may include an **`Unknown County`** row (cases reported without a county
  assignment). Those cases **are included in the statewide totals** but are excluded from the
  county map, the county-profile selector, and the "across N counties" count, since they cannot
  be mapped and carry no rate or demographics.
- Plotly.js and PapaParse load from their public CDNs; the base map topology is bundled locally
  so the maps do not depend on `cdn.plot.ly` at runtime.
- Disease labels follow the spelling used in the source data (e.g. `Chlaydia`, `Syphillis`). To
  correct these, fix them in the source CSVs — the dashboard displays whatever the data contains.
