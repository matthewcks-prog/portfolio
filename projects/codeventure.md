## CodeVenture

**One-liner**: **CodeVenture** is a gamified Python learning platform with interactive lessons, auto-graded quizzes/challenges, and a safe in-browser playground for students and educators.  
**Status**: Active • **Role**: Solo developer (view commit history) • **Timeline**: 2023–present  
**Repo**: [github.com/matthewcks-prog/CodeVenture](https://github.com/matthewcks-prog/CodeVenture)  
**Live**: https://codeventure-ez4m.onrender.com

**Feature list**: [Main features implemented for CodeVenture (PDF)](https://github.com/matthewcks-prog/CodeVenture/blob/main/Main%20features%20implemented%20for%20Code%20Venture.pdf)

---

### Snapshot

- **Problem**: Intro programming courses are often dry and non-interactive, which makes it hard for students to stay engaged or see their progress.  
- **Solution**: A web platform that breaks Python into bite-sized modules with videos/text, auto-graded quizzes, hands-on coding challenges, and a built-in Python playground, all wired into progress tracking.  
- **Impact**: Helps educators onboard students into Python quickly, while learners can practice safely in the browser, get instant feedback, and track their progress over time.

---

### Tech stack

- **Frontend**: Django templates, HTML/CSS, responsive layout (Bootstrap-style), vanilla JS  
- **Backend**: Django 4.2 (Python), Django views/forms, custom management commands (e.g. `seed_data`)  
- **Data/Storage**: SQLite for local dev, PostgreSQL on Render (`DATABASE_URL`)  
- **Infra/DevOps**: Render web service (`render.yaml` blueprint), Gunicorn, WhiteNoise, environment-based settings  
- **Testing/CI**: `pytest`, `pytest-django`, `manage.py check --deploy`, GitHub Actions (tests, lint, build)

---

### What I built

#### Core features

- Guided learning modules that structure Python concepts into a clear curriculum with videos and rich text.  
- Auto-graded quizzes and coding challenges with immediate scoring and feedback.  
- A safe in-browser Python playground (Monaco-based editor) so students can experiment without installing anything.  
- Progress dashboards and exportable reports for students and educators.  
- Google OAuth login via `django-allauth` for low-friction, secure authentication.  
- Admin tooling and seeding commands to bootstrap curriculum, assessments, and an admin user in one step.

#### Key workflows

- **Student journey**: sign up → land on dashboard → pick a module → complete lessons → take quizzes → tackle coding challenges → review progress report.  
- **Educator/admin workflow**: deploy app → run `seed_data` to provision curriculum + admin → log into admin → tweak modules/assessments → monitor student outcomes.

---

### Architecture

- **Overall**: Django monolith with separate apps for playground, learning resources, assessments, and authentication.  
- **Frontend**: HTML template-based UI backed by Django views/forms for predictable, easy-to-deploy behavior.  
- **Configuration**: Environment-based settings with `.env` and `DATABASE_URL` so the same code runs locally and on Render.  
- **Seeding**: Idempotent `seed_data` pipeline that can be safely run on every deploy to keep curriculum in sync.

**Main boundaries**

- **Learning/assessment modules**: Django app with models for modules, submodules, quizzes, and challenges.  
- **Python playground**: Separate app encapsulating the Monaco editor UI and execution endpoints.  
- **Auth & user management**: Django auth + `django-allauth` for Google OAuth and user profiles.  
- **Platform & deployment**: Settings module, `render.yaml`, and `build.sh` to handle migrations, static files, and seeding.

> _Diagram (optional)_: Architecture diagram showing Django apps (learning, assessments, playground, auth) and their data flows to PostgreSQL.

---

### Data model (simplified)

- **Entities**:  
  - `User`  
  - `LearningModule`, `SubModule`  
  - `Quiz`, `Question`  
  - `Challenge`, `Submission`  
  - `ProgressReport`  

- **Relationships**:  
  - A `User` has many `ProgressReport` and `Submission` records.  
  - A `LearningModule` has many `SubModule`.  
  - A `SubModule` has many `Quiz` and `Challenge`.  
  - A `Quiz` has many `Question`.  
  - Each `Submission` is tied to a specific quiz or challenge.

- **Key constraints**:  
  - Foreign keys enforce ownership between modules/quizzes/challenges.  
  - Unique constraints on slugs/codes for modules.  
  - Validation around scoring and progress aggregation to avoid inconsistent results.

---

### Engineering highlights

#### Scalability & performance

- **Approach**: Simple, cache-friendly views, pagination on heavy pages, static assets via WhiteNoise, curriculum stored in the database (not hard-coded).  
- **Bottlenecks addressed**:  
  - Moved curriculum setup into a seeding command instead of on-request seeding.  
  - Reduced duplicate queries using selective `select_related` / `prefetch_related` where needed.  
- **Result**: Architecture can scale by upgrading Postgres or adding more web workers on Render without code changes; `manage.py check --deploy` passes under production settings.

#### Reliability & resilience

- **Seeding & migrations**: Idempotent seeding commands that are safe to rerun; migrations + seeding are wired into the deployment flow so each environment boots in a valid state.  
- **Validation**: Django forms and model validators guard user input; quizzes/submissions enforce score ranges and required fields.  
- **Ops visibility**: Clear separation of dev/prod settings, admin views for inspecting data, and structured debug pages in development.  
- **Edge cases**: Handles incomplete quizzes, partially finished modules, and reseeding without duplicating content.

#### Security & privacy

- **Secrets hygiene**: All sensitive values (Django secret key, OAuth credentials, database URL) come from environment variables; sample values live in `.env.example` only.  
- **AuthN/AuthZ**: `django-allauth` with Google OAuth for authentication; Django’s permission system and admin for staff-only actions.  
- **Data protection**: Minimal PII (mainly auth + progress); TLS handled by Render; CSRF protection and HTTPS-only configuration in production.  
- **Abuse controls**: Request validation on forms, CSRF tokens, and conservative logging to avoid leaking sensitive data.

#### Maintainability

- **Structure**: Modular Django apps with clear responsibilities and conventional layout.  
- **Docs**: Root `README.md` for setup, `docs/` for OAuth/deployment, `assets/README.md` for visual documentation.  
- **Consistency**: Shared settings patterns, consistent URL/view/template structure.  
- **Extensibility**: New modules, assessments, or even new languages can be added via models and seeding scripts without reworking the core architecture.

---

### Testing & CI

- **Unit tests**: Model and utility tests (e.g. scoring logic, seeding logic).  
- **Integration tests**: View and template tests for key flows (login, module listing, quiz submissions) using `pytest-django`.  
- **CI pipeline**: GitHub Actions workflow running `pytest`, optional linting, and build checks on each push/PR.  
- **Quality gates**: Tests must pass before deploy; `manage.py check --deploy` is used in production environments.

---

### Demo & screenshots
**Screenshots** (from `assets/`):

- Student dashboard after login (`home.png`)  
- Python playground running sample code (`python_playground.png`)  
- Learning module and quiz flow (`learning_module.png`, `quizzes.png`)  
- Coding challenges overview (`challenges.png`)  
- Welcome/sign-up flow (`welcome_page.png`, `sign_up_page.png`)

---

### Key trade-offs

| Decision                 | Options considered          | Why I chose it                                                                 | What I’d change next                                      |
|--------------------------|-----------------------------|--------------------------------------------------------------------------------|-----------------------------------------------------------|
| Web framework            | Django vs Flask/FastAPI     | Django provides batteries-included auth, admin, ORM, and templates             | Add a SPA frontend if the UI needs to grow in complexity |
| Hosting                  | Self-hosted vs Render (PaaS)| Render simplifies deployments and environment management for a solo project     | Move to a more customizable setup if scale demands it     |
| Curriculum management    | Manual data vs scripted     | Scripted seeding ensures any environment can be bootstrapped consistently      | Add a simple admin UI for editing modules without code   |

---

### Challenges & learnings

- **Keeping multiple environments in sync**  
  - **Challenge**: Local, staging, and production databases drifting apart.  
  - **Fix**: Wrote an idempotent `seed_data` command and wired it into the deployment/build process.  
  - **Learning**: Treating content as data and scripting setup drastically reduces maintenance and onboarding time.

- **Reliable Google OAuth across localhost and Render**  
  - **Challenge**: `redirect_uri_mismatch` issues and environment-specific URLs.  
  - **Fix**: Centralized OAuth settings in environment variables and documented exact redirect URIs and common error fixes.  
  - **Learning**: Clear documentation around third-party auth saves a lot of debugging time for me and future collaborators.

---

### Roadmap

- Add richer analytics dashboards for teachers (e.g. cohort performance, question difficulty).  
- Introduce badges/achievements to deepen the gamification loop.  
- Expand beyond Python (e.g. JavaScript) reusing the existing architecture and seeding pipeline.  

---

### How to run (short)

Full setup instructions are in the project `README.md`. Summary:
