# CLAUDE.md — Longyearbyen City Builder

> Read this before touching anything. Then read NOW.md for current session state.

---

## What this is

Browser-based city builder simulation on real Longyearbyen geodata. Single player, no server, runs in a browser tab. Real building footprints and roads from OSM. Dijkstra routing, tick-based economy, agent simulation. Personal project — fun is a first-class requirement.

**Owner:** Sunny Islam (`sunny.islam@snsk.no`)
**Project ID:** PROJ-002 | Framework: `sunnyerteit/project-framework`

---

## Key files

| File | Purpose |
|---|---|
| `phases/phase-0/longyearbyen-sim.html` | Main simulation (the game) |
| `phases/phase-0/longyearbyen-draw.html` | Geodata drawing tool |
| `phases/phase-1/api-test.html` | OSM verification page — 625 buildings, 263 roads, Sentrum, data baked in |
| `phases/phase-1/buildings.geojson` | Sentrum buildings (OSM Overpass, bbox 78.210,15.54,78.245,15.70) |
| `phases/phase-1/roads.geojson` | Sentrum roads (same bbox) |
| `TASKS.md` | Task board — check before starting work |
| `NOW.md` | Claude's working memory between sessions — read second |
| `CHARTER.md` | Goals, phases, milestones |
| `NOTES.md` | Architecture decisions, open questions |
| `RISKS.md` | Risk register |
| `changelog/CHANGELOG.md` | Session log |

---

## Architecture — do not question these

- **Single HTML file always.** Never split into multiple JS/CSS files.
- **Internal section order:** World Config → Data Model → Road Network → Simulation Engine → Renderer → UI. No section reaches into another's internals.
- **Tick-based.** Systems write to shared Day State at end of each day. Systems read from it at start of next. No mid-day cross-calls.
- **Map and simulation are fully separated.** Sim engine never calls Leaflet. Renderer never runs economy logic.
- **Bake data in, never fetch at runtime.** Geodata lives as `const GEOJSON = {...}` embedded in HTML. No live API calls in the sim or verification pages.

---

## Geodata pipeline

**Method:** OSM Overpass (confirmed 2026-06-24, TASK-006 complete).

**Fetch pattern:**
```bash
curl -H "User-Agent: LongyearbyenCityBuilder/1.0" \
  --data-urlencode "data=[out:json]; ... " \
  https://overpass-api.de/api/interpreter -o output.json
```
Then convert Overpass JSON → GeoJSON in PowerShell, embed as constant in HTML.

**Gotchas:**
- PowerShell gets 406 from Overpass (User-Agent blocked). Always use Bash curl.
- WebFetch tool also works for one-off verification.
- `buildings-raw.json` / `roads-raw.json` in phase-1 are raw fetch outputs — can be deleted.

---

## Key technical constants

```
NP Basiskart tiles: geodata.npolar.no/.../MapServer/tile/{z}/{y}/{x}  maxNativeZoom:17
Leaflet 1.9.4, SVG overlay for agents
ROAD_REACH_M = 15000  (flagged for tuning in Beta)
CAPACITY = { residential:[8,16,28], commercial:[16,30,50], industrial:[18,34,56] }
UPGRADE_THRESHOLD=70, DOWNGRADE_THRESHOLD=30, STREAK_NEEDED=4
LIFE_EVENTS = true  (set false to debug evening/night behaviour)
VIS_CAP = 150  (agent rendering cap)
```

Road graph: nodes sampled every ~30m, Dijkstra pathfinding, lazy-cached per access node.

Agent arrival: walks S1 polyline from airport end, `arrivalTime` offset prevents teleporting.

---

## Current state (as of 2026-06-24)

**Phase 1 — Alpha** is active.

GeoJSON coverage in sim: 6 roads (S1–S3, S28, S38, S39), ~37 building polygons (airport corridor → Sentrum fringe).

**Known issues:**
- S2, S3, S28, S38, S39 are disconnected from S1 — agents only route along S1 effectively (TASK-005)
- Economy constants untuned (acceptable until Beta)
- Leaflet may be replaced with custom renderer (TASK-025 — do not assume it stays)

**Next unblocked tasks:** TASK-019 (full city import, all neighbourhoods) or TASK-005 (road intersection fix).

---

## How Sunny works

- Concise and direct. Match his register.
- Approves conversationally — no formal sign-off needed.
- Pushes back clearly and fast when something is wrong. Trust that signal.
- Give a one-line plan, then build. Don't ask again unless genuinely blocked.
- When he says "push," include all untracked files (check `git status` first).
- "Do not change the programming" = preserve existing logic literally.
- Fun is a first-class requirement — don't let simulation accuracy kill playability.

---

## Before starting any task

1. Read `NOW.md` — it has the most recent session state
2. Check `TASKS.md` — confirm task is unblocked
3. Check `NOTES.md` for relevant architecture decisions
4. If touching geodata: confirm you're using the fetch-once-embed pipeline, not live API calls
