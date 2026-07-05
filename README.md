# Portfolio

I build pragmatic, production-minded software: cleanly architected, well-tested, and shaped around real user needs. This page highlights projects that show how I think about architecture, reliability, data, user experience, and developer workflow.

## Engineering focus

- **Software architecture:** layered design, domain modelling, SOLID principles, design patterns, ADRs
- **Full-stack development:** TypeScript, React, Node.js, Express, Django, PostgreSQL, Supabase
- **Quality and reliability:** unit tests, CI, validation, error handling, security-minded defaults
- **Systems and data:** networking labs, Docker, cloud deployment, ETL, visual analytics

## Featured projects

### 1) Lock-in

**What:** In-page study assistant Chrome extension + backend. Keeps notes, lecture transcripts, AI help, tasks, and revision tools in one place—on the page you're viewing. Consent-first: only uses content you explicitly select; LMS credentials never leave the browser.

**Tech:** TypeScript, React, Lexical, Node.js, Express, Supabase (Postgres + Storage + Auth), pgvector, Docker, Azure Container Apps, GitHub Actions (OIDC), Trivy, Sentry.

**Highlights:**
- Layered architecture: Routes → Controllers → Services → Repositories, with no cross-layer imports enforced by dependency-cruiser
- Code quality guardrails: file/function size limits, cyclomatic complexity checks, Zod validation at boundaries
- Reliability patterns: unit tests, idempotency, timeouts, graceful fallbacks, request guards for streaming responses
- Security and privacy: backend-only secrets, Supabase RLS, transcript URL redaction, 90-day TTL, Trivy checks for high-severity issues
- CI quality gates: format, lint, typecheck, tests, coverage, build, and security scan

**Links:** [Repo](https://github.com/matthewcks-prog/Lock-in) · [Demo](https://drive.google.com/file/d/1BxJfHWy5cRs7nm8L6mrWar0dgdRflV-M/view?usp=sharing) · [Case study](projects/lock-in.md)

---

### 2) TTR London — Java OOP architecture project

**What:** Java Swing desktop board-game implementation designed around clean object-oriented architecture. Game rules live outside Swing, player actions enter through application commands, and UI panels render immutable snapshots.

**Tech:** Java 25, Swing, Maven, JUnit, Checkstyle, Javadoc.

**Highlights:**
- Four-layer architecture: `domain`, `application`, `infrastructure`, `ui`
- Rich domain model for routes, players, cards, turns, scoring, and game phases
- Design patterns used for real architectural problems: Command, Observer, Strategy, Factory
- Testable core rules with deterministic randomness through injected `ShuffleStrategy`
- Architecture boundary tests preventing Swing/AWT imports in non-UI layers
- 106 automated tests, Maven `verify`, Google Java Style checks, Javadoc generation, ADRs

**Links:** [Repo](https://github.com/matthewcks-prog/Ticket-to-Ride-Game) · [Case study](projects/ticket-to-ride-game.md)

---

### 3) mini-fabric-lab

**What:** Fully containerized, reproducible spine-leaf network fabric lab built with Containerlab and FRRouting. Models an OSPF underlay, iBGP overlay with route reflectors, and an eBGP edge router with policy control and automated verification.

**Tech:** Containerlab, FRRouting, OSPF, iBGP, eBGP, Python, Docker, Make.

**Highlights:**
- Topology-as-code with a single declarative lab definition
- Configs-as-code with version-controlled FRR configs
- OSPF underlay for loopback reachability and stable BGP peering
- iBGP overlay using route reflectors for scalable session design
- eBGP edge peering with default-route injection and prefix filtering
- Python healthcheck validates containers, adjacencies, BGP sessions, routing entries, and end-to-end connectivity
- Failure testing showing expected behaviour during link loss and recovery

**Why it matters:** Demonstrates systems thinking beyond application code: protocol behaviour, routing design, policy control, reproducibility, automated verification, and troubleshooting discipline.

**Links:** [Repo](https://github.com/matthewcks-prog/mini-fabric-lab) · [Case study](projects/mini-fabric-lab.md)

---

### 4) CodeVenture — LMS platform

**What:** Python learning platform with interactive lessons, auto-graded quizzes/challenges, a browser-based Python playground, and progress tracking for students and educators. Built in my first year of engineering and expanded as I learned more about Django, testing, and deployment.

**Tech:** Django 4.2, PostgreSQL, SQLite locally, HTML/CSS/JavaScript, Google OAuth via `django-allauth`, GitHub Actions, Render, Figma.

**Highlights:**
- Django monolith split into apps for learning modules, assessments, playground, and authentication
- Environment-based production setup with `DATABASE_URL`, `.env` configuration, WhiteNoise static assets, and Render deployment
- Google OAuth setup with callback handling and secure session configuration
- CI checks with `pytest`, `pytest-django`, and `manage.py check --deploy`
- Monaco-based Python playground so students can run code in the browser without local setup
- UX/UI designed in Figma before implementation
- **Recognition:** Monash commendation for FIT1056 coursework

**Links:** [Repo](https://github.com/matthewcks-prog/CodeVenture) · [Live](https://codeventure-ez4m.onrender.com) · [Feature overview PDF](https://github.com/matthewcks-prog/CodeVenture/blob/main/Main%20features%20implemented%20for%20Code%20Venture.pdf) · [Case study](projects/codeventure.md)

---

### 5) NotMoodle — team LMS

**What:** Lightweight Learning Management System built by a 7-person team for Monash FIT2101. Supports multi-role access, course/lesson management, classroom tracking, and an AI assistant grounded in course content through RAG.

**Tech:** Django 5.x, PostgreSQL + pgvector, Ollama (`llama3.1`, `nomic-embed-text`), pytest, GitLab CI.

**Highlights:**
- Scrum-inspired workflow with Jira task/time tracking, sprint planning, retrospectives, and Definition of Done
- My role: Scrum Master, integrator, AI assistant design/implementation, test infrastructure, Git workflow, merge requests, and code reviews
- RAG pipeline work: chunking, embeddings, vector similarity, and rate-limited chat API
- Team delivery of a functional web app with testing and CI practices

**Links:** [Repo](https://github.com/matthewcks-prog/NotMoodle) · [Case study](projects/notmoodle.md) · [Demo](https://drive.google.com/file/d/1IEYiaHdESkzElaRnT_lVPxgtUEioPg0w/view?usp=sharing) · [Retrospective report](https://github.com/matthewcks-prog/NotMoodle/blob/main/Retrospective_report.pdf)

---

### 6) Data visualisation projects

**What:** Two storytelling dashboards:
- **Sun & Skin — UV vs Melanoma in Australia** using Vega-Lite
- **Groceries up, kitchens adjust — Prices vs searches in Australia** using Tableau Public

**Tech:** Vega-Lite, Vega, Vega-Embed, HTML/CSS/JavaScript, Python (`pandas`, `requests`, `openpyxl`), TopoJSON/GeoJSON, Tableau Public, Excel.

**Highlights:**
- Interactive dashboards with cross-filtering, state/city drill-downs, and overview → filter → details flows
- Deliberate visual encodings: small multiples, dual choropleths, scatterplots, regression reference, heatmaps, lollipop charts
- Reproducible ETL and pre-aggregation to keep front-end experiences fast and API-independent
- Responsible communication: clear annotation of limitations, association vs causation, and uncertainty in interpretation

**Links:** [Vega-Lite repo](https://github.com/matthewcks-prog/data-viz-vega-lite) · [Vega-Lite live demo](https://matthewcks-prog.github.io/data-viz-vega-lite/) · [Vega-Lite case study](projects/data-viz-vega-lite.md) · [Tableau dashboard](https://public.tableau.com/app/profile/matthew.chung.kai.shing/viz/DataVisualisation1_17570904026250/Datavisualization1dashboard?publish=yes) · [Tableau case study](projects/data-viz-tableau.md)

## Coursework highlights

- **Software Design / OOP:** SOLID principles, layered architecture, UML, design patterns, refactoring, architecture documentation
- **Testing & Quality:** unit/integration testing, regression prevention, CI checks, maintainability practices
- **Databases & SQL:** schema design, normalisation, indexing, joins/aggregations, transactions, PostgreSQL
- **Cloud / Big Data:** Azure fundamentals, storage, deployment workflows, Python/SQL data processing

## Notes

I keep this portfolio focused on polished, maintained work. For private university or group projects, I can share demos and details where appropriate.
