# Changelog — Longyearbyen City Builder

*An honest record of how this project actually went. Written to be read.*

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
