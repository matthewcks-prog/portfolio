# DisasterSight — Case Study

> **One-liner:** Local-first Streamlit dashboard for AI-assisted satellite damage triage using xBD/xView2 pre-disaster and post-disaster imagery.  
> **Role:** Solo developer  
> **Focus:** Python, computer vision, geospatial data processing, Streamlit dashboards, ML evaluation, responsible AI  
> **Links:** [Project repository](https://github.com/matthewchungkaishing/DisasterSight)

---

## Overview

DisasterSight is an academic decision-support prototype for reviewing building-level damage after disasters. It uses xBD/xView2 pre-disaster and post-disaster satellite imagery, xBD building polygons, and a paired-crop damage classifier to surface scene summaries, severity counts, confidence scores, review flags, overlays, and evaluation metrics in a Streamlit dashboard.

The project is intentionally scoped as a local-first MVP. It does not claim to be an operational emergency-response system, and it keeps human review central to the workflow.

---

## Problem

Post-disaster imagery can contain many buildings across multiple scenes. A useful triage tool needs to organise pre/post imagery, building geometries, predicted damage labels, confidence values, review flags, and evaluation outputs in a way that is fast to inspect and easy to audit.

The project focuses on a reliable baseline first:

- use xBD/xView2 as the primary dataset
- use xBD-provided building polygons for localisation
- extract paired pre/post building crops
- train and evaluate a simple paired-crop classifier
- cache predictions for dashboard use
- present outputs as decision support for human review

---

## Tech stack

| Area | Tools |
|---|---|
| App | Streamlit |
| Language | Python |
| Data | xBD/xView2, pandas, NumPy, GeoPandas, Shapely, Rasterio |
| Vision/ML | OpenCV, Pillow, PyTorch, TorchVision, scikit-learn |
| Visualisation | Matplotlib, Streamlit components, image overlays |
| Quality | unittest, Ruff, mypy, pre-commit, compile checks |

---

## Architecture

```text
src/
├── common/       # constants and path helpers
├── data/         # xBD parsing, scene manifests, crop extraction
├── models/       # training and evaluation scripts
├── inference/    # cached dashboard-ready predictions
└── dashboard/    # Streamlit app, artifact resolver, pages, overlays, UI components
```

Key design choice: the dashboard reads generated artifacts instead of running live model inference. This keeps the demo responsive, reproducible, and usable on CPU-only machines.

---

## Pipeline

1. Discover xBD/xView2 scenes and pair pre/post disaster imagery.
2. Parse xBD building polygons, bounding boxes, and damage labels.
3. Build event-aware train/validation/test scene manifests.
4. Extract paired building crops from pre/post imagery.
5. Train and evaluate a baseline paired-crop classifier.
6. Generate cached building predictions and scene summaries.
7. Load artifacts into a Streamlit dashboard for review.

---

## Dashboard features

The Streamlit app includes:

- Dashboard, Map Explorer, and Analytics pages
- scene-level severity counts and priority score
- building-level labels, confidence, class probabilities, and review flags
- pre/post image overlays using building geometry
- evaluation metrics and confusion matrix artifacts
- fixture fallback for offline development when generated artifacts are unavailable

---

## Quality and engineering details

- Config-driven paths through `config.yaml`
- Event-aware manifests to avoid splitting disaster events incorrectly
- Crop manifest validation and QA contact-sheet generation
- Pure artifact-resolver layer decoupled from Streamlit for testability
- Focused unit tests for parsing, manifests, crop extraction, inference, dashboard labels, overlays, priority scoring, and artifact resolution
- Ruff, unittest discovery, and compile checks documented as quality gates
- Interface contracts for scene records, building records, model inputs, predictions, summaries, dashboard artifacts, and responsible-AI messages

---

## Responsible-AI scope

DisasterSight is designed as an academic prototype and decision-support tool. The project explicitly avoids claims about autonomous emergency dispatch, live satellite ingestion, route optimisation, or production emergency-response use. Low-confidence results are flagged for human review.

---

## Current access status

No public hosted dashboard URL is documented in the repository. The dashboard is accessible locally with:

```powershell
streamlit run src/dashboard/app.py
```

The README also keeps cloud deployment out of scope for the current MVP, so I am not adding a fake live-site link.

---

## Skills demonstrated

- Python software engineering
- Streamlit dashboard development
- Computer vision and image preprocessing
- Satellite imagery and geospatial data handling
- xBD/xView2 dataset processing
- PyTorch/TorchVision model training and evaluation
- pandas/NumPy data pipelines
- GeoPandas/Shapely/Rasterio geospatial tooling
- Unit testing, static checks, and interface contracts
- Responsible-AI framing and human-review workflows
