# Portfolio — Matthew Chung Kai Shing

I’m a Software Engineering student at Monash University in Melbourne. I build pragmatic, production-minded software: cleanly architected, well-tested, documented, and shaped around real user needs.

This portfolio is organised around engineering evidence rather than only feature lists. Each project highlights the problem, my role, the architecture, quality practices, and trade-offs.

## Hiring snapshot

| Strength | Evidence in this portfolio |
|---|---|
| **Clean architecture & OOP** | Ticket to Ride: London, Lock-in, CodeVenture |
| **Full-stack product engineering** | Lock-in, CodeVenture, NotMoodle |
| **Testing, CI & maintainability** | Lock-in quality gates, Ticket to Ride test suite, NotMoodle team workflow |
| **Systems thinking** | mini-fabric-lab, cloud/deployment work, data visualisation ETL |
| **Communication & documentation** | Case studies, ADRs, architecture docs, retrospective/reporting evidence |

## Featured projects

### 1) Lock-in — AI study assistant browser extension

**What:** In-page study assistant Chrome extension and backend. It keeps notes, lecture transcripts, AI help, tasks, and revision tools in one place on the page a student is viewing. Consent-first design: only selected/requested study content is used; LMS credentials never leave the browser.

**Role:** Solo builder — product design, architecture, frontend, backend, database, CI/CD, security posture, documentation.

**Tech:** TypeScript, React, Lexical, Node.js, Express, Supabase (Postgres + Storage + Auth), pgvector, Docker, Azure Container Apps, GitHub Actions (OIDC), Trivy, Sentry.

**Engineering highlights:**
- Layered monorepo: `/core`, `/api`, `/backend`, `/extension`, `/integrations`, `/ui`
- Backend boundaries: Routes → Controllers → Services → Repositories/Providers
- Dependency rules enforced with dependency-cruiser
- Zod validation at API boundaries and message seams
- Reliability patterns: idempotency, timeouts, graceful fallbacks, request guards for streaming
- Security/privacy: Supabase RLS, backend-only secrets, transcript URL redaction, 90-day TTL, Trivy scan gates
- CI quality gates: format, lint, type-check, tests, coverage, build, security scan

**Links:** [Repo](https://github.com/matthewcks-prog/Lock-in) · [Demo](https://drive.google.com/file/d/1BxJfHWy5cRs7nm8L6mrWar0dgdRflV-M/view?usp=sharing) · [Case study](https://github.com/matthewcks-prog/portfolio/blob/main/projects/lock-in.md)

---

### 2) Ticket to Ride: London — OOP architecture & design

**What:** Java Swing implementation of Ticket to Ride: London designed around maintainable object-oriented architecture, not a UI-heavy script or single controller. Game rules live outside Swing, player actions enter through application commands, and UI panels render immutable snapshots.

**Role:** Solo builder — domain modelling, Java implementation, Swing UI, architecture documentation, test strategy, refactoring evidence.

**Tech:** Java 25, Swing, Maven, JUnit, Checkstyle, Javadoc.

**Engineering highlights:**
- Four-layer architecture: `domain`, `application`, `infrastructure`, `ui`
- Rich domain model for board, route, player, card, ticket, turn, game, and scoring rules
- Command pattern for player actions such as claiming routes and drawing cards
- Observer pattern for UI updates from immutable `GameSnapshot` data
- Strategy pattern for route requirements and shuffle behaviour
- Factory pattern for authoritative London board/deck/setup data
- Architecture boundary test guarding domain/application/infrastructure from Swing/AWT imports
- 106 automated tests, Maven `verify`, Google Java Style via Checkstyle, Javadoc generation, ADRs

**Links:** [Repo](https://github.com/matthewcks-prog/Ticket-to-Ride-Game) · [Case study](https://github.com/matthewcks-prog/portfolio/blob/main/projects/ticket-to-ride-game.md)

---

### 3) mini-fabric-lab — reproducible network fabric lab

**What:** Fully containerized spine-leaf network fabric lab built with Containerlab and FRRouting. It models an OSPF underlay, iBGP overlay with route reflectors, and an eBGP edge router with policy control and automated verification.

**Role:** Solo builder — topology design, routing configuration, automated verification, failure testing, documentation.

**Tech:** Containerlab, FRRouting, OSPF, iBGP, eBGP, Python, Docker, Make.

**Engineering highlights:**
- Topology-as-code with a declarative lab definition
- Configs-as-code with version-controlled FRR configs
- OSPF underlay for loopback reachability and stable BGP peering
- iBGP overlay using route reflectors for scalable session design
- eBGP edge peering with default-route injection and prefix filtering
- Python healthcheck validating containers, adjacencies, BGP sessions, routes, and end-to-end connectivity
- Failure testing showing expected behaviour during link loss and recovery

**Why it matters:** Demonstrates systems thinking beyond application code: protocol behaviour, routing design, policy control, reproducibility, automated verification, and troubleshooting discipline.

**Links:** [Repo](https://github.com/matthewcks-prog/mini-fabric-lab) · [Case study](https://github.com/matthewcks-prog/portfolio/blob/main/projects/mini-fabric-lab.md)

---

### 4) CodeVenture — Python learning platform

**What:** Python learning platform with lessons, auto-graded quizzes/challenges, a browser-based Python playground, and progress tracking for students and educators. Built in first year and expanded as I learned more about Django, testing, and deployment.

**Role:** Solo builder — backend, data model, frontend templates, authentication, deployment, documentation.

**Tech:** Django 4.2, PostgreSQL, SQLite locally, HTML/CSS/JavaScript, Google OAuth via `django-allauth`, GitHub Actions, Render, Figma.

**Engineering highlights:**
- Django monolith separated into apps for learning modules, assessments, playground, and authentication
- Environment-based production setup with `DATABASE_URL`, `.env` configuration, WhiteNoise static assets, and Render deployment
- Google OAuth callback/session flow with secure configuration
- CI checks with `pytest`, `pytest-django`, and `manage.py check --deploy`
- Monaco-based Python playground for browser-based coding practice
- UX flows designed and iterated in Figma before implementation
- **Recognition:** Monash commendation for FIT1056 coursework

**Links:** [Repo](https://github.com/matthewcks-prog/CodeVenture) · [Live](https://codeventure-ez4m.onrender.com) · [Feature overview PDF](https://github.com/matthewcks-prog/CodeVenture/blob/main/Main%20features%20implemented%20for%20Code%20Venture.pdf) · [Case study](https://github.com/matthewcks-prog/portfolio/blob/main/projects/codeventure.md)

---

### 5) NotMoodle — team LMS with RAG assistant

**What:** Lightweight Learning Management System built by a 7-person team for Monash FIT2101. It supports multi-role access, course/lesson management, classroom tracking, and an AI assistant grounded in course content through RAG.

**Role:** Scrum Master, integrator, AI assistant contributor, test infrastructure contributor, code review/merge workflow contributor.

**Tech:** Django 5.x, PostgreSQL + pgvector, Ollama (`llama3.1`, `nomic-embed-text`), pytest, GitLab CI.

**Engineering highlights:**
- Scrum-inspired delivery with Jira task/time tracking, sprint planning, retrospectives, and Definition of Done
- RAG pipeline work: chunking, embeddings, vector similarity, and rate-limited chat API
- Test infrastructure and CI quality gates
- Git feature-branch workflow, merge requests, code reviews, and integration support
- Delivered a functional web app while coordinating within a multi-person team

**Links:** [Repo](https://github.com/matthewcks-prog/NotMoodle) · [Case study](https://github.com/matthewcks-prog/portfolio/blob/main/projects/notmoodle.md) · [Demo](https://drive.google.com/file/d/1IEYiaHdESkzElaRnT_lVPxgtUEioPg0w/view?usp=sharing) · [Retrospective report](https://github.com/matthewcks-prog/NotMoodle/blob/main/Retrospective_report.pdf)

---

### 6) Data visualisation projects

**What:** Two storytelling dashboards:
- **Sun & Skin — UV vs Melanoma in Australia** using Vega-Lite
- **Groceries up, kitchens adjust — Prices vs searches in Australia** using Tableau Public

**Tech:** Vega-Lite, Vega, Vega-Embed, HTML/CSS/JavaScript, Python (`pandas`, `requests`, `openpyxl`), TopoJSON/GeoJSON, Tableau Public, Excel.

**Engineering highlights:**
- Interactive dashboards with cross-filtering, state/city drill-downs, and overview → filter → details flows
- Deliberate visual encodings: small multiples, choropleths, scatterplots, heatmaps, lollipop charts, custom tooltips
- Reproducible ETL and pre-aggregation to keep front-end experiences fast and API-independent
- Responsible communication: clear annotation of limitations, uncertainty, and association vs causation

**Links:** [Vega-Lite repo](https://github.com/matthewcks-prog/data-viz-vega-lite) · [Vega-Lite live demo](https://matthewcks-prog.github.io/data-viz-vega-lite/) · [Vega-Lite case study](https://github.com/matthewcks-prog/portfolio/blob/main/projects/data-viz-vega-lite.md) · [Tableau dashboard](https://public.tableau.com/app/profile/matthew.chung.kai.shing/viz/DataVisualisation1_17570904026250/Datavisualization1dashboard?publish=yes) · [Tableau case study](https://github.com/matthewcks-prog/portfolio/blob/main/projects/data-viz-tableau.md)

## Coursework highlights

- **Software Design / OOP:** SOLID principles, layered architecture, UML, design patterns, refactoring, architecture documentation
- **Testing & Quality:** unit/integration testing, regression prevention, CI checks, maintainability practices
- **Databases & SQL:** schema design, normalisation, indexing, joins/aggregations, transactions, PostgreSQL
- **Cloud / Big Data:** Azure fundamentals, storage, deployment workflows, Python/SQL data processing

## Notes

I keep this portfolio focused on polished, reviewable work. For private university or team work, I can share demos, reports, and implementation details where appropriate.
