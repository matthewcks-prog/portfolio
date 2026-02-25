# NotMoodle LMS — Case Study

**One-liner:** A lightweight Learning Management System for flexible, self-paced learning, built by a 7-person team using Scrum, Jira, and Git feature-branch workflow for Monash FIT2101.

**Status:** Archived (course project) • **Role:** Team (Scrum Master, integrator, AI/QA lead) • **Timeline:** FIT2101 semester

**Links:** [Repo](https://github.com/matthewcks-prog/NotMoodle) • [Demo (mp4)](https://github.com/matthewcks-prog/NotMoodle/blob/main/demo_NotMoodle.mp4) • [Retrospective report (PDF)](https://github.com/matthewcks-prog/NotMoodle/blob/main/Retrospective_report.pdf)

---

## Snapshot

| | |
|---|---|
| **Problem** | Build an LMS for a teaching start-up focused on flexible, self-paced learning, with scope negotiated with the unit “client” (teaching associate). |
| **Solution** | Django-based LMS with multi-role access, course/lesson management, classroom tracking, student reporting, and an AI assistant grounded in course content. |
| **Impact** | Delivered a working product on time; practised Scrum, Jira, and CI/CD; demonstrated process discipline and team collaboration. |

---

## Tech stack

| Layer | Stack |
|-------|------|
| **Frontend** | Django server-rendered templates, vanilla JavaScript, custom CSS |
| **Backend** | Python 3.x, Django 5.x |
| **Data/Storage** | SQLite (local/test), PostgreSQL 14+ with pgvector (AI features) |
| **AI/ML** | Ollama (llama3.1, nomic-embed-text), RAG pipeline |
| **Infra/DevOps** | GitLab CI (tests, lint, coverage), Git feature-branch workflow |
| **Testing** | pytest, pytest-django, pytest-xdist, model-bakery, freezegun, responses |

---
## Team process & agile practices

### Scrum & Jira

- **Scrum-inspired workflow:** Sprint planning, reviews, retrospectives
- **Jira:** Task and time tracking as required by the unit; backlog management, sprint boards
- **Definition of Done:** Tests, linting, code review before merge
- **Risk management:** Identified and mitigated risks (AI stack, database changes, deployment)

### My role (Scrum Master & integrator)

- **Scrum Master:** Facilitated ceremonies, kept process visible, unblocked team
- **Integrator:** Merged features safely, ensured tests and infra stayed green
- **Collaboration:** Code reviews, merge requests, shared ownership of quality

### Delivering as a team

- **BackLog busters:** 7-person team (PO, SM, back-end, front-end, QA)
- **Git workflow:** Feature branches, merge requests, code reviews
- **Analysis of alternatives:** Evaluated AI stack, database, and deployment options before committing

## What I built

### Core features (my contributions)

- **NotMoodle AI assistant** — RAG pipeline (chunking, embeddings, pgvector similarity search), `/api/notmoodle/ask/` and `/api/notmoodle/usage/` endpoints, floating chat widget UI, rate limiting, usage tracking
- **Lessons and classroom logic** - django model-views-templates
- **Test infrastructure** — pytest setup, fixtures, ~150+ tests across 7 apps, ~95–100% coverage targets, parallel execution
- **CI/CD & quality gates** — GitLab CI pipeline for tests, linting, coverage reports, JUnit output
- **Integration & glue work** — Ensured database, AI stack, tests, and docs worked together end-to-end

### Key workflows

- **Student flow:** Enrol → dashboard → lessons → AI assistant for questions
- **Teacher flow:** Create courses/lessons → manage classrooms → grade assignments

---

## Architecture

### High-level design

- **Django apps:** `assist` (AI), `classroom_and_grading`, `course_management`, `lesson_management`, `student_management`, `teachersManagement`, `welcome_page`
- **assist app:** RAG pipeline (chunk → embed → store in pgvector), retrieval by cosine similarity, chat API with auth and rate limits
- **Test layer:** Shared `conftest.py`, `settings_test.py` (SQLite in-memory), markers for unit/integration/API

### Data model (assist)

- **Entities:** DocumentChunk (lesson content + embeddings), StudentQuestion (queries, answers, usage)
- **Relationships:** DocumentChunk → Lesson; StudentQuestion → User
- **Constraints:** IVFFlat index on embeddings; rate limit enforced in views

---

## Engineering highlights

### Reliability & resilience

- **Failure handling:** Ollama timeouts, graceful fallbacks when PostgreSQL/pgvector unavailable
- **Input validation:** Schema validation, safe defaults, defensive coding on API endpoints
- **Edge cases:** Rate limiting (100/day), insufficient context handling, CSRF protection

### Security & privacy

- **Secrets hygiene:** `.env`-driven config, no keys in repo
- **AuthN/AuthZ:** Django auth, student-only access to AI assistant
- **Compliance mindset:** No sensitive data in prompts/logs; least-privilege access; clear user consent

### Maintainability

- **Code structure:** Clear app boundaries, SOLID-inspired layering
- **Documentation:** README, `docs/` (AI guide, DB setup, test setup, implementation summary)
- **Consistency:** black, isort, flake8, mypy; `pyproject.toml` for config

### Testing & quality gates

- **Unit tests:** Models, views, forms, Ollama client (mocked)
- **Integration tests:** API flows, DB operations, pipelines
- **CI checks:** Lint, typecheck, tests, coverage on every push
- **Quality gates:** Coverage targets, PR checks, merge-request widgets

---



---

## Demo

- **Live:** N/A (local deployment)
- **Video:** [demo_NotMoodle.mp4](../demo_NotMoodle.mp4)

---

## Trade-offs & decisions

| Decision | Options considered | Why I chose it | What I’d change next |
|----------|--------------------|----------------|----------------------|
| Ollama (local LLM) | OpenAI API, other cloud LLMs | No API costs, privacy-friendly, fits unit constraints | Consider streaming responses (SSE) |
| pgvector | Pinecone, Chroma, FAISS | Native Postgres, single DB, simpler ops | Evaluate HNSW index for larger scale |
| pytest + SQLite | Django TestCase, Postgres | Fast feedback, parallel runs, CI-friendly | Add E2E with Playwright if time |

---

## Challenges & how I solved them

**Challenge:** AI stack and DB choices had to align with team skills and unit requirements.  
**Fix:** Documented analysis of alternatives; chose Ollama + pgvector for simplicity and local control.  
**What I learned:** Process documentation helps align technical decisions with stakeholders.

**Challenge:** Keeping 7 people’s work integrated without breaking the build.  
**Fix:** Strong CI, shared fixtures, clear merge workflow; acted as integrator to resolve conflicts early.  
**What I learned:** Integration is a first-class role; catching issues in CI saves time.

---

## Roadmap

- [ ] Streaming responses (SSE) for better UX
- [ ] Conversation history / session memory
- [ ] HNSW index for larger lesson corpora

---

## My contribution (team project)

**My responsibilities:** Scrum Master, AI assistant design/implementation, test infrastructure, CI/CD, integration.

**Key areas:** `assist` app (RAG, API, UI), pytest setup, `conftest.py`, GitLab CI, docs.

**Collaboration:** Code reviews, merge requests, sprint planning, retrospectives; worked with back-end and front-end teammates on lesson/classroom logic and UI integration.

---

## How to run (short)

Full setup is in the project repo README.

```bash
git clone https://github.com/matthewcks-prog/NotMoodle
cd NotMoodle
python -m venv venv
venv\Scripts\activate   # Windows
pip install -r requirements.txt
cd NotMoodle
python manage.py migrate
python manage.py runserver
```

**Tests:** `pip install -r requirements-dev.txt` then `pytest` (or `pytest -n auto` for parallel).

---

## References

- [Retrospective report (PDF)](../Retrospective_report.pdf) — Team process, agile practices, lessons learned
- [FIT2101 — Software Engineering Process and Management](https://handbook.monash.edu/2025/units/FIT2101) (Monash)
