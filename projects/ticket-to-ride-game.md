# Ticket to Ride: London — Case Study

> **One-liner:** Java Swing board game implementation designed around clean OOP, layered architecture, and testable game rules.  
> **Status:** Complete / portfolio-ready  
> **Role:** Solo builder  
> **Links:** [Repo](https://github.com/matthewcks-prog/Ticket-to-Ride-Game) · [Architecture guide](https://github.com/matthewcks-prog/Ticket-to-Ride-Game/blob/docs/architecture-and-rules-record/architecture_guide.md) · [Rules spec](https://github.com/matthewcks-prog/Ticket-to-Ride-Game/blob/docs/architecture-and-rules-record/game_rules.md)

---

## Why this project matters

Ticket to Ride: London is a desktop game, but the main engineering goal was not simply to draw a playable board. The goal was to show that I can take a rules-heavy problem and design it as maintainable software:

- game rules independent from the UI
- meaningful domain objects rather than empty data containers
- clear application use cases for player actions
- data-driven board/deck setup
- automated tests and architecture checks
- documented trade-offs and design decisions

That makes this project a strong example of my software engineering architecture and design work.

---

## Problem

A board game implementation can easily become one large controller or UI class: button handlers validate rules, update state, draw the board, calculate scores, and decide winners. That kind of design works for a demo but becomes hard to test, extend, or debug.

For this project, I treated the game as a domain problem first and a Swing UI second. The key architectural rule was:

> **The game rules must be playable and testable without Java Swing.**

---

## Tech stack

| Area | Tools |
|---|---|
| Language | Java 25 |
| UI | Java Swing |
| Build | Maven |
| Testing | JUnit |
| Quality | Checkstyle, Google Java Style, Javadoc |
| Documentation | README, architecture guide, rules spec, ADRs, domain model, implementation notes |

---

## Architecture

The codebase follows a four-layer structure under `src/main/java/ttrlondon`:

```text
ttrlondon/
├── domain/          # Game rules, state, invariants
├── application/     # Use-case coordination and commands
├── infrastructure/  # Factories, configuration, shuffle strategies
└── ui/              # Swing rendering and user input
```

### Layer responsibilities

| Layer | Responsibility | Boundary rule |
|---|---|---|
| `domain` | Board, routes, cards, tickets, players, turns, scoring | No Swing/AWT imports |
| `application` | Coordinates player actions through commands and services | No rendering logic |
| `infrastructure` | Creates London board/deck/setup data and randomness strategies | No UI logic |
| `ui` | Displays snapshots and forwards user intent | Does not validate rules or mutate domain state directly |

The architecture keeps the core game logic testable without launching a GUI. Swing panels render state and send user intentions; they do not own the rules.

---

## OOP and design principles demonstrated

### 1. Rich domain model

Domain classes own the behaviour that naturally belongs to them:

- `Route` validates whether a claim is legal and records claims.
- `Player` manages cards, buses, score, and destination tickets through methods rather than exposing mutable internals.
- `TurnManager` controls turn progression and final-round sequencing.
- `ScoreCalculator` delegates ticket completion and longest-path logic to focused collaborators.

This avoids an anemic model where one controller knows everything.

### 2. Separation of concerns

The UI is responsible for rendering and input only. Rules such as route claiming, card payment, destination ticket scoring, double-route restrictions, and final scoring stay in domain/application classes.

### 3. Dependency boundaries

Domain, application, and infrastructure code are guarded from Swing/AWT imports by an architecture boundary test. This turns an architectural decision into an executable check rather than relying only on discipline.

### 4. Polymorphism and strategies

`RouteRequirement` and `ShuffleStrategy` make rule variation and randomness injectable. This supports ferry routes, grey routes, coloured routes, deterministic tests, and production shuffling without scattering conditional logic through the UI.

### 5. Commands and application service

Player actions enter through command objects such as:

- `ClaimRouteCommand`
- `DrawTransportCardCommand`
- `DrawDestinationTicketsCommand`

`GameApplicationService` becomes the single entry point for use cases. This keeps UI event handlers small and gives tests a stable application API.

### 6. Observer-driven UI updates

The UI listens for game-state changes and renders immutable snapshots such as `GameSnapshot`. That avoids leaking mutable domain state into Swing panels.

### 7. Factories for authoritative setup data

Board, deck, and game setup data are centralised through factory classes. This keeps London map/deck creation out of UI code and makes setup easier to audit.

---

## Quality and verification

The project includes several pieces of engineering evidence:

- **106 automated tests** passing for current implementation
- **ArchitectureBoundaryTest** ensuring domain/application/infrastructure do not import Swing/AWT
- **Maven `verify`** for build and verification
- **Checkstyle** enforcing Google Java Style with zero current violations
- **Javadoc generation** succeeds
- **ADRs** documenting Command, Observer, Strategy, Factory, setup draft handling, action eligibility, and automated quality gates
- **Known defects** tracked explicitly instead of hidden

This is the part of the project I would emphasise most in an interview: it shows maintainability habits, not just feature completion.

---

## Feature scope

The playable flow supports:

- 2–4 player setup with names, colours, first-player selection, and initial destination ticket choices
- route claiming
- transportation card draws, including blind deck draw
- destination ticket draws
- five-card face-up transportation card market
- score markers around the board edge
- final-round indication
- final scoring, winner display, and tie-breaker details

---

## Trade-offs and design decisions

| Decision | Why it was chosen | Trade-off |
|---|---|---|
| Swing UI with domain-first architecture | Required desktop GUI while keeping rules testable | More upfront structure than a simple UI script |
| Commands for player actions | Keeps UI handlers thin and use cases explicit | Adds extra classes but improves clarity and testing |
| Immutable snapshots for UI | Prevents Swing panels from mutating game state | Requires DTO mapping from domain to view state |
| Factories for board/deck data | Centralises authoritative setup data | Data updates must go through factory/source documents |
| Strategy for randomness and route requirements | Enables deterministic tests and extensible route rules | Slightly more abstraction than direct conditional logic |
| ADRs and architecture docs | Makes decisions explainable for review/interviews | Takes additional documentation time |

---

## What I learned

- How to keep UI code from becoming the architecture.
- How to model a rules-heavy domain using entities, value objects, services, and clear responsibilities.
- How design patterns are useful when tied to real forces: testability, extensibility, and boundary control.
- How to convert architectural rules into automated tests.
- How to document design decisions so future maintainers can understand why the code is shaped the way it is.

---

## What I would improve next

- Add a CI badge once the repository workflow is public and stable.
- Add screenshots or a short demo GIF to improve recruiter scanability.
- Add a small architecture diagram image to complement the text docs.
- Expand end-to-end smoke tests around the Swing workflow where practical.

---

## Interview talking points

If asked about this project, I would focus on:

1. **Architecture boundary:** rules outside Swing and enforceable via tests.
2. **OOP modelling:** behaviour belongs to domain objects, not one God controller.
3. **Patterns with purpose:** Command, Observer, Strategy, and Factory solve concrete maintainability problems.
4. **Quality evidence:** 106 tests, Checkstyle, Javadocs, ADRs, known-defect tracking.
5. **Extensibility:** ferry routes, randomness strategies, and setup data are designed to evolve without rewriting the UI.
