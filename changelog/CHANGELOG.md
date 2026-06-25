# Changelog — Longyearbyen City Builder

*An honest record of how this project actually went. Written to be read.*

---

### 2026-06-25 — Full city imported, CLAUDE.md added, road pruning started

**Project:** Longyearbyen City Builder
**Type:** Session
**Status:** 🔄 Partial (road pruning continues next session)

**The humans note**
> N/A

**What happened**
Three things this session. First, wrote `CLAUDE.md` at the project root — a quick-start guide for Claude covering architecture decisions, geodata pipeline, working style, and current sim state. Replaces the need to read six files at the start of every session.

Second, completed TASK-019: full city geodata imported into the sim. OSM Overpass fetch for bbox 78.196,15.43→78.252,15.72 — 777 buildings and 278 roads covering all central neighbourhoods (airport corridor, Sentrum, Lia, Nybyen, Elvesletta). Road IDs are now R001–R278, building IDs B001–B777. Replaces the 37-building hand-traced prototype from Phase 0.

Third, started road pruning. Sunny's direction: roads not connected to the main network should be deleted rather than patched. Built a connectivity analyser (5m snap tolerance, BFS from root road R255 at the airport): 267 roads connected, 11 to delete. Preview page built (`road-connectivity-preview.html`, blue/red). Sunny approved the cut. Then Sunny also flagged roads that don't lead to any buildings — those should go too. That filter (100m building proximity threshold) is built next session before anything is applied to the sim.

**The thinking behind it**
The pruning approach (delete rather than connect) is cleaner than the original TASK-005 plan to detect and stitch intersections. If a road doesn't serve buildings and doesn't connect to the network, it's noise. The two-filter preview-before-apply pattern means Sunny sees the cut before it's permanent — safe to tune the threshold if needed.

---

### 2026-06-24 — Geodata method found: OSM works, 625 buildings baked in

**Project:** Longyearbyen City Builder
**Type:** Session
**Status:** ✅ Complete

**The humans note**
> I dag så fikk vi scannet inn brorparten av bygg o.l., samt veier fra LYB. Alt dette ligger nå klart (dog urensket) i .json-filer.
>
> Er fortsatt veldig fornøyd med hvordan dette går. Jeg tror at det som har gjort øvelsen her bedre enn de andre småprosjektene jeg har lagt ut er at vi har sett på både å bruke prosjektrammeverket som vi tidligere har etablert, samt at vi har vært flinke til å kartlegge brorparten av aktivitetene i prosjektet. Dette sier jo egentlig seg selv: Det er typisk forprosjektet i et prosjekt som gir deg best gevinst senere.

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
