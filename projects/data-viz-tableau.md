# Groceries up, kitchens adjust — Prices vs searches in Australia (2019–2025)  
**Case study (Tableau Public)**

> **One-liner:** Compare Australia’s official food price movements (ABS CPI) with Google search interest that proxies “budget / home-cooking” behaviour to see if timing patterns emerge.  
> **Domain:** Cost of living & daily food choices • **Audience:** General Australian readers • **Role:** Solo • **Timeline:** 2019–Jul 2025 (data range)  
> **Links:**  
> - **Tableau Public dashboard:** https://public.tableau.com/app/profile/matthew.chung.kai.shing/viz/DataVisualisation1_17570904026250/Datavisualization1dashboard?publish=yes  

---

## Snapshot
**Problem / question**  
Food prices have risen since 2019 (including the COVID period), but it’s not obvious whether Australians responded by seeking more budget-friendly, home-cooking options.  
This project asks:

1. Which grocery-related price categories rose the most over 2019–2025?
2. Do “budget/home-cooking” searches rise around the same time as price changes?
3. Are there seasonal patterns in CPI YoY changes and search interest?
4. Does behaviour (search interest) lead or lag price changes by a few months?

**Solution**  
A single-scroll Tableau dashboard that pairs CPI series with Google Trends series, plus heatmaps and a lollipop summary, with interactive filtering and a lead/lag slider.

**Impact (what this demonstrates)**  
- Ability to combine official macroeconomic indicators with behavioural proxy data  
- Clear narrative + accessible design + interaction for exploration  
- Thoughtful visual encoding choices (ink ratio, avoiding chart junk, baseline normalisation, smoothing)

---

## Dataset
### Source 1 — ABS CPI (official)
**Australian Bureau of Statistics (ABS): Monthly CPI Indicator**  
- Table: Monthly CPI Indicator — **Table 1** (Australia)  
- Content used: selected CPI groups / expenditure classes (monthly index; YoY % change available)  
- Why it matters: CPI is Australia’s official measure of consumer price change.

Source: https://www.abs.gov.au/statistics/economy/price-indexes-and-inflation/monthly-consumer-price-index-indicator/latest-release

### Source 2 — Google Trends (behaviour proxy)
**Google Trends (Australia), monthly “interest over time” (0–100)**  
Search terms used to approximate budget/home-cooking behaviour:  
- “meal prep”  
- “cheap recipes”  
- “air fryer recipes”  
- “bulk buy”  
- “one-pot”

Source: https://trends.google.com/trends/explore

**Why these sources together**  
ABS CPI tells *what happened to prices*; Google Trends approximates *public interest and intent* in coping strategies (e.g., cheaper meals, batch cooking). Both are accessible and interpretable for a broad audience.

---

## Cleaning + transformations (Excel / Tableau Prep / SQL equivalent)
> Tooling used: **Excel** for initial cleanup + **Tableau** for modelling and calculations (joins + calculated fields).  
> *(If you used Tableau Prep, keep this section but swap in the exact steps.)*

### 1) ABS CPI cleanup (Excel)
- Downloaded monthly CPI subgroup data (national).  
- Removed unnecessary rows/columns.  
- Filtered to **Jan 2019 → Jul 2025**.  
- Standardised the time field to a consistent **Month** format for joining.

### 2) Google Trends extraction (Google Trends → Excel)
- Exported monthly “interest over time” for Australia for each term.  
- Added trends data into a **new sheet** in the same workbook as CPI.  
- Standardised the **Month** field to match CPI.

### 3) Data modelling (Tableau)
- **Inner join** CPI and Trends on **Month** (both sheets) to align time series.  
- Added calculations to support fair comparison:

**Key calculations**
- **CPI baseline normalisation (Jan 2019 = 100):**  
  - Purpose: compare shapes/rates across categories on a consistent starting point.  
- **3-month moving average for Trends:**  
  - Purpose: reduce volatility/spikes and make month-to-month comparisons more stable.  
- **Lead/Lag offset (parameter/slider):**  
  - Purpose: explore delayed behavioural response (search interest may shift after price changes).

> Note: Google Trends is an index (0–100) rather than absolute volume; CPI is an index level (and YoY % change). This dashboard focuses on *patterns and timing*, not causal measurement.

---

## Dashboard design choices (what + why)
### Layout & story structure (single scroll)
A “top-to-bottom” narrative for non-technical readers:
1. **Overview** of multiple CPI categories (2019–2025)  
2. **Pairwise panels** comparing a CPI category vs a search term  
3. **Seasonality heatmaps** to reveal recurring monthly patterns  
4. **Lollipop summary** to show “change since last July” with low ink

This supports both **guided reading** and **self-directed exploration**.

### Chart choices
**1) Multi-series line overview (CPI 2019–2025)**  
- Why: time series is the natural form for monthly data.  
- Line chart preserves continuity and makes trend direction obvious.

**2) Side-by-side paired line panels (CPI vs Trends)**  
- Why: compare a price category and a behavioural term **without dual axes** (dual axes often mislead).  
- Uses **small multiples** so each pair remains readable.

**3) Heatmaps (CPI YoY change & Search interest)**  
- CPI YoY heatmap: **diverging palette, centered at 0** to show above/below baseline inflation pressures.  
- Search heatmap: **sequential palette (0–100)** to show relative intensity.  
- Why: quickly surface seasonal patterns and repeated “hot/cold” periods.

**4) Lollipop chart (change from last July to latest)**  
- Why: “one value per category” summary with less ink than bars; supports quick ranking.

### Interaction (filters + controls)
- **Category/term selection** to let readers focus on relevant items.  
- **Lead/Lag slider** (months) to explore whether searches lead/lag CPI changes.  
- Aimed at answering “Does behaviour react immediately, or with delay?”

### Visual design & accessibility
- Built as a **single-scroll webpage** for easy reading.  
- **Colour-blind-safe palette** to ensure interpretability.  
- Prioritised visual hierarchy (titles, spacing, consistent annotation) to reduce cognitive load.

---

## Insights found (2–5)
- **Prices rose across multiple food categories over 2019–2025**, with some categories showing stronger upward movement than others (visible in the CPI overview).  
- **Search interest in budget/home-cooking proxies changes over time**, and smoothing (3-month average) helps reveal underlying shifts rather than noise.  
- **Lead/lag exploration suggests timing matters**—behavioural signals may not move in the same month as price changes, and can appear a few months before/after.  
- **Seasonal patterns are easier to detect via heatmaps** than lines alone, especially for recurring monthly peaks/troughs.  
- **“Change since last July” provides a digestible summary** that complements the richer time-series story.

*(If you want this section to be sharper for employers: replace the above with 3–5 concrete, quantified statements once you’ve validated which categories/terms show the clearest patterns.)*

---

## Limitations / next steps
### Limitations
- **Correlation ≠ causation:** this dashboard shows co-movement and timing patterns, not causal proof.  
- **Google Trends is indexed (0–100):** reflects relative interest, not absolute search volume; comparisons across terms can be imperfect.  
- **Proxy terms may be noisy:** “air fryer recipes” can trend for reasons beyond cost-of-living (e.g., product popularity).  
- **Join granularity:** monthly alignment may hide weekly spikes or short-lived events.

### Next steps 
- **Introduce a simple correlation/lag analysis table** (e.g., best lag month per term/category) as a companion panel.  
- **Expand behavioural signals:** include supermarket catalogue searches, “discount grocery”, or “budget meals” variants; test robustness.  
- **Segment analysis (if data available):** compare states/regions or metro vs non-metro using location filters.
- **Overall introduction and summary missing.** Could have added annotations

---

## What I learned (employer-relevant takeaways)
- How to **combine official economic indicators with behavioural proxy data** to tell a consumer-facing story.  
- How to design an accessible, single-scroll dashboard with clear **layout, colour, figure ground, hierarchy, typography and interaction** that serves non-technical audiences.
- What and how to chose **Visualisations idioms and complexity**
- How **normalisation + smoothing** can make comparisons fairer and patterns clearer.  


---

## Repro notes
**Data range:** Jan 2019 – Jul 2025  
**Join key:** Month (standardised format across sources)  
**Key transforms:** CPI baseline to 100 at Jan 2019; Trends 3-month moving average; lead/lag parameter  
**Refresh plan:** *(add if you plan to update monthly)*

---

## Credits / references
- Australian Bureau of Statistics — Monthly CPI Indicator (Australia)  
- Google Trends — Australia “interest over time”
