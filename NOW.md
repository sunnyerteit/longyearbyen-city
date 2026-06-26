# NOW

> Claude's working memory between sessions. Not a deliverable — written for Claude, not for humans. Last updated takes priority over everything else here.

---

**Last updated:** 2026-06-26 (end of session)

## What's settled

**Geometry is finalised.** `phases/phase-1/longyearbyen-final.geojson` is the authoritative source going forward.

- 245 roads (from original 278 OSM roads after: Filter 1 disconnected, Filter 2 no-buildings, R100 force-removed, R115 split at T-junction with R121, 9 dead-end tails trimmed)
- 741 buildings (from 777 — 36 removed as orphans with no kept road within 100m, includes Hiorthamn area)
- Phase-0 sim is untouched and frozen.

**Next task: build `phases/phase-1/longyearbyen-sim.html`**
- Copy phase-0 sim as the base
- Replace `const GEOJSON` with the content of `longyearbyen-final.geojson`
- Road routing will need T-junction support (TASK-005): currently nodes are sampled every ~30m along each road independently — junctions where R115b and R121 share a coordinate need to be detected so agents can cross between them. Approach: when building the road graph, check if any sampled node on road A is within SNAP_M of any node on road B (not just endpoints).

**Key geometry facts:**
- R115 was split: R115 first half dropped (no buildings), R115b kept (coords[5..21], shares first coord with R121[0])
- R100 removed by Sunny's request (long road to nowhere, western edge of bbox)
- Hiorthamn area buildings and roads removed cleanly via filters

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
- Fun is a first-class requirement.

**Key technical facts:**
- Main sim file: `phases/phase-0/longyearbyen-sim.html` (single HTML, all-in-one)
- Drawing tool: `phases/phase-0/longyearbyen-draw.html`
- NP Basiskart WMTS tiles: `geodata.npolar.no/.../MapServer/tile/{z}/{y}/{x}`, maxNativeZoom:17
- OSM fallback layer at 30% opacity underneath NP tiles
- Leaflet 1.9.4 for map rendering; SVG overlay for agents
- GeoJSON embedded directly in sim as `const GEOJSON = {...}` on line 148
- Road graph: nodes sampled every ~30m, Dijkstra pathfinding, lazy-cached per access node
- `LIFE_EVENTS = true` gates all evening/night behaviour — flip false to debug
- Agent arrival: walks main road from airport end; `arrivalTime` offset prevents teleporting
- `ROAD_REACH_M = 15000` (flagged for tuning in Beta)
- `CAPACITY = { residential:[8,16,28], commercial:[16,30,50], industrial:[18,34,56] }`
- `UPGRADE_THRESHOLD=70, DOWNGRADE_THRESHOLD=30, STREAK_NEEDED=4`

**Canonical geometry (as of 2026-06-26):**
- Source: `phases/phase-1/longyearbyen-final.geojson` — 245 roads + 741 buildings
- Phase-0 sim frozen at original 278 roads / 777 buildings (unmodified, historical record)
- Phase-1 sim not yet created — next task

**Known issues / deferred:**
- T-junction routing: road graph builder needs to detect shared interior nodes (not just endpoints) so agents can cross between e.g. R115b and R121
- Economy constants need tuning (Phase 2)
- Leaflet may be replaced with custom renderer (TASK-025, Phase 2)

---

## Architecture decisions

- Single HTML file always — modular by internal convention, not file system
- Tick-based simulation — systems communicate only via shared Day State at end of each day
- Map and simulation are fully separated — no Leaflet calls in sim engine, no economy logic in renderer
- Internal section order: World Config → Data Model → Road Network → Simulation Engine → Renderer → UI
- Bake data in — no live API calls in sim or verification pages

## What's Next (start here next session)

- [ ] TASK-005: Open phase-1 sim in browser, confirm agents route via T-junctions (R115b↔R121). T-junction routing should work automatically via coordinate key matching — verify or fix.
- [ ] TASK-028: Zone buildings at city edges, confirm agents route across multiple roads to reach them.
- [ ] TASK-008: Zone a full mix of R/C/I, run 10+ days, verify economy loop is stable.
- [ ] TASK-020: Lot access paths (agents walk to building wall, not lot centre).
- [ ] TASK-024: Scale capacity to polygon area (after economy baseline confirmed).
- [ ] TASK-025: Discuss custom background map — drop Leaflet + NP tiles.
- [ ] TASK-018: Phase 1 spec snapshot (written when all above are done).
