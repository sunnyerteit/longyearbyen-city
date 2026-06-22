# Project Charter: Longyearbyen City Builder

**Charter ID:** PROJ-002
**Created:** 2026-06-21
**Last Updated:** 2026-06-21
**Status:** 🟢 Active
**Type:** Software

---

## 1. Overview

**One-line description:**
> A browser-based city builder simulation set in real Longyearbyen, built on geodata and a live economy/agent model.

**Problem statement:**
Existing city builders use generic fictional geometry. This project uses the actual street layout and building footprints of Longyearbyen — sourced either by hand-tracing or automated extraction — to create a simulation grounded in a real place.

**Success looks like:**
A playable simulation where you can zone real buildings, watch residents arrive from the airport and find work, and see the economy respond — all on an accurate map of Longyearbyen. Personal and fun to use. Showable if the opportunity arises.

---

## 2. Goals & Non-Goals

### Goals — ranked by importance
- [ ] **G1 —** Real geometry: buildings and roads reflect the actual layout of Longyearbyen
- [ ] **G2 —** Working economy: residential → commercial → industrial supply chain with satisfaction and zone levelling
- [ ] **G3 —** Agent simulation: inhabitants arrive, find work, shop, and live visibly on the map
- [ ] **G4 —** Full central coverage: all major Longyearbyen neighbourhoods represented (Sentrum, Lia, Nybyen, Elvesletta, airport corridor)
- [ ] **G5 —** Svalbard flavour: light simulation touches that make this feel like Longyearbyen, not a generic city

### Non-Goals
- No multiplayer
- No mobile support
- No real-time external data feeds
- Not historically accurate — approximate and fun beats precise and broken
- No deep simulation (population dynamics, political decisions, historical periods)

---

## 3. Stakeholders

| Role | Name | Involvement |
|---|---|---|
| Owner | Sunny Islam | Final decisions, direction, geodata input |
| Contributor | Claude | PM, engineering, simulation logic, documentation |

---

## 4. Scope & Deliverables

### In Scope
- Geodata coverage of all central Longyearbyen (method TBD: hand-traced, automated, or hybrid)
- Map base layer (implementation TBD: Leaflet or custom renderer)
- Zoning system (residential / commercial / industrial) with level progression
- Agent simulation: arrival, daily schedule, pathfinding on real road graph
- Economy simulation: production, restocking, shopping, satisfaction scoring
- Svalbard-specific flavour layer (light — tourism, seasons, Arctic character)
- Tooling for adding geodata over the map (method TBD)

### Out of Scope
- Terrain modelling and elevation
- Building interiors or floor-level detail
- Historical Longyearbyen periods
- Export or sharing features
- Mobile support

### Definition of Done
All central neighbourhoods are covered, the economy runs without obvious exploits, agents move believably on the real road network, and the simulation can run for 30+ days without degrading. Satisfying to use.

---

## 5. Phases & Milestones

| Phase | Name | Description | Status |
|---|---|---|---|
| **Phase 0** | Inception | Concept, tooling, first working prototype on real map tiles | ✅ Complete |
| **Phase 1** | Alpha | Core features: full central geodata, stable economy, agent simulation working | 🟢 Active |
| **Phase 2** | Beta | Feature complete: Svalbard flavour, economy tuning, balance | ⬜ Not Started |
| **Phase 3** | Release Candidate | Polish, stability, 30-day run test, showable if desired | ⬜ Not Started |

### Phase 1 Milestones

| Milestone | Description | Status |
|---|---|---|
| M1 — Airport corridor | Airport-to-Sentrum road + first buildings simulating | ✅ Complete |
| M2 — Sentrum | Commercial core traced and zoned | ⬜ Not Started |
| M3 — Lia | Hillside residential neighbourhood | ⬜ Not Started |
| M4 — Nybyen | Historic workers' district | ⬜ Not Started |
| M5 — Elvesletta | Residential area near the shore | ⬜ Not Started |
| M6 — Alpha complete | All central neighbourhoods in, economy stable | ⬜ Not Started |

---

## 6. Linked Documents

| Document | Location |
|---|---|
| Task board | [TASKS.md](./TASKS.md) |
| Risk register | [RISKS.md](./RISKS.md) |
| Changelog | [changelog/CHANGELOG.md](./changelog/CHANGELOG.md) |
| Notes | [NOTES.md](./NOTES.md) |
| Working memory | [NOW.md](./NOW.md) |
| Drawing tool | draw.html |
| Simulation | test.html |

---

## 7. Communication Plan

**Check-in frequency:** Session-driven
**Decision log:** [changelog/CHANGELOG.md](./changelog/CHANGELOG.md)

---

*Part of PROJ-002. Follows [sunnyerteit/project-framework](https://github.com/sunnyerteit/project-framework) conventions.*
