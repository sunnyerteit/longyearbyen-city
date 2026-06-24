# Changelog — Longyearbyen City Builder

*An honest record of how this project actually went. Written to be read.*

---

### 2026-06-24 — Geodata method found: OSM works, 625 buildings baked in

**Project:** Longyearbyen City Builder
**Type:** Session
**Status:** ✅ Complete

**The humans note**
> *(Sunny — legg inn din norske kommentar her)*

**What happened**
First Claude Code session. The goal was TASK-006a: evaluate whether the NP ArcGIS REST API could replace hand-tracing as the geodata method. It could not — the NP service is tiles-only and doesn't expose feature queries. We pivoted to OSM Overpass, confirmed 625 building footprints and 263 roads for the Sentrum area, fetched it all in one shot, converted Overpass JSON to GeoJSON, and baked it directly into a standalone verification page. No live API calls — open the file, see the data overlaid on the NP map tiles. TASK-006 is complete. OSM is the method.

Two new tasks added: TASK-024 (scale simulation capacity values to actual polygon area once real geodata is in) and TASK-025 (evaluate replacing Leaflet + NP tile streaming with a custom renderer built on our own GeoJSON — Sunny flagged this as a performance concern worth tracking).

**The thinking behind it**
The NP ArcGIS dead end was a useful failure — it took about twenty minutes to discover and rule out, and it pointed us directly at the FKB service layer names that confirmed OSM layer 28 = buildings and 37 = roads were exactly right. The live-fetch approach in the first version of the test page hit Overpass rate limits immediately. Sunny caught the flaw: fetch the data once, embed it, verify from a static file. That turned out to be the correct model for TASK-019 too — not a live API in the sim, just curated GeoJSON as a constant. The pipeline is now proven. Next session starts with either TASK-019 (full city import) or TASK-005 (road intersection fix).

---

### 2026-06-22 — Project foundation complete, planning session done

**Project:** Longyearbyen City Builder
**Type:** Milestone
**Status:** ✅ Complete

**The humans note**
> Nå har vi hatt tre sesjoner: De to første ble utført i 1-2 chatvinduer og målet var bare å lage en enkel prototyp. Det er vel og bra, men jeg mener at dette lille leikeprosjektet med en mini-SimCity er en veldig god måte å få testet prosjektrammeverket mitt.
>
> Jeg føler at jeg (kanskje) har fått schwungen over å få Claude til å sjekke inn med meg. Det må rett og slett defineres per oppgave i prosjektnedbrytningsstrukturen.
>
> Neste bit blir antakeligvis å få laget til et verktøy som kan scanne inn hele byen. Det kan godt hende at det tar flere runder. Jeg gleder meg i alle fall til å få lekt videre med Claude!

**What happened**
Phase 0 is complete. Over two sessions we built a working browser-based city simulation on real Longyearbyen geodata — NP Basiskart map tiles, a hand-drawing tool, a GeoJSON pipeline, Dijkstra pathfinding on hand-traced roads, and a full agent + economy loop running in a single HTML file. Today we structured the project properly: WBS built, tasks reorganised, folder hierarchy established, Phase 0 spec written, and a decision made to move from Cowork to Claude Code for all future work.

**The thinking behind it**
The proof of concept approach was deliberate — get something running on real data before worrying about correctness or scale. That paid off: the pipeline works, the map looks right, and agents visibly move through Longyearbyen. The shift to Claude Code is a practical one — cheaper to run, better file handling, easier GitHub integration from VS Code. The project now has a clean foundation to build Phase 1 from.

---
