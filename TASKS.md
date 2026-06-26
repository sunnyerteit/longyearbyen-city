# Tasks — Longyearbyen City Builder

*Project-specific tasks. See master TASKS.md in sunnyerteit/project-framework for global ID registry.*

---

## 📋 To Do

### Phase 1 — Alpha

#### Documentation

- [ ] TASK-018 — Write Phase 0 + Phase 1 spec snapshot
  - **Goal:** A short human-readable document describing what the simulation does, how it works, and what is currently in it. Easy for a new Claude to pick up from.
  - **Context:** Written at the end of Phase 1 (and again at end of Phase 2). Covers: geodata coverage, road network, agent behaviour, economy loop, known limitations.
  - **Owner:** Claude (draft) → Sunny (review)
  - **Dependencies:** TASK-008 (economy verified — snapshot written after Phase 1 is complete)

#### Geodata

- [x] ~~TASK-006~~ — Completed 2026-06-24. OSM Overpass confirmed.

- [x] ~~TASK-019~~ — Completed 2026-06-25. 777 buildings + 278 roads for full city (bbox 78.196,15.43→78.252,15.72) embedded in sim. Sunny visual check passed.

#### Simulation Port

- [ ] TASK-027 — Port simulation from Phase 0 to Phase 1
  - **Goal:** A working `phases/phase-1/longyearbyen-sim.html` with the full Phase 1 geodata and the Phase 0 simulation engine. Agents move, economy ticks, zoning works.
  - **Context:** Phase 0 sim is frozen. Phase 1 sim starts as a copy of it with `const GEOJSON` replaced by the contents of `longyearbyen-final.geojson` (245 roads, 741 buildings). T-junction routing must be fixed as part of this (see TASK-005 — absorbing it): the road graph builder currently only connects roads at endpoints; it needs to also detect shared interior nodes (e.g. R115b and R121 share a coordinate mid-road). Zoning is reset — needs initial zoning pass before economy can be verified.
  - **Owner:** Claude
  - **Dependencies:** `phases/phase-1/longyearbyen-final.geojson` (done)
  - **Verification:** Open sim in browser, zone a mix of R/C/I buildings across the city, watch agents route from the airport across multiple roads. Sunny confirms it runs without errors.

#### Road Network & Agent Movement

- [x] ~~TASK-007~~ — Moot. TASK-019 replaced the entire GEOJSON block in one pass; incremental append never needed.

- [ ] TASK-005 — Fix road intersection detection (T-junctions)
  - **Goal:** Agents can route across T-junctions where a road branches off an interior node of another road (not just at endpoints).
  - **Context:** Absorbed into TASK-027. R115b and R121 share coord [15.7090165, 78.2174097] as an interior node on R115b and the first endpoint of R121. Original S1/S2/S3 issue is resolved by the pruning — disconnected roads were deleted rather than patched.
  - **Owner:** Claude
  - **Dependencies:** TASK-027
  - **Verification:** Confirm agents can route from a building on R121 through to the airport via the junction with R115b.

- [ ] TASK-020 — Implement building lot access paths (driveway connections)
  - **Goal:** Each building has a visible or invisible path connecting the nearest road to the nearest wall of the lot. Agents walk this path rather than cutting across open ground to the lot centre.
  - **Context:** Currently agents walk from road to lot centre regardless of geometry. This looks wrong and breaks immersion. Path can be invisible (routing only) or visible (rendered as a thin line).
  - **Owner:** Claude
  - **Dependencies:** TASK-005 (roads connected), TASK-019 (full geodata in)
  - **Verification:** Sunny visually confirms agents approach buildings from the road side

#### Simulation

- [ ] TASK-008 — Generate and verify economy loop end-to-end
  - **Goal:** Confirmed working with all three zone types: industrial produces → commercial restocks → residents shop → satisfaction scores correctly. Runs stably for 10+ days.
  - **Context:** Industrial was silently broken earlier. Now fixed, but needs a real mixed-zone test. Renamed from "verify" to "generate" — we will also expand this system later.
  - **Owner:** Sunny (play-test) + Claude (fix issues)
  - **Verification:** Sunny play-tests and confirms before sign-off

- [ ] TASK-024 — Scale simulation values to lot area
  - **Goal:** Inhabitants capacity, goods production rate, and inventory stock are calculated proportionally from the actual building polygon area, not hardcoded tier constants.
  - **Context:** Currently `CAPACITY = {residential:[8,16,28], commercial:[16,30,50], industrial:[18,34,56]}` applies fixed values regardless of building size. Once real geodata is in (TASK-019), polygon area can be computed from coordinates. Large buildings should hold more residents/goods than small ones. Tier levels stay — area scales the base values within each tier.
  - **Owner:** Claude
  - **Dependencies:** TASK-019 (real polygon data), TASK-008 (stable economy baseline before changing constants)
  - **Verification:** Sunny checks that large buildings have proportionally more capacity, and that the economy still runs stably after the change.

- [ ] TASK-025 — Custom background map: discuss and implement
  - **Goal:** Replace Leaflet + NP WMTS tile streaming with a custom-drawn background map using our own GeoJSON. No external tile dependency, fully styleable, offline-capable.
  - **Context:** Leaflet streams NP Basiskart raster tiles on every session — bandwidth cost, visual noise, external dependency. We now have 245 roads and 741 building footprints — enough to draw a clean, stylised base map ourselves (canvas or SVG). Discuss approach with Sunny first (canvas 2D / WebGL / SVG overlay), then implement. Could also be a phased drop-in: custom map underneath, remove Leaflet last.
  - **Owner:** Sunny (approach decision) + Claude (implement)
  - **Dependencies:** TASK-027 (phase-1 sim exists to build on top of)
  - **Verification:** Sunny confirms the map looks good and the sim runs without Leaflet/NP tile calls.

---

### Phase 2 — Beta

- [ ] TASK-009 — Architecture refactor: impose clean module structure on test.html
  - **Goal:** test.html reorganised into World Config → Data Model → Road Network → Simulation Engine → Renderer → UI. No cross-section reach into internals.
  - **Context:** Single HTML file stays. Old file kept as reference — do not delete. New code must be readable and explicitly named. Claude checks in with Sunny throughout.
  - **Owner:** Claude
  - **Dependencies:** TASK-008
  - **Verification:** Sunny reviews structure before sign-off. Claude presents a before/after summary.

- [ ] TASK-010 — Design and implement shared Day State object
  - **Goal:** A single state object written at end of each day tick. All systems read from it; none call each other mid-day.
  - **Context:** Decouples systems. Includes: population, employment rate, supply levels per plot, power balance, money balance. Structure likely stays stable — Claude flags if options arise that affect this.
  - **Owner:** Claude
  - **Dependencies:** TASK-009

- [ ] DECISION-001 — Money system: decide income model and spending
  - **Goal:** Agreed answers to: where does income come from, and what is money spent on?
  - **Context:** Income candidate: satisfaction-weighted daily tax per occupied residential plot. Spending: zoning costs, power upgrades. To be discussed with Sunny before TASK-011.
  - **Owner:** Sunny (decision) + Claude (options)
  - **Dependencies:** TASK-010

- [ ] DECISION-002 — Power supply: decide implementation
  - **Goal:** Agreed design for power system.
  - **Context:** Coal plant at game start produces X MW, costs Y per MW. Player can later invest in solar, wind, or more coal. Consequences of shortage TBD (satisfaction penalty? zone shutdown?).
  - **Owner:** Sunny (decision) + Claude (options)
  - **Dependencies:** TASK-010

- [ ] TASK-011 — Implement money system
  - **Goal:** Zoning costs money. Income from satisfaction-weighted tax on occupied residential plots. Seed budget at start.
  - **Owner:** Claude
  - **Dependencies:** TASK-010, DECISION-001
  - **Verification:** Sunny reviews before sign-off. Discuss and agree before Claude begins implementation.

- [ ] TASK-012 — Implement power supply system
  - **Goal:** Coal plant at start. Consumption per resident/worker. Shortage consequences. Upgrades purchasable (solar, wind, more coal).
  - **Owner:** Claude
  - **Dependencies:** TASK-011, DECISION-002
  - **Verification:** Sunny reviews before sign-off. Discuss and agree before Claude begins implementation.

- [ ] TASK-021 — Implement scoring system and timed game end
  - **Goal:** Player has a score (TBD formula — likely satisfaction + population + economy health). Game has a defined end condition (time limit or target score). High score list in-game (local only for now — hosted version is out of scope but keep in mind for future).
  - **Context:** Adds a win/lose dimension to the simulation. Design to be discussed with Sunny before implementation.
  - **Owner:** Claude (implement) → Sunny (design approval)
  - **Dependencies:** TASK-011, TASK-012
  - **Verification:** Sunny play-tests scoring feel before sign-off.

- [ ] TASK-022 — Agent simulation quality: routing, schedules, believable behaviour
  - **Goal:** Inhabitants move in a believable way — proper daily schedules, realistic routing, visible purpose. World feels alive.
  - **Context:** Currently agent behaviour is basic. This task consolidates all improvements to make agents feel real: route via roads and driveways, follow time-of-day schedules, vary behaviour by zone type.
  - **Owner:** Claude
  - **Dependencies:** TASK-020 (lot access paths), TASK-010 (Day State)
  - **Verification:** Sunny play-tests and confirms agents feel believable.

- [ ] TASK-013 — Economy tuning pass
  - **Goal:** Satisfaction curves, capacity constants, streak thresholds feel balanced and fun.
  - **Context:** ROAD_REACH_M, CAPACITY, thresholds all flagged for tuning. Do after full geodata and systems are in.
  - **Owner:** Sunny (play-test) + Claude (adjust constants)
  - **Dependencies:** TASK-019, TASK-012, TASK-022

- [ ] TASK-023 — Write Phase 2 spec snapshot
  - **Goal:** Updated human-readable document describing the simulation after Beta — what systems exist, how they interact, current limitations.
  - **Owner:** Claude (draft) → Sunny (review)
  - **Dependencies:** TASK-013

---

### Phase 3 — Release Candidate

- [ ] TASK-014 — Performance audit: 30-day run test
  - **Goal:** Simulation runs 30 in-game days without frame rate degradation or memory leak.
  - **Context:** Agent Map grows unboundedly. Verify eviction and trim logic.
  - **Owner:** Claude

- [ ] TASK-015 — UI polish pass
  - **Goal:** Header, inspector, stats panel clean and intentional. No layout bugs at different window sizes.
  - **Owner:** Claude

- [ ] TASK-016 — GeoJSON size review
  - **Goal:** GeoJSON size assessed at full city scale. Lazy-loading or compression plan if >200KB.
  - **Context:** Single-file HTML stays by decision. RISK-001.
  - **Owner:** Claude

- [ ] TASK-017 — First changelog entry written and approved
  - **Goal:** Changelog entry covering full arc from inception through Alpha.
  - **Context:** Claude drafts, Sunny approves, human notes in Norwegian per framework.
  - **Owner:** Claude (draft) → Sunny (approve)

---

## 🔄 In Progress

*(max 3)*

---

## ✅ Done

- [x] TASK-026 — Write CLAUDE.md for project root
  - Completed: 2026-06-25. Quick-start guide for Claude: architecture decisions, geodata pipeline, key constants, current state, working style. Lives at project root.

- [x] TASK-019 — Extract and import all central Longyearbyen geodata
  - Completed: 2026-06-25. 777 buildings + 278 roads, full city bbox (78.196,15.43→78.252,15.72), OSM Overpass, embedded in sim. Sunny visual check passed.

- [x] TASK-007 — GeoJSON incremental import workflow
  - Completed (moot): 2026-06-25. TASK-019 replaced the whole GEOJSON block in one pass — incremental append never needed.

- [x] TASK-006 — Evaluate and decide geodata sourcing method
  - Completed: 2026-06-24. OSM Overpass confirmed. NP ArcGIS tiles-only. Fetch-once-embed pattern proven.

- [x] TASK-000 — Project foundation: map tiles, drawing tool, GeoJSON pipeline, base simulation
  - Completed: 2026-06-21. NP Basiskart tiles, OSM fallback, Leaflet map, draw.html tool, first GeoJSON import (airport corridor + 37 buildings), full simulation loop running.

---

*Next available ID: TASK-028*

---

## 🚫 Removed / Out of Scope

- ~~TASK-001~~ — Replaced by TASK-019 (single automated extraction replaces per-neighbourhood drawing tasks)
- ~~TASK-002~~ — Replaced by TASK-019
- ~~TASK-003~~ — Replaced by TASK-019
- ~~TASK-004~~ — Replaced by TASK-019
- ~~TASK — Polar night / midnight sun~~ — Removed 2026-06-21. Out of scope entirely.
- ~~TASK — Supply ship arrival events~~ — Removed 2026-06-21. Out of scope entirely.
- ~~TASK — Tourism season~~ — Removed 2026-06-21. Out of scope entirely.
- ~~TASK — Snowmobile/pedestrian paths~~ — Removed 2026-06-21. Out of scope entirely.
