# E-Scooter Crash Exploration — Tel Aviv (Phase 3)

**E-Scooter Safety Analytics · Phase 3 of 4**
Part of a longitudinal multi-phase research project on micro-mobility safety in Israel.

Presented at:
- **ISTRC Conference, Technion** — January 2025 (Phase 3a)
- **Tel Aviv Municipality** — twice, as part of ongoing road safety collaboration (Phase 3a)
- **Maven Analytics** — public showcase (Phase 3a)

---

## Live demo

🏆 **[Maven Analytics showcase](https://mavenshowcase.com/project/53880)**

---

## What this report covers

A focused crash exploration of **e-scooters in Tel Aviv**, using the full CBS microdata from 2013 through 2025. The analysis goes beyond crash counts to examine severity patterns, spatial distribution, counterpart dynamics.

This repo contains **two sub-stages**:

| Stage | Data cut | Key additions |
|---|---|
| **3a** | CBS 2013–Jun 2025 | Spatial layers via QGIS; ISTRC + Municipality presentations |
| **3b** | CBS 2013–Dec 2025 | Full year data; spatial integration moved into PBI (Python); multi-party matrix coverage; model rebuilt with MCP tooling |

### Spatial integration (QGIS + Power BI)

Both stages use Tel Aviv's official administrative boundaries:
- **9 Tel Aviv quarters** — crash density aggregated at quarter level, mapped in Power BI
- **Street-level layer** — individual street crash counts, enabling hotspot identification

In **Phase 3a**, the spatial join (assigning a quarter number to each accident based on GPS coordinates) was a separate multi-step process in QGIS, external to Power BI.

In **Phase 3b**, this assignment runs as a **Python script inside Power BI**, so the spatial layer updates automatically whenever the data is refreshed — no manual QGIS steps required.

| Attribute | Value |
|---|---|
| **Geography** | Tel Aviv–Yafo |
| **Years** | 2013–2025 (full calendar years) |
| **Total accidents** | 9,769 |
| **Total people involved** | 19,613 |
| **Vehicle type** | E-Scooter (CBS code 21) |
| **Tools** | Power BI Desktop · QGIS · Python |
| **Data sources** | CBS Israel crash microdata |

---

## Key findings

### Volume and trend
- **9,769 e-scooter accidents** in Tel Aviv across 2013–2025
- Crash volume rose from 486 (2013) to a peak of **886 (2022)**, then declined — 796 in 2025
- The decline after 2022 coincides with tighter regulation of shared e-scooter operators

### Crash classification
- **8,882 two-party** (91%) · **304 multi-party** (3%)
- The dominant counterpart in two-party crashes is **private car** — consistent across years


### Volume ≠ Severity
- High-volume years and streets are **not** the same as high-severity ones
- Severity is driven by collision type, road conditions, and whether safety equipment was worn — not by raw volume
- **Key Influencers (AI visual)**: overturning identified as a **4.77× severity multiplier**

### Spatial findings
- Crash density concentrates in Tel Aviv's central quarters
- Street-level analysis reveals persistent hotspots that are not visible at the city level
- Quarter-level mapping enables targeted infrastructure prioritization

### Safety device findings
- CBS data contains a known coding error: "seatbelt" is recorded for e-scooter riders (physically impossible)
- Corrected: for vehicle types 21 (e-scooter), 23 (e-bike), 15 (bicycle) → belt codes recoded to **Helmet**
- Helmet usage is the key safety variable for micromobility severity analysis

### Counterpart matrix
The report includes a **crash counterpart matrix** — a general analytical tool showing total accident counts, injury severity, and statistical significance (p-values) for every vehicle-type pair in the CBS dataset.

- Designed to be applicable to **any crash type** (not micromobility-specific)
- In this report, filtered to micromobility rows for focused analysis
- **Phase 3a** coverage: single-party + two-party crashes only (97% of all accidents)
- **Phase 3b** coverage: single-party + two-party + **multi-party** (100% — 56,236 multi-party accidents added)

### Scalability
The entire analytical approach — data model structure, spatial integration, counterpart matrix, natural language Q&A layer — is designed to be **vehicle-agnostic and location-portable**. It can be applied to any vehicle type in the CBS dataset and adapted to any Israeli city using the same methodology.

### Crash volume by year

| Year | Accidents |
|---|---|
| 2019 | 787 |
| 2020 | 799 |
| 2021 | 842 |
| 2022 | 886 |
| 2023 | 740 |
| 2024 | 845 |
| 2025 | 796 |

---

## Data model

The model contains **54 tables** structured with strict prefixes:

| Prefix | Purpose |
|---|---|
| `fact_` | Core CBS data — `fact_Accidents`, `fact_Involved`, `fact_Vehicles` |
| `dim_` | Dimension/lookup tables — calendar, vehicle type, quarters, streets |
| `&` | Calculated bridge/helper tables (party count pipeline, matrix filters) |
| `_Measures` | All 244 DAX measures, organized into 10 display folders |
| `survey_` | Tel Aviv Municipality e-scooter rider survey data |
| `enforcement_` | Tel Aviv Police enforcement records |

### Party count pipeline (do not modify)
`&BaseAccidentPartySummary → &MultiPartyIDs → &AccidentMatrixSource → &MatrixAccidentIDs → fact_Accidents[PartyCount]`

### Key data corrections
| Item | Fix |
|---|---|
| `fact_Involved[Safety Measures Label]` | Belt code → Helmet for vehicle types 15/21/23 (141 rows) |
| `fact_Accidents[PartyCount]` | New column: single-party=1, two-party=2, multi-party=3 |
| `&BaseAccidentPartySummary` | Pedestrian count capped at MIN(1, n) — prevents overcounting multiple pedestrians as separate parties |
| `&MatrixAccidentIDs` | Multi-party IDs added; BothDirections cross-filter on 2 relationships |

See [phase_3b/README.md](phase_3b/README.md) for the full list of model changes introduced in Phase 3b.

---

## Files in this repo

```
├── README.md                                                    ← this file
├── phase_3a/                                                    ← CBS 2013–Jun 2025 (ISTRC submission)
│   ├── E-Scooter Crashes in TA Report (live PBI).pptx          ← Technion ISTRC conference presentation
│   ├── From Data to Safer E-Scooter Rides_abstract_ISTRC.docx  ← conference abstract
│   └── Escooter_demo_Technion.mp4                              ← dashboard demo video (ISTRC Jan 2025)
└── phase_3b/                                                    ← CBS 2013–Dec 2025 (full year update)
    └── README.md                                               ← 3a→3b change log
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
