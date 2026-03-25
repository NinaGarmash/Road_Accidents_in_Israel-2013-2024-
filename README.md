# E-Scooter Crash Exploration — Tel Aviv (Phase 3)

**E-Scooter Safety Analytics · Phase 3 of 4**
Part of a longitudinal multi-phase research project on micro-mobility safety in Israel.
Presented at ISTRC Conference, Technion, January 2026.

---

## Live demo

🏆 **[Maven Analytics showcase](https://mavenshowcase.com/project/53880)**

---

## What this report covers

A focused crash exploration of **e-scooters only** in Tel Aviv, using the full CBS microdata from 2013 through 2025. This phase introduced QGIS-based spatial analysis (Tel Aviv quarters and street-level mapping) and produced the foundational data model that Phase 4 builds on.

This repo contains **two sub-stages** captured as git tags:

| Tag | Scope | Data cut | Notes |
|---|---|---|---|
| `v3a` | E-scooters in Tel Aviv vs other micromobility modes and national data | CBS 2013–Jun 2025 | Original ISTRC submission version |
| `v3b` | Same scope, full 2025 calendar year | CBS 2013–Dec 2025 | Updated after year-end data release |

| Attribute | Value |
|---|---|
| **Geography** | Tel Aviv–Yafo |
| **Years** | 2013–2025 (full calendar years) |
| **Total accidents** | 9,769 |
| **Total people involved** | 19,613 |
| **Vehicle type** | E-Scooter (CBS code 21) only |
| **Tool** | Power BI Desktop + QGIS |
| **Data sources** | CBS Israel crash microdata |

---

## Key findings

- **9,769 e-scooter accidents** in Tel Aviv across 2013–2025 — crash volume rose sharply from 486 (2013) to a peak of 886 (2022)
- Crash classification: **583 solo** (6%) · **8,882 two-party** (91%) · **304 multi-party** (3%)
- **Solo crash underreporting**: CBS solo rate (~8–12%) vs Tel Aviv rider survey (~69–77%) — CBS captures only ~5% of real solo crash events
- **Volume ≠ Severity** confirmed at e-scooter level: high-volume years/locations are not the same as high-severity ones
- **QGIS analysis**: crash density mapped at Tel Aviv quarter level (9 quarters) and at individual street level
- **AI visuals**: Key Influencers identifies overturning as a 4.77× severity multiplier

### Crash volume by year

| Year | Accidents | Solo |
|---|---|---|
| 2019 | 787 | 40 |
| 2020 | 799 | 64 |
| 2021 | 842 | 70 |
| 2022 | 886 | 79 |
| 2023 | 740 | 64 |
| 2024 | 845 | 58 |
| 2025 | 796 | 51 |

---

## Data model

The model contains **54 tables** structured with strict prefixes:

| Prefix | Purpose |
|---|---|
| `fact_` | Core CBS data — `fact_Accidents`, `fact_Involved`, `fact_Vehicles` |
| `dim_` | Dimension/lookup tables — calendar, vehicle type, quarters, streets |
| `&` | Calculated bridge/helper tables (party count pipeline, matrix filters) |
| `_Measures` | All DAX measures (single table) |
| `survey_` | Tel Aviv Municipality survey data |
| `enforcement_` | Tel Aviv Police enforcement records |

### Party count pipeline (do not modify)
`&BaseAccidentPartySummary → &MultiPartyIDs → &AccidentMatrixSource → &MatrixAccidentIDs → fact_Accidents[PartyCount]`

---

## Key data corrections applied

These are permanent calculated columns/expressions in the model — do not revert:

| Item | Fix |
|---|---|
| `fact_Involved[Safety Measures Label]` | CBS code for belt (impossible for e-scooters) → recoded to Helmet for vehicle types 15/21/23 · 141 rows corrected |
| `fact_Accidents[PartyCount]` | New column: solo=1, two-party=2, multi-party=3 |
| `&BaseAccidentPartySummary` | Pedestrian count fixed to MIN(1, n) — prevents overcounting |
| `&MatrixAccidentIDs` | Multi-party IDs added; BothDirections cross-filter on 2 relationships |

Full change log: [`docs/raw_data_change_log.md`](docs/raw_data_change_log.md)

---

## Files in this repo

```
├── README.md                                                    ← this file
├── phase_3a/                                                    ← CBS 2013–Jun 2025 (ISTRC submission)
│   ├── E-Scooter Crashes in TA Report (live PBI).pptx          ← Technion ISTRC conference presentation
│   ├── From Data to Safer E-Scooter Rides_abstract_ISTRC.docx  ← conference abstract
│   └── Escooter_demo_Technion.mp4                              ← dashboard demo video (ISTRC Jan 2025)
└── phase_3b/                                                    ← CBS 2013–Dec 2025 (full year update)
```

> **Power BI files (.pbix) are not stored in this repo** due to file size (200+ MB).
> Download links:
> - **Phase 3a** (`escooter_TA_jan6_datadct_presented!.pbix`): [Download](https://1drv.ms/u/c/52d6cdf6c3e9894b/IQC9Nq_ki1O7SqYnUjclpe06AU2I56ePwlaWNwzokUWMFBI?e=F1Ai36)
> - **Phase 3b** (`escooter_TA_upd_0218_noQA.pbix`): [Download](https://1drv.ms/u/c/52d6cdf6c3e9894b/IQDMXQhTmmNISbaw4dqk5CxVAX6SE6758wyWlgIUiIocd-A?e=os5NsL)
>
> Maven Analytics showcase (interactive): [mavenshowcase.com/project/53880](https://mavenshowcase.com/project/53880)
---

## Data sources

**CBS Israel** — Central Bureau of Statistics road accident microdata.
Available at: [data.gov.il](https://data.gov.il/dataset/road_accidents_with_casualties_israel)
Scope: All Tel Aviv–Yafo e-scooter accidents (vehicle_type = 21), 2013–2025.

---

## How this fits into the project

```
Phase 1  →  Phase 2  →  Phase 3a/3b  →  Phase 4
National     Tel Aviv     Tel Aviv         Tel Aviv
all modes    micromobility  e-scooters     full integration
                           (this repo)    + surveys + enforcement
```

← **Phase 2**: [TLV_Micromobility_Phase2](../TLV_Micromobility_Phase2)
→ **Phase 4**: [Escooter_TLV_Safety_Phase4](../Escooter_TLV_Safety_Phase4)

---

## Author

**Nina Garmash, PhD**
ANYWAY Project (DATA FOR CHANGE) · HIT (Holon Institute of Technology)
[LinkedIn](https://www.linkedin.com/in/nina-garmash) · [Portfolio](https://ninagarmash.wixsite.com/data-showcase)

