# Sun & Skin — UV vs Melanoma in Australia (Interactive Vega-Lite Story)

> **One-liner:** An interactive dashboard that connects Australian UV exposure patterns (where + when) with melanoma incidence by state to make risk differences interpretable for non-experts.  
> **Status:** Maintained (portfolio) • **Role:** Solo (end-to-end: data, design, implementation) • **Timeline:** Oct 2025  
> **Links:**  
> - **Repo:** https://github.com/matthewcks-prog/data-viz-vega-lite  
> - **Live demo:** https://matthewcks-prog.github.io/data-viz-vega-lite/  
> - **Docs:** `README.md` (data pipeline + structure)

---

## Snapshot (what / why / so what)

### Problem
People are told “UV causes melanoma,” but most public explanations fail to show **UV exposure and melanoma outcomes in one coherent view**. It’s hard for non-experts to understand how risk varies by:
- **Place** (state),
- **Time** (season / month),
- **Outcome** (melanoma incidence + counts).

### Solution
A browser-based, interactive story that combines:
- **Dual choropleth maps** (state UV vs state melanoma),
- **Seasonality small multiples** (eight capitals across months, with UV thresholds), and
- A **UV–melanoma scatterplot** (with a regression reference line + outlier emphasis),

all linked through **click-to-filter** and a **dynamic state card** (“overview → zoom/filter → details on demand”).

### What it shows (outcomes)
- Makes UV thresholds **actionable** (3/6/8/11) by tying them to real cities and months.  
- Reveals **spatial contrast** (e.g., Queensland vs Tasmania) and why “high UV ≠ guaranteed highest incidence” (behaviour/demographics).  
- Demonstrates skills in **ETL + visualization design + interaction engineering** in a clean, shippable artifact.

---

## Key questions answered
1. Which states show consistently higher UV exposure (and when does it peak)?
2. How do melanoma incidence rates and counts differ by state?
3. Are UV and melanoma associated at a state level — and which states are outliers?
4. When do capital cities cross “moderate / high / very high / extreme” UV thresholds across the year?

---

## Data (sources + scope)

### UV exposure (ARPANSA)
- Minute-level UV Index data for **8 capital cities** via CKAN API  
- Aggregated to daily max → monthly mean → state metrics  
- Years used: **2022–2024** (consistent availability across cities)

### Melanoma incidence (AIHW Book 7)
- Melanoma of the skin incidence by state  
- Years used: **2017–2021** (to compute a stable multi-year mean)  
- Measures: **age-standardised rate (ASR)** + **case counts**

### Geography
- Natural Earth Admin-1 boundaries (converted to TopoJSON for smaller payloads)

> **Privacy note:** All data is aggregated to state level; no individual-level health data is used.

---

## Data wrangling & transformations (Python ETL)

**Tooling:** Python (`pandas`, `requests`, `openpyxl`) → outputs clean CSVs consumed by Vega-Lite.

### Pipeline steps (high level)
1. **Download UV data** (ARPANSA CKAN) for 8 capitals  
2. **Normalize schema** across inconsistent CSV formats  
3. Aggregate:
   - minute → **daily max UVI**
   - daily → **monthly mean UVI**
   - monthly → **state metrics** (annual mean, peak month, peak UVI)
4. **Extract melanoma tables** from AIHW Book 7 Excel:
   - filter to melanoma of skin, incidence, persons, selected years
   - compute **state 5-year mean ASR** and keep **case counts**
5. **Join datasets** on `state_code` with controlled mappings:
6. Emit reproducible outputs:
   - `uv_state_metric.csv`
   - `melanoma_rates_state_5yr_mean.csv`
   - `uv_climatology_selected_years.csv`
   - `uv_melanoma_scatter.csv`

> **Why pre-aggregate?** It keeps the runtime app fast, stable, and independent from external API uptime.

---

## Dashboard design (why these views)

### 1) Dual choropleth maps (UV vs melanoma)
**Purpose:** immediate spatial comparison.  
**Design rationale:** side-by-side maps reduce mental switching and support quick pattern detection.

### 2) Seasonality small multiples (8 capitals × months)
**Purpose:** answer “when is it dangerous where I live?”  
**Design rationale:** small multiples + shared scales reduce distortion; threshold rules (3/6/8/11) make interpretation concrete.

### 3) Scatterplot (UV vs melanoma)
**Purpose:** show association, highlight outliers, and support discussion of “more than UV matters.”  
**Design rationale:** point size = counts gives context; regression line is a **reference**, not a claim of causality.

### Interaction model
- Click a state in any map → updates:
  - **cross-highlighting across charts**
  - **state card**: annual mean UVI, peak month, peak UVI, melanoma ASR, total cases  
- Built to match the “overview → filter → details” pattern used in professional BI products.

---

## Insights 
- **UV season length differs dramatically by latitude**: northern capitals show longer high-UV periods than southern capitals.  
- **Melanoma incidence varies by state**, and not always proportionally to UV alone, implying behavioural/demographic factors also matter.  
- The scatterplot shows **clear clustering + outliers**, helping explain “association, not causation” responsibly.  
- Threshold lines make it obvious which cities exceed **UVI ≥ 3** for large portions of the year (practical “sun protection season”).


---

## Limitations / responsible interpretation
- State-level aggregation can hide within-state variability (coastal vs inland, urban vs regional).  
- UV years (2022–2024) and melanoma years (2017–2021) are not the same period; this supports **general comparison**, not time-matched inference.  

---

## Next steps
- Create more complex charts and add more interactivity
- Add **comparative context** in the state card (“rank among 8 states”, percentile)  

---

## Demo
- **Live:** https://matthewcks-prog.github.io/data-viz-vega-lite/  
- **Repo:** https://github.com/matthewcks-prog/data-viz-vega-lite  

---

## How to run (short)
```bash
git clone https://github.com/matthewcks-prog/data-viz-vega-lite.git
cd data-viz-vega-lite
# Follow README for ETL + local serving instructions
