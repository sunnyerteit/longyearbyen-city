# NOW

> Claude's working memory between sessions. Not a deliverable — written for Claude, not for humans. Last updated takes priority over everything else here.

---

**Last updated:** 2026-06-25

## ⚠️ First thing next session

**Road pruning — mid-task, do this before anything else:**

1. TASK-019 complete — 777 buildings + 278 roads imported into `phases/phase-0/longyearbyen-sim.html`. Full city bbox 78.196,15.43→78.252,15.72. Road IDs are now R001–R278 (not S-prefixed).

2. Road pruning is half-done. Two filters to apply (in order):
   - **Filter 1 — disconnected:** already analysed. 5m snap, BFS from root road R255 (airport). 267 connected, 11 disconnected. Preview at `phases/phase-1/road-connectivity-preview.html` (blue=keep, red=delete).
   - **Filter 2 — no buildings nearby:** NOT YET DONE. Plan: from the 267 kept roads, remove any road where no building centroid is within 100m of any road node. Show as orange in an updated preview before applying. Threshold may need tuning.

3. Once Sunny approves the combined preview, apply both filters to the GEOJSON in the sim (permanently strip removed roads from `const GEOJSON`).

4. TASK-005 (road intersection fix) is effectively being replaced by this pruning approach — roads that don't connect get deleted rather than patched. Update TASKS.md accordingly after pruning is done.

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

**Current GeoJSON coverage (as of 2026-06-25):**
- 278 roads: R001–R278, full city OSM import
- 777 buildings: B001–B777, full city OSM import
- Coverage: full city bbox 78.196,15.43→78.252,15.72 (airport + Sentrum + Lia + Nybyen + Elvesletta)
- Roads not yet pruned — disconnected + building-less roads still in the data

**Known issues / deferred:**
- Road pruning not yet applied to sim (in progress — see First thing next session)
- Economy constants need tuning (Phase 2)
- Leaflet may be replaced with custom renderer (TASK-025, Phase 2)

---

## Architecture decisions

- Single HTML file always — modular by internal convention, not file system
- Tick-based simulation — systems communicate only via shared Day State at end of each day
- Map and simulation are fully separated — no Leaflet calls in sim engine, no economy logic in renderer
- Internal section order: World Config → Data Model → Road Network → Simulation Engine → Renderer → UI
- Bake data in — no live API calls in sim or verification pages

## What's Next

- [ ] Finish road pruning (Filter 2: remove roads with no buildings nearby, then apply both filters to sim)
- [ ] TASK-008: Generate and verify economy loop with mixed zones
- [ ] TASK-020: Building lot access paths (agents walk to building wall via road)
- [ ] TASK-024: Scale simulation values to polygon area (depends on TASK-008)
- [ ] TASK-018: Write Phase 0+1 spec snapshot (after Phase 1 complete)
- [ ] Return to DECISION-001 (money) and DECISION-002 (power) before TASK-011/012
