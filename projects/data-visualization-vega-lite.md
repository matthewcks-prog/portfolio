# Sun & Skin — UV vs Melanoma in Australia — Case Study

> **One-liner:** Interactive, data-driven story linking where, when, and how strongly Australians are exposed to UV radiation with melanoma incidence by state.  
> **Status:** Maintained (portfolio) • **Role:** Solo (end‑to‑end: data, design, implementation) • **Timeline:** Oct 2025  
> **Links:**  
> - **Repo:** https://github.com/matthewcks-prog/data-visualization-vega-lite  
> - **Live demo:** https://matthewcks-prog.github.io/data-visualization-vega-lite/  
> - **Docs:** See `README.md` in the repo

---

## Snapshot

**Problem:**  
Australians hear that UV causes melanoma, but rarely see UV exposure and melanoma outcomes in one coherent, interpretable view. It’s hard for non-experts to understand how risk varies by **place** (state), **time** (season), and **outcome** (melanoma rates).

**Solution:**  
A browser‑based dashboard that combines:
- State‑level UV and melanoma **choropleth maps**,
- **Seasonality small multiples** for eight capital cities, and
- A **UV–melanoma scatter plot** with a reference regression line,

all linked through interaction and a dynamic state card to support “overview first, zoom and filter, then details on demand”.

**Impact:**  
- Turns three separate datasets (UV, cancer registry, geography) into a single, consistent narrative about sun exposure.  
- Makes **UV thresholds actionable** (3/6/8/11) by tying them to real cities and months.  
- Surfaces non‑obvious patterns (e.g. Queensland vs Tasmania) and provides a concrete artifact I can show to employers to demonstrate skills in data wrangling, visualization design, and interactive storytelling.

**Tech stack**

- **Frontend:**  
  - HTML5, semantic layout  
  - CSS3 (custom responsive layout, no framework)  
  - Vega-Lite v5, Vega v5, Vega-Embed v6  
  - Vanilla JavaScript for chart orchestration and cross-filtering

- **Backend:**  
  - None (static site served via GitHub Pages)

- **Data/Storage:**  
  - Python (pandas, requests, openpyxl) for ETL (`data_wrangling_script.py`)  
  - CSVs for processed UV and melanoma tables  
  - TopoJSON (Natural Earth Admin-1) for state boundaries

- **Infra/DevOps:**  
  - Git + GitHub for version control  
  - GitHub Pages for static hosting

- **Testing:**  
  - Manual visual and interaction testing across browsers and viewports

---

## What I built

### Core features

- **Linked UV and melanoma choropleth maps** so users can compare spatial patterns and immediately see how exposure and outcomes differ by state.  
- **Seasonality small multiples for eight capitals**, with shared scales and UVI threshold lines, letting users answer “when is it dangerous where I live?”.  
- **Correlation scatterplot (UV vs melanoma)** with point size = case counts and a regression reference line, highlighting outliers while clearly signaling “association, not causation”.  
- **Interactive state card and cross-highlighting**: clicking any state updates a compact card and highlights the same state across views to support comparison without losing context.

### Key workflows

1. **Public user / student exploring risk**
   - Lands on the page → sees two side‑by‑side maps (UV vs melanoma).  
   - Clicks their state → card shows annual mean UVI, peak month, peak UVI, melanoma rate, and total cases.  
   - Scrolls to seasonality charts → reads off when UVI > 3 and how long the “extreme” season lasts in their city.

2. **Health communicator preparing a talk**
   - Uses the maps to screenshot spatial gradients.  
   - Uses the small multiples to show how Darwin vs Hobart differ across the year.  
   - Uses the scatterplot + “Queensland paradox” narrative to explain that behavior and demographics matter beyond just UV.

---

## Architecture

### High-level design

- **Data pipeline (Python / offline):**
  - Downloads ARPANSA UV index data for 8 capitals (2022–2024) via the CKAN API.  
  - Normalizes and aggregates minute data → daily max → monthly mean → state-level metrics (annual mean UVI, peak month, peak UVI).  
  - Processes AIHW Book 7 Excel to extract melanoma incidence (age-standardised rates and counts, 2017–2021) by state.  
  - Joins UV and melanoma into clean, documented CSVs consumed directly by Vega-Lite specs.

- **Visualization layer (Vega-Lite specs):**
  - Separate JSON specs per chart (`map_uv`, `map_melanoma`, `lines_uv_capitals`, `scatter_uv_melanoma`).  
  - Encodings chosen to be consistent (UV = warm sequential; melanoma = cool sequential; same state_code keys across all views).

- **Interaction orchestrator (JS app):**
  - `app.js` embeds all four specs, wires Vega selection signals to a shared `selectedState`, and updates the state card accordingly.  
  - Keeps a small, explicit lookup table for per‑state UV and melanoma metrics to make the card snappy and decoupled from chart internals.

- **Presentation layer (HTML/CSS):**
  - `index.html` provides the narrative structure (hero, What/Why/How, sections for maps, seasonality, scatter, takeaways, and data sources).  
  - `styles.css` handles layout, typography, focus states, and responsive grid behaviour without any CSS framework.

---

## Engineering highlights

### Scalability & performance

- **Approach:**
  - Pre‑aggregated data served as small CSVs → the browser only does lightweight rendering and filtering.  
  - Used TopoJSON for geographic data to minimize payload versus raw GeoJSON.  
  - Limited the number of marks (8 states, 8 cities × 12 months) to stay performant on low‑power devices.

- **Bottlenecks considered:**
  - Many small network calls vs. a few larger ones: consolidated data into a few core CSVs (`uv_state_metric`, `melanoma_rates_state_5yr_mean`, `uv_climatology_selected_years`, `uv_melanoma_scatter`).  
  - Keeping projections and heavy geographic joins in Vega-Lite (which uses Vega optimizations) rather than custom JS loops.

- **Evidence:**
  - Fast initial load on GitHub Pages over a normal connection.  
  - Smooth interaction (no visible lag) even when switching selections repeatedly.

### Reliability & resilience

- **Failure handling:**
  - ETL script uses robust CSV parsing and simple checks (e.g., tolerates header variations in ARPANSA files and ensures key columns exist).  
  - All heavy processing happens offline, so the live app is just reading static files—fewer moving parts to fail.

- **Input validation:**
  - Explicitly filters AIHW data to melanoma of the skin, incidence, persons, and the chosen years, avoiding accidental mixing of series.  
  - Normalizes state codes and names with controlled mappings (`CAP_TO_STATE`, `NAME_TO_CODE`) to keep joins consistent.

- **Edge cases:**
  - UV data: handles missing months/years by focusing on a defined set of years `[2022, 2023, 2024]`.  
  - Melanoma data: ensures both ASR and counts per state before including them in the scatter to avoid misleading partial points.

### Security & privacy

- **Secrets hygiene:**  
  - No API keys or secrets in the repo; ARPANSA and AIHW sources are open data.  
  - The Excel source file is **not** tracked in git; the README documents how to obtain it instead.

- **Data protection:**  
  - All data is aggregated to state level; there is no PII or row‑level patient data.  
  - Clear data source attribution and license links to ARPANSA, AIHW, and Natural Earth.

### Maintainability (long-term)

- **Code structure:**  
  - Clear separation between **ETL (`data_wrangling_script.py`)**, **specs (`docs/specs/*.json`)**, **interaction glue (`docs/js/app.js`)**, and **presentation (`docs/index.html`, `docs/styles.css`)**.  
  - Vega-Lite specs are self‑contained, so individual charts can be evolved without rewriting JS.

- **Documentation:**  
  - Top-level `README.md` explains domain, data, design choices, and file layout.  
  - The Python script begins with a docstring enumerating each output dataset and its schema.

- **Extensibility:**  
  - Easy to add new views (e.g., a time slider or demographic breakdown) by creating new Vega-Lite specs and wiring them through `app.js` with the same `state_code`/`selectedState` convention.

---

## Testing & quality gates

### Test strategy

- **Data sanity checks (manual + pandas):**
  - Checked ranges (e.g., UVI values within realistic bounds, ASR values reasonable by state).  
  - Spot‑checked a sample of ARPANSA and AIHW values against the public dashboards.

- **UI / interaction tests (manual):**
  - Verified consistent behaviour across Chrome, Firefox, and Edge.  
  - Tested selections on each map to ensure the scatter and state card update correctly for every state.  
  - Checked responsive behaviour at mobile, tablet, and desktop breakpoints.

*(There is no automated test suite yet; for this static visualization, I prioritized data correctness, visual clarity, and interaction robustness.)*

---

## Demo

**Live:**  
https://matthewcks-prog.github.io/data-visualization-vega-lite/

**Screenshots:**
- Hero + dual maps (UV vs melanoma).  
- Seasonality grid for all cities with threshold lines.  
- Scatter plot highlighting one specific state (e.g. Queensland).

---

## Trade-offs & decisions

| Decision | Options considered | Why I chose it | What I’d change next |
|---|---|---|---|
| **Vega-Lite + static HTML** vs full JS framework | React, Svelte, D3, raw Canvas/SVG | Vega-Lite lets me express complex visuals declaratively and focus on design and data instead of low-level rendering code. Static hosting is simple and reliable. | If the project grew into a larger app, I’d wrap it in a small framework for state management and routing. |
| **State-level aggregation** vs finer spatial granularity | LGAs, SA2, postcode-level maps | State-level data is consistent across ARPANSA and AIHW and is easier to communicate to general audiences; finer units add complexity and noise. | Add a second view with finer granularity if I can find reliable, matching datasets. |
| **Pre-aggregated CSVs** vs live API queries from the browser | Fetching from ARPANSA/AIHW at runtime | Preprocessing keeps the runtime app snappy and avoids coupling to external API uptime/performance. | Add a “data refresh” script or GitHub Action if I wanted near-real-time updates. |

---

## Challenges & how I solved them

- **Challenge:** ARPANSA UV CSVs had inconsistent headers and formats across cities and years.  
  **Fix:** Wrote a tolerant parser that decodes with fallback encodings and normalizes columns before aggregation.  
  **What I learned:** Real-world “open data” is often messy; robust ETL matters just as much as the visualization itself.

- **Challenge:** Making UV thresholds (3/6/8/11) understandable for non-experts.  
  **Fix:** Encoded them as horizontal reference rules across all small multiples, added textual legends, and synchronized color choices (warm tones for UV) with clear explanatory captions.  

- **Challenge:** Communicating correlation vs causation responsibly.  
  **Fix:** Added a dashed regression line, explicit “context, not causation” copy, and a “Queensland paradox” explanation that calls out behavior and genetics, not just UV levels.

---

## Roadmap

- [ ] Introduce simple UI controls (e.g., toggling between different year ranges or standards for melanoma rates).  
- [ ] Add an accessibility pass (ARIA roles, keyboard navigation for map selections, color‑contrast verification with automated tooling).  
- [ ] Extend the state card with comparative context (e.g., “top 3 of 8 states” for a metric).

---

## How to run (short)

> Full setup instructions live in the project repo `README.md`.

- **Clone:**

  git clone https://github.com/matthewcks-prog/data-visualization-vega-lite.git
  cd data-visualization-vega-lite
  
