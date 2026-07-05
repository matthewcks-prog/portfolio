# TTR London — Case Study

> **One-liner:** Java 25 Swing board-game implementation built around clean object-oriented design, layered architecture, and testable game rules.  
> **Role:** Solo developer  
> **Focus:** OOP, SOLID principles, domain modelling, design patterns, testing, software architecture  
> **Links:** [Project repository](https://github.com/matthewcks-prog/Ticket-to-Ride-Game) · [Demo video](https://drive.google.com/file/d/16gYKUoiqWr433hspgT5yW5bvSiqttjQ8/view?usp=drive_link) · [Architecture guide](https://github.com/matthewcks-prog/Ticket-to-Ride-Game/blob/docs/architecture-and-rules-record/architecture_guide.md) · [Rules spec](https://github.com/matthewcks-prog/Ticket-to-Ride-Game/blob/docs/architecture-and-rules-record/game_rules.md)

---

## Overview

This is a Java desktop board-game project built for a software architecture and design unit. The main goal was not only to make the game playable, but to model a rules-heavy system in a way that stays maintainable, testable, and easy to reason about.

The project uses a layered structure where the domain owns the rules, the application layer coordinates use cases, infrastructure creates setup data and randomness, and Swing focuses on rendering and input.

---

## Demo

A recorded walkthrough is available here: [Ticket_to_Ride_Sprint3_implementation.mp4](https://drive.google.com/file/d/16gYKUoiqWr433hspgT5yW5bvSiqttjQ8/view?usp=drive_link).

The demo shows the integrated Swing flow and helps reviewers quickly understand the gameplay, UI, and implemented Sprint 3 features without needing to build the project locally first.

---

## Problem

Board games have many rule interactions: turn order, route claiming, card payment, destination-card choices, final scoring, double-route constraints, and tie-breakers. If these rules are mixed directly into button listeners and UI panels, the code becomes difficult to test and difficult to change.

The core design rule was:

> **Game rules must live outside Swing.**

That means the core game can be tested without opening a UI window, and the UI cannot directly mutate domain state or calculate rules.

---

## Tech stack

| Area | Tools |
|---|---|
| Language | Java 25 |
| UI | Java Swing |
| Build | Maven |
| Testing | JUnit |
| Quality | Checkstyle, Google Java Style, Javadoc |
| Documentation | README, architecture guide, ADRs, domain model, rules spec |

---

## Architecture

```text
ttrlondon/
├── domain/          # Board, routes, cards, players, turns, scoring
├── application/     # Commands, results, snapshots, application service
├── infrastructure/  # Factories, setup data, shuffle strategies
└── ui/              # Swing panels, renderers, view models
```

| Layer | Responsibility | Boundary |
|---|---|---|
| `domain` | Game rules, state, invariants, scoring | No Swing/AWT imports |
| `application` | Coordinates use cases and exposes player actions | No rendering logic |
| `infrastructure` | Builds board/deck/game setup and randomness | No UI decisions |
| `ui` | Displays state and captures user intent | No direct rule validation or domain mutation |

The result is a desktop app with a testable core. Swing panels submit actions and render snapshots; they do not decide whether a move is legal.

---

## Design choices

### Rich domain model

Domain classes own behaviour instead of acting as passive data containers:

- `Route` validates and records route claims.
- `Player` manages cards, buses, score, and destination cards through methods.
- `TurnManager` controls turn progression and final-round sequencing.
- `ScoreCalculator` delegates route scoring, destination completion, and longest-path logic.

This keeps responsibilities close to the concepts they describe and avoids a single controller owning the whole game.

### Command pattern

Player actions enter through command objects such as:

- `ClaimRouteCommand`
- `DrawTransportCardCommand`
- `DrawDestinationTicketsCommand`

`GameApplicationService` acts as the entry point for use cases. This keeps UI event handlers small and makes actions easier to test.

### Observer pattern

The UI listens for state changes and renders immutable snapshots such as `GameSnapshot`. This prevents Swing components from mutating live domain objects.

### Strategy and Factory patterns

`RouteRequirement` models route rule variation without spreading route-type conditionals through the UI. `ShuffleStrategy` makes randomness injectable for deterministic tests. `BoardFactory`, `DeckFactory`, and `GameFactory` centralise setup data.

---

## Quality evidence

- 106 automated tests for the current implementation
- Architecture boundary tests preventing Swing/AWT imports in `domain`, `application`, and `infrastructure`
- Maven `verify` for build and verification
- Checkstyle enforcing Google Java Style with zero current violations
- Javadoc generation succeeds
- Architecture Decision Records for key patterns and quality decisions
- Known defects tracked explicitly in project documentation

These checks make the architecture enforceable rather than only documented.

---

## Feature scope

The application supports:

- 2–4 player setup with player names, colours, first-player selection, and initial destination-card choices
- route claiming
- transportation-card draws
- destination-card draws
- five-card face-up transportation market
- score markers around the board edge
- final-round indication
- final scoring, winner display, and tie-breaker details

---

## Skills demonstrated

- Java and object-oriented programming
- SOLID principles and clean architecture
- Domain modelling and layered design
- Design patterns: Command, Observer, Strategy, Factory
- Unit testing with JUnit
- Maven build workflows
- Static analysis and style enforcement with Checkstyle
- Documentation with ADRs, architecture guides, and Javadocs
- Refactoring for maintainability and separation of concerns

---

## What I learned

This project reinforced that UI code should not be the centre of the architecture. For a rules-heavy application, the most important design work is deciding where behaviour belongs, how state changes enter the system, and how to keep boundaries enforceable through tests.

---

## Next improvements

- Add screenshots to make the repository easier to scan visually.
- Add a CI badge once the workflow is stable and public.
- Add a compact architecture diagram image for the README.
- Expand automated smoke coverage around the full Swing workflow where practical.
