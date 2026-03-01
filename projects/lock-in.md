# Lock-in — Case Study

> **One-liner:** AI-powered Chrome extension that helps students learn more efficiently through contextual explanations, rich notes, and lecture transcript extraction.  
> **Status:** Active • **Role:** Solo • **Timeline:** 2025–2026   
> **Links:**
> - [demo_video_lock-in.mp4](https://drive.google.com/file/d/1BxJfHWy5cRs7nm8L6mrWar0dgdRflV-M/view?usp=sharing)
> - [Repo](https://github.com/matthewcks-prog/Lock-in)
> - [Docs](https://github.com/matthewcks-prog/Lock-in/tree/main/docs)
---

## Intro

 Over the summer I built **Lock-in**, an in-page study assistant that keeps your notes, lecture transcripts, AI help, tasks, and revision tools in one place, on the page you're viewing, so you don't bounce between tabs and lose context.

---

## Problem + Principle

When I study, I used AI a lot—but the workflow was messy: lecture in one tab, ChatGPT in another, notes somewhere else. Lock-in solves this by living inside the page and only using content the user has already accessed.

It's designed with a **consent-first mindset**: you choose what to highlight, ask about, or summarise—no scraping hidden systems. Your LMS credentials and cookies never leave the browser; the extension does not read or forward those cookies to the backend. Only the study content you explicitly select or request—transcripts, highlights, notes—flows through. That keeps it transparent and aligned with academic integrity: the assistant stays grounded in your own materials, and you stay in control.

---

## Tech Stack

| Layer | Technologies |
|-------|--------------|
| **Frontend** | React 18, Lexical (rich text), TanStack Query, Vite, Tailwind, TypeScript |
| **Backend** | Node.js, Express, Supabase (Postgres + Storage + Auth) |
| **Data/Storage** | Supabase (Postgres, RLS, pgvector for embeddings), Supabase Storage |
| **Infra/DevOps** | Docker, Azure Container Apps, Azure Container Registry, GitHub Actions (OIDC), Trivy security scan |
| **Testing** | Vitest (frontend), Node.js test runner (backend), MSW, coverage gates in CI |

---

## What I Built

### Core features

- **Explain** — Right-click any text → AI explanation in plain English with examples
- **Sidebar chat** — Persistent chat history, 65/35 split layout, context menu prefill
- **Rich notes** — Lexical editor (bold, lists, headings), autosave, image/file attachments, course/week auto-linking, starred notes
- **Transcripts** — Extract from Panopto, Echo360, HTML5; AI transcription for videos without captions; caching by media fingerprint
- **Tasks & revision** — Study planning and revision tools in one place
- **Feedback** — In-app bug reports and feature requests with auto-captured context (URL, course code, version)

### Key workflows

1. **Explain flow:** Highlight → Right-click "Lock-in: Explain" → Sidebar opens with prefilled text → Send → Streaming AI response
2. **Note capture:** Create note from selection or chat → Auto-linked to course/week → Semantic search via embeddings
3. **Transcript flow:** Detect video on page → Extract or transcribe → Cache by fingerprint → Use in chat/notes

---

## Architecture

### High-level design

- **Layered monorepo:** `/core` (platform-agnostic domain), `/api` (typed client), `/backend` (Express API), `/extension` (Chrome glue), `/integrations` (site adapters), `/ui` (React components)
- **Strict boundaries:** No cross-layer imports (enforced by dependency-cruiser); `/core` and `/api` are Chrome-free and testable in Node
- **Backend layering:** Routes → Controllers → Services → Repositories/Providers; Controllers never touch the database; Services never see HTTP
- **Transcript system:** Core providers (pure TS) + injected fetchers; extension implements Chrome-specific fetcher for CORS/credentials
- **Single widget:** One `<LockInSidebar />` reused across sites; site differences via adapters/context

### Data model

- **Entities:** Users, Chats, ChatMessages, Notes, NoteAssets, Folders, Transcripts, TranscriptJobs, Feedback, AIRequests, IdempotencyKeys
- **Relationships:** User → Chats → Messages; User → Notes → NoteAssets; User → Transcripts/Jobs; User → Feedback
- **Constraints:** RLS on all tables; pgvector for note embeddings; idempotency keys for chat deduplication; transcript cache by (user_id, fingerprint)

---

## Engineering Highlights

### Scalability & performance

- Rate limiting per user, idempotency for chat requests, transcript caching by fingerprint, pagination-ready indexes
- AI token limits handled via truncation; transcript upload chunking with rate limits; orphan asset cleanup (scheduled)
- Indexes on user+created_at, partial index for starred notes, ivfflat for vector search

### Reliability & resilience

- Timeouts on async ops (`withTimeout()`), retries in providers, idempotency for duplicate requests
- Zod schemas at API boundaries and message seams; `ValidationError` with structured details
- Pino structured logging, Sentry (backend + extension) with request-body/query redaction
- Truncate-on-edit for chat message revisions (ADR-002); requestId guards for streaming to avoid stale deltas

### Security & privacy

- AI keys only on backend; `.env.example`; no keys in repo; Trivy fails build on CRITICAL/HIGH
- Supabase JWT on protected routes; RLS for all user-scoped tables
- Transcript URL redaction, 90-day transcript TTL, PII minimization in logs
- Rate limiting per user, input validation, CORS for extension only
- **Compliance mindset:** No sensitive data in prompts/logs; least-privilege; explicit consent for AI transcription; clear academic-integrity stance in docs

### Maintainability

- AGENTS.md per layer, max file/function sizes, SOLID-style layering
- README, docs/ (architecture, DATABASE, CICD, ADRs), CODE_OVERVIEW
- Prettier, ESLint, stylelint, dependency-cruiser for architecture
- Adapter pattern for transcript providers; prompt config in `/config/prompts.js`

---

## Testing & Quality Gates

- **Unit tests:** Core logic, services (mocked repos), repositories (mocked Supabase), validators
- **Integration tests:** API flows, critical paths (backend)
- **E2E/UI:** Smoke checklist; no full E2E automation yet

**CI:** Quality gate (format-check, doc links, lint, type-check, tests, coverage, build) on every PR. Backend deploy: Build → Trivy scan → Push to ACR → Deploy to staging (develop) / production (main, with reviewers). `npm run validate` required before commit; Husky pre-commit (format + lint:fix).

---

## Challenges & How I Solved Them

- **Transcript extraction from Panopto/Echo360** — CORS, auth, varying DOM. Adapter pattern in `/integrations`; extension fetcher handles CORS via extension context; fingerprint-based caching. *Extension context bypasses CORS for same-origin-like access; adapters keep DOM logic isolated.*

- **Chat message edits invalidating later responses** — Truncate-on-edit (ADR-002): mark edited message non-canonical, insert revision, truncate subsequent messages, regenerate from canonical history. *Single-table + `is_canonical` avoids joins and branching UI; requestId guards prevent stale streaming updates.*

- **Architecture drift** — dependency-cruiser rules, AGENTS.md per layer, `npm run lint:deps` in CI. *Automated rules catch drift early; docs help humans and AI follow boundaries.*

---

## Trade-offs & Decisions

| Decision | Options considered | Why I chose it | What I'd change next |
|----------|--------------------|----------------|----------------------|
| Zod for validation | Manual checks, no validation | Consistent schemas, early contract drift detection | Passthrough for new fields; keep schemas in sync with backend |
| Truncate-on-edit for chat | Destructive delete, separate revisions table, tree model | Single-table simplicity, audit trail, no branching UI complexity | Optional TTL cleanup for non-canonical rows |
| Supabase + Postgres | Firebase, custom backend | RLS, migrations, pgvector, Storage; good DX | — |
| Layered architecture | Monolith, feature-based | Testability, clear boundaries, Chrome-free core | — |
| Azure Container Apps | Vercel, Railway, EC2 | OIDC, managed scaling, ACR integration | — |

---

## Demo

**Live:** Chrome extension — load unpacked from `extension/` after `npm run build`  
**Video/GIF:**

--

## How to Run

> Full setup: [README](https://github.com/matthewcks-prog/Lock-in#setup-instructions)

- **Clone:** `git clone https://github.com/matthewcks-prog/Lock-in.git && cd Lock-in`
- **Setup:** `.\scripts\dev\setup-local.ps1` (or manual: `npm install`, `npm run db:start`, `npm run db:reset`)
- **Run:** `npm run dev:backend` (backend) + `npm run build` (extension) → Load unpacked in Chrome at `chrome://extensions`
- **Tests:** `npm run validate` (full) or `npm run test:all`

---

## Roadmap

- [ ] E2E tests for critical flows
- [ ] Account export/delete (GDPR-style)
- [ ] Further permission minimization (callsite audit)
- [ ] Admin dashboard for feedback triage

---

## Close

Lock-in is still actively evolving and improving, but it already supports a complete study loop—from understanding to revision.

---

## References

- [Supabase RLS](https://supabase.com/docs/guides/auth/row-level-security)
- [Lexical Editor](https://lexical.dev/)
- [Chrome Extension Manifest V3](https://developer.chrome.com/docs/extensions/mv3/)
