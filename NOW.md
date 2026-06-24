# NOW

> Claude's working memory between sessions. Not a deliverable — written for Claude, not for humans. Last updated takes priority over everything else here.

---

**Last updated:** 2026-06-24

## ⚠️ First thing next session

- TASK-006 complete — OSM is the geodata method. Verification page at `phases/phase-1/api-test.html` (625 buildings, 263 roads, Sentrum bbox, data embedded).
- TASK-019 is now unblocked — full city import (airport → Nybyen → Lia → Elvesletta), same fetch-once-embed pipeline
- TASK-007 (incremental import) likely moot — TASK-019 will be a single clean replacement of GEOJSON, not incremental
- TASK-025 added: evaluate custom map renderer as Leaflet replacement (Sunny's idea — performance concern)
- TASK-024 added: scale simulation values to polygon area (depends on TASK-019 + TASK-008)
- Raw fetch files in phases/phase-1/ (buildings-raw.json, roads-raw.json) can be deleted — not needed
- Next unblocked tasks: TASK-019 (full city import) or TASK-005 (road intersection fix)

---

## What a new Claude needs to know

**Owner:** Sunny Islam (sunny.islam@snsk.no)
**Project:** Longyearbyen City Builder — browser-based city sim on real Longyearbyen geodata
**Folder:** `C:\Users\sunny\Documents\Sunny\Prosjekter\longyearbyen-city\Longyearbyen City\`
**Framework:** PROJ-002, follows sunnyerteit/project-framework conventions

**How Sunny works:**
- Concise and direct. No unnecessary explanation.
- Pushes back clearly when something is wrong.
- "Do not change the programming" = preserve existing logic literally.
- Approves conversationally — no formal sign-off needed.
- Draws geodata himself (or may switch to automated methods — TBD).
- Uses this project to develop AI skills. Fun is a first-class requirement.

**Key technical facts:**
- Two main files: `test.html` (simulation) and `draw.html` (drawing tool). Both single-file HTML.
- NP Basiskart WMTS tiles: `geodata.npolar.no/.../MapServer/tile/{z}/{y}/{x}`, maxNativeZoom:17
- OSM fallback layer at 30% opacity underneath NP tiles
- Leaflet 1.9.4 for map rendering; SVG overlay for agents
- GeoJSON embedded directly in test.html as `const GEOJSON = {...}`
- Road graph: nodes sampled every ~30m, Dijkstra pathfinding, lazy-cached per access node
- `LIFE_EVENTS = true` gates all evening/night behaviour — flip false to debug
- Agent arrival: walks S1 polyline from airport end; `arrivalTime` offset prevents teleporting
- `ROAD_REACH_M = 15000` (tripled from original 5000 — flagged for tuning in Beta)
- `CAPACITY = { residential:[8,16,28], commercial:[16,30,50], industrial:[18,34,56] }`
- `UPGRADE_THRESHOLD=70, DOWNGRADE_THRESHOLD=30, STREAK_NEEDED=4`

**Current GeoJSON coverage:**
- 6 roads: S1 (main spine, 19 pts, airport→sentrum), S2, S3, S28, S38, S39
- ~37 building polygons: S4–S44 (S5 deduplicated from S4, S40 missing)
- Coverage: airport corridor down to Sentrum/Elvesletta western fringe

**Known issues / deferred:**
- Road branches (S2, S3, S28, S38, S39) not connected to S1 via intersection detection — agents only route along S1 effectively
- Economy constants need tuning (Phase 2)
- Leaflet may be replaced with custom renderer (TBD — do not assume it stays)
- Geodata sourcing method TBD (hand-traced vs automated vs hybrid)

---

## Architecture decisions (2026-06-21)

- Single HTML file always — modular by internal convention, not file system
- Tick-based simulation — systems communicate only via shared Day State at end of each day
- Map and simulation are fully separated — no Leaflet calls in sim engine, no economy logic in renderer
- Internal section order: World Config → Data Model → Road Network → Simulation Engine → Renderer → UI

## What's Next

- [ ] TASK-006: Decide geodata method — Claude builds browser test page, Sunny verifies visually
- [ ] TASK-005: Fix road intersection detection (unblocked, Claude can start now)
- [ ] TASK-007: GeoJSON incremental import workflow (unblocked, needed before TASK-019)
- [ ] TASK-019: Extract + import all central geodata (after TASK-006 decision)
- [ ] TASK-020: Building lot access paths (agents walk to building wall via road)
- [ ] TASK-008: Generate and verify economy loop with mixed zones
- [ ] TASK-018: Write Phase 0+1 spec snapshot (after Phase 1 complete)
- [ ] Return to DECISION-001 (money) and DECISION-002 (power) before TASK-011/012
- [ ] New in Phase 2: TASK-021 (scoring + game end), TASK-022 (agent behaviour quality)
