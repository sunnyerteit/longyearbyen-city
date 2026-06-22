# Risk Register — Longyearbyen City Builder

*Score = Likelihood × Impact (L=1, M=2, H=3). 🔴 Score ≥ 6 — address immediately. 🟡 Score 3–5 — monitor. 🟢 Score 1–2 — acceptable.*

---

| Risk ID | Description | Likelihood | Impact | Score | RAG | Mitigation | Status |
|---|---|---|---|---|---|---|---|
| RISK-001 | Single-file HTML hits size/performance limits as geodata grows | High | Med | 6 | 🔴 | Monitor file size; plan refactor to multi-file if needed before Beta | Open |
| RISK-002 | Geodata sourcing bottleneck — hand-tracing all neighbourhoods is slow | High | Med | 6 | 🔴 | Evaluate automated/OSM extraction in Alpha; draw.html is fallback | Open |
| RISK-003 | NP Basiskart tiles unavailable or URL changes | Low | High | 3 | 🟡 | OSM fallback already in place; tiles are a public service but not guaranteed | Open |
| RISK-004 | Road routing graph incomplete — side roads disconnected from S1 | High | Med | 6 | 🔴 | Implement intersection detection or manual connection in Alpha (TASK-TBD) | Open |
| RISK-005 | Scope creep into deep simulation features | Med | Med | 4 | 🟡 | Non-goals explicitly listed in charter; revisit per phase | Open |
| RISK-006 | Economy constants poorly balanced — simulation either trivial or stuck | Med | Low | 2 | 🟢 | Tuning reserved for Beta phase; acceptable to be unbalanced in Alpha | Open |
| RISK-007 | Leaflet SVG overlay performance degrades with many agents at full city scale | Med | Med | 4 | 🟡 | VIS_CAP=150 limits rendering; revisit if frame rate drops | Open |

---

*Next available ID: RISK-008*
