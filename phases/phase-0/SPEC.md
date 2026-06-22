# Longyearbyen City Builder — Phase 0 Spec Snapshot

**Written:** 2026-06-22
**Covers:** Phase 0 (complete)
**Purpose:** Human-readable description of what the simulation is and does. Written for Sunny and for a new Claude picking this up mid-project.

---

## What this is

A browser-based city builder running on real Longyearbyen geodata. You zone real buildings, watch residents arrive from the airport, find work, shop, and live — all on an accurate map of Longyearbyen, Svalbard. Single HTML file, runs locally in any browser, no server needed.

---

## Files

| File | Description |
|---|---|
| `longyearbyen-sim.html` | The simulation. Everything: map, agents, economy, UI. |
| `longyearbyen-draw.html` | Drawing tool for tracing roads and building footprints on the live map. Outputs GeoJSON. |
| `geodata-phase0.json` | First geodata import. 6 roads (S1–S39) and ~37 building polygons covering the airport corridor down to Sentrum/Elvesletta western fringe. Hand-traced by Sunny in draw.html. |

---

## Map

- Base layer: NP Basiskart WMTS tiles from `geodata.npolar.no` (maxNativeZoom 17)
- Fallback: OpenStreetMap at 30% opacity underneath
- Renderer: Leaflet 1.9.4
- Agent overlay: SVG layer on top of the map

---

## Geodata coverage (Phase 0)

All roads and building polygons in Phase 0 were hand-traced by Sunny in draw.html directly on the NP Basiskart map. This was a proof of concept to validate the pipeline — geodata accuracy and completeness were secondary to getting the system working end-to-end.

- **6 roads:** S1 (main spine, 19 points, airport → Sentrum), S2, S3, S28, S38, S39
- **~37 building polygons:** S4–S44 (S5 is a duplicate of S4; S40 missing)
- **Coverage area:** Airport corridor down to Sentrum and Elvesletta western fringe
- **Known gap:** S2, S3, S28, S38, S39 are not connected to S1 in the routing graph — agents only route along S1 effectively (TASK-005)

---

## Road network & pathfinding

- Road nodes sampled every ~30m along each polyline
- Dijkstra pathfinding between nodes, lazy-cached per access node
- Each building finds its nearest road node as its "access point"
- `ROAD_REACH_M = 15000` — max distance a building can be from a road to participate in the economy (tripled from original 5000, flagged for tuning)
- **Known issue:** Agents walk straight from road node to lot centroid — no driveway path to building wall (TASK-020)

---

## Agents

- New agents arrive daily, walking S1 from the airport end toward their home building
- `arrivalTime` offset prevents teleporting — arrival is animated
- `VIS_CAP = 150` — max visible agents at once (performance cap)
- `LIFE_EVENTS = true` — gates all evening/night behaviour. Set false to debug daytime only.
- Each agent has a daily schedule: arrive → work → shop → home (evening)
- Agent dots rendered as SVG circles on the Leaflet overlay

---

## Economy

- **Three zone types:** residential, commercial, industrial
- **Capacity per zone level:**
  - Residential: 8 / 16 / 28
  - Commercial: 16 / 30 / 50
  - Industrial: 18 / 34 / 56
- **Supply chain:** Industrial produces → Commercial restocks → Residents shop → Satisfaction scored
- **Zone levelling:** `UPGRADE_THRESHOLD = 70`, `DOWNGRADE_THRESHOLD = 30`, `STREAK_NEEDED = 4` (4 consecutive days above/below threshold triggers level change)
- **Known issue:** Economy loop needs end-to-end verification with all three zone types active (TASK-008)

---

## Architecture

- Single HTML file — always. Internal structure by convention.
- Section order: World Config → Data Model → Road Network → Simulation Engine → Renderer → UI
- Tick-based: systems communicate only via shared Day State at end of each day (Day State not yet implemented — TASK-010)
- Map and simulation are separated: no Leaflet calls in sim engine, no economy logic in renderer
- GeoJSON embedded directly as `const GEOJSON = {...}`

---

## What is NOT yet in the simulation

- Sentrum, Lia, Nybyen, Elvesletta geodata (TASK-019)
- Road intersection detection / side road connectivity (TASK-005)
- Building lot access paths / driveways (TASK-020)
- Money system (TASK-011)
- Power system (TASK-012)
- Scoring and game end (TASK-021)
- Architecture refactor / Day State (TASK-009, TASK-010)
- Agent behaviour quality pass (TASK-022)
- Significant tuning required across the board: road network coverage, economy constants, agent behaviour, capacity values, and zone thresholds are all placeholder/first-guess values from the proof of concept phase.
