# Notes & Open Questions — Longyearbyen City Builder

*Long-term storage for Claude. Open questions, loose thoughts, and issues that don't belong in the charter or task list.*

---

## Architecture Decisions (2026-06-21)

- **File structure:** Single HTML file. Always. Easy to open and share. Internal structure is modular by convention, not by file system.
- **Internal module order:** World Config → Data Model → Road Network → Simulation Engine → Renderer → UI. No section reaches into another's internals.
- **System communication:** Tick-based. Systems do not call each other mid-day. At end of each day, every system writes outputs to a shared Day State object. The next day, all systems read from Day State to compute their behaviour. The renderer animates what Day State says — the player sees a "faux" live simulation built on pre-computed daily state.
- **Map/simulation separation:** The simulation engine never calls Leaflet. The renderer never runs economy logic. They share only through Day State.
- **Geodata:** OSM extraction to be evaluated (TASK-006). Single-file output regardless of source.

---

## Open Decisions

**DECISION-001 — Money system income model**
*Parked: 2026-06-21. Return to before TASK-011.*
- Where does player income come from? Candidate: daily tax from each occupied residential plot, proportional to satisfaction score. Happy residents pay more.
- What does zoning cost? Flat fee per building, or variable by zone type?
- Does money carry over between days or reset?
- Linked to: TASK-011

**DECISION-002 — Power supply implementation**
*Partially decided: 2026-06-22. Return to before TASK-012.*
- Coal plant at game start produces X MW. Player pays a cost per MW consumed.
- After some time, player can invest in additional sources: solar, wind, more coal plants.
- Consequences of shortage TBD — satisfaction penalty and/or zone shutdown.
- Whether coal plant is a placeable building or abstract background resource: still TBD.
- Linked to: TASK-012

---

## Open Questions

- **Geodata sourcing method:** OSM evaluation pending (TASK-006). NP ArcGIS is tile-only. Hand-tracing in draw.html is the current fallback.
- **Map renderer:** Leaflet stays for now. Custom renderer is out of scope unless Leaflet proves limiting.
- **Road intersection detection:** S2, S3, S28, S38, S39 disconnected from S1 — agents only route on S1. Fix is TASK-005.
- **Performance ceiling:** Single-file HTML stays by decision. GeoJSON size at full city scale to be assessed in TASK-016.
- **Lia hillside:** Steep terrain, no elevation data. Trace footprints only, ignore elevation for now.

## Loose Thoughts

- The drawing tool (draw.html) could be made more capable: layers, import/merge, cross-segment snapping.
- Agent dots may become invisible at full city scale — zoom-level-aware sizing worth considering.
- The airport arrival walk along S1 is a nice visual. Keep it.
- "Fun is a first-class requirement" — don't let simulation accuracy kill playability.

---

*Next available note: add as they arise*
