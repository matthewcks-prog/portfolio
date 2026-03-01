# Portfolio
I build pragmatic, production-minded software: cleanly architected, well-tested, and shaped around real user needs. This page highlights a few projects that show how I think about reliability, data, and developer experience end to end.
## Featured projects

### 1) Lock-in (Flagship)
**What:** In-page study assistant Chrome extension + backend. Keeps notes, lecture transcripts, AI help, tasks, and revision tools in one place—on the page you're viewing. Consent-first: only uses content you explicitly select; LMS credentials never leave the browser.

**Tech:** TypeScript, React, Lexical, Node.js, Express, Supabase (Postgres + Storage + Auth), pgvector, Docker, Azure Container Apps, GitHub Actions (OIDC), Trivy, Sentry.

**Highlights:**
- Layered architecture (Routes → Controllers → Services → Repos; no cross-layer imports, enforced by dependency-cruiser)
- Code quality guardrails (file/function size limits, cyclomatic complexity, Zod validation at boundaries)
- Reliability (unit tests, idempotency, timeouts, graceful fallbacks)
- Security & privacy (secrets only on backend, RLS on all tables, transcript URL redaction, 90-day TTL)
- CI quality gates (format, lint, typecheck, tests, coverage, build, Trivy on CRITICAL/HIGH)

**Links:**
- **Repo**: https://github.com/matthewcks-prog/Lock-in
- **Demo**: [demo_video_lock-in.mp4](https://drive.google.com/file/d/1BxJfHWy5cRs7nm8L6mrWar0dgdRflV-M/view?usp=sharing)
- **Case study**: [Lock-in.md](https://github.com/matthewcks-prog/portfolio/blob/main/projects/lock-in.md)

---

### 2) Codeventure (LMS Platform)
**What:** Python learning platform with interactive lessons, auto‑graded quizzes/challenges, a browser‑based Python playground, and progress tracking for students and educators.Done in my 1st year of engineering and expanded as I learned more about Django, testing, and deployment.

**Tech:** 
- Django 4.2 monolith with separate apps for learning modules, assessments, playground, and auth
- Django templates with responsive HTML/CSS (Bootstrap‑style layout) and vanilla JS for interactive behaviour
- PostgreSQL on Render in production, SQLite locally (`DATABASE_URL`, `.env`‑driven settings)
- Google OAuth with `django-allauth` (OAuth client setup, callback handling, and secure session management)
- CI with GitHub Actions (`pytest`, `pytest-django`, `manage.py check --deploy`)
- UX/UI designed in Figma (wireframes and flows, see `assets/design` in the repo)

**Highlights:**
- Structured curriculum (learning videos from youtube) with quizzes and coding challenges, all auto‑graded with instant feedback
- Monaco‑based Python playground so students can run code safely in the browser without local setup
- Production‑ready setup: environment‑based config, static assets with WhiteNoise, and deployment to Render
- **Recognition**: Received a commendation letter from Monash University for this LMS project in the FIT1056 unit [View](https://drive.google.com/file/d/1krWQwxm0iuOw_qYn4SLptuMpsn16l7DQ/view?usp=sharing)

**Impact / what I learned:**
- Deepened understanding of Django app structure, "models" for data acess layer, "views"for logic layer and "templates" for presentation layer
- Learned how to design and iterate on UI flows in Figma before coding
- Got hands‑on experience integrating third‑party OAuth and running tests in CI for every push

**Links**:
- **Repo**: [github.com/matthewcks-prog/CodeVenture](https://github.com/matthewcks-prog/CodeVenture)
- **Live**: https://codeventure-ez4m.onrender.com
- **Feature overview (PDF)**: [Main features implemented for CodeVenture](https://github.com/matthewcks-prog/CodeVenture/blob/main/Main%20features%20implemented%20for%20Code%20Venture.pdf)
- **Case study**: [codeventure.md](https://github.com/matthewcks-prog/portfolio/blob/main/projects/codeventure.md)

---
### 3) NotMoodle (FIT2101 — Team LMS)
**What:** Lightweight Learning Management System built by a 7-person team for Monash FIT2101 (Software Engineering Process and Management). Multi-role access, course/lesson management, classroom tracking, and an AI assistant grounded in course content via RAG (Ollama + pgvector).

**Tech:** Django 5.x, PostgreSQL + pgvector, Ollama (llama3.1, nomic-embed-text), pytest, GitLab CI

**Highlights:**
- **Process-first:** Scrum-inspired workflow, Jira task/time tracking, sprint planning, retrospectives, Definition of Done
- **My role:** Scrum Master, integrator, AI assistant design/implementation, test infrastructure, RAG pipeline (chunking, embeddings, vector similarity), rate-limited chat API, Git feature-branch workflow, merge requests, code reviews, CI quality gates
- Delivering a *fully functional web app while working alongside a team 

**Links**:
- **Repo**: [github.com/matthewcks-prog/NotMoodle](https://github.com/matthewcks-prog/NotMoodle)
- **Case study**: [projects/notMoodle.md](https://github.com/matthewcks-prog/portfolio/projects/notmoodle.md)
- **Demo**: [demo_NotMoodle.mp4](https://drive.google.com/file/d/1IEYiaHdESkzElaRnT_lVPxgtUEioPg0w/view?usp=sharing) 
- **Retrospective report**: [Retrospective_report.pdf](https://github.com/matthewcks-prog/NotMoodle/blob/main/Retrospective_report.pdf)


---
### 4) Data visualisation projects

**What**: Two storytelling dashboards:  
- **Sun & Skin — UV vs Melanoma in Australia** (Vega-Lite, browser-based)  
- **Groceries up, kitchens adjust — Prices vs searches in Australia** (Tableau Public)

**Tech**:
- Vega-Lite, Vega, Vega-Embed (JSON specs + JS) for fully client-side interactive visuals  
- HTML/CSS + lightweight JS for layout and embedding  
- Python (pandas, requests, openpyxl) for ETL and pre-aggregation of UV/melanoma datasets  
- TopoJSON/GeoJSON for geographic boundaries and choropleth maps  
- Tableau Public for multi-view dashboards, parameters, calculated fields, and custom tooltips  
- Excel / Tableau data modelling for joins, baseline normalisation, moving averages, and lead/lag parameters  

**Highlights**:
- Built linked, interactive dashboards with cross-filtering, state/city drill-downs, and “overview → filter → details” flows  
- Designed visual encodings deliberately: small multiples, dual choropleths, scatterplots with regression reference, heatmaps, lollipop charts  
- Implemented reproducible ETL pipelines and pre-aggregation to keep front-end experiences fast and API-independent  
- Focused on responsible communication: clear annotation of limitations, association vs causation, and uncertainty in interpretation  

**Links**:
- **Vega-Lite repo**: <https://github.com/matthewcks-prog/data-viz-vega-lite>  
- **Vega-Lite live demo**: <https://matthewcks-prog.github.io/data-viz-vega-lite/>
- **Case study**: [projects/data-viz-vega-lite.md](https://github.com/matthewcks-prog/portfolio/blob/main/projects/data-viz-vega-lite.md)
- **Tableau dashboard**: <https://public.tableau.com/app/profile/matthew.chung.kai.shing/viz/DataVisualisation1_17570904026250/Datavisualization1dashboard?publish=yes>
- **Case study**: [projects/data-viz-vega-lite.md](https://github.com/matthewcks-prog/portfolio/blob/main/projects/data-viz-vega-lite.md)


## Coursework highlights (selected)
Artifact: https://github.com/matthewcks-prog/Lock-in
- **Testing & Quality** – property-based testing, test design, and CI  
  - Focus: unit/integration testing, coverage, regression prevention  
  - Tech: Python/`pytest`, GitHub Actions  

- **Databases & SQL** – schema design and query optimisation  
  - Focus: normalisation, indexing, joins/aggregations, transactions  
  - Tech: PostgreSQL, SQL, ER modelling  

- **Cloud / Big Data** – deploying and scaling data-heavy workloads  
  - Focus: cloud architecture, storage, basic distributed processing  
  - Tech: Azure (App Service / Storage / Functions), Python, SQL  

- **Software Design / OOP** – clean architecture and patterns  
  - Focus: SOLID principles, layering, dependency management  
  - Tech: Java or Python (OOP), UML, design patterns  

## Notes
- I keep this portfolio focused: only polished, maintained work is featured.
- For private uni/group work, I can share demos and details on request.
