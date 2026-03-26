# From Data to Safer E-Scooter Rides: An Open Safety Dashboard for Tel Aviv

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

## Problem and motivation

In Tel Aviv, e-scooters have rapidly become a dominant micromobility mode. Israel already has two key building blocks for data-driven road safety: rich national accident microdata from the Central Bureau of Statistics (CBS), and ANYWAY's SafetyData platform — an open national tool that visualises police-reported crashes for all vehicle types.

However, for e-scooters in Tel Aviv important gaps remain. SafetyData and similar tools focus on all vehicles and high-level statistics. They are not designed to answer e-scooter-specific questions such as:

- Where, along which streets and quarters, do e-scooter crashes concentrate when normalised by street length or area?
- How do e-scooter patterns compare to e-bikes, bikes, and mobility scooters?
- Which crash scenarios and user groups drive severe injuries, beyond simple exposure?

Stakeholders focused on micromobility (municipal planners, engineers, NGOs, operators, researchers) lack a dedicated open analytical tool that goes beyond descriptive counts and shows trends, patterns, and relative risks. This project fills that gap.

---

## Research objectives

1. Construct a reusable dataset for micromobility accidents in Tel Aviv, derived from CBS tables and enriched with spatial attributes (quarters, streets) and derived variables.
2. Define clear concepts and indicators: what counts as an e-scooter accident; how to classify modes at crash level; how to derive "parties" within a crash; how to measure density and severity rates comparably.
3. Build an open Power BI dashboard that lets any interested user explore where, who, when, and how crashes happen.
4. Analyse volume and severity in parallel for each lens, showing where severity patterns diverge from exposure.
5. Develop new binary interaction matrices for micromobility, covering party-level crash interactions with volume and severity by counterpart.
6. Leverage Power BI AI features (decomposition trees, Key Influencers, Q&A) to help non-experts interrogate the data.
7. Ensure scalability so the approach can be replicated for other Israeli cities and vehicle categories.

---

## Data and methodology

### Data sources

| Source | Tables / layers | Content |
|---|---|---|
| **CBS Israel** | `markers_hebrew` | Accident-level: ID, type, severity, date/time, road characteristics, coordinates |
| **CBS Israel** | `involved_hebrew` | Person-level: injury severity, role, age, gender, vehicle type, safety measures |
| **CBS Israel** | `vehicles_hebrew` | Vehicle-level: type, technical details, damage |
| **OpenDataTLV** | Quarter boundaries | 15-quarter + 9-quarter administrative boundaries |
| **OpenDataTLV** | Street layer | Street geometries and lengths (m), prepared for safety analysis |

Accidents are joined to Tel Aviv quarters via point-in-polygon (QGIS in Phase 3a; Python inside Power BI in Phase 3b) and to streets via street names and coordinates.

> **Note on data quality**: multiple issues in the Tel Aviv street layer (inconsistent naming, code variants) were identified and partially resolved. Open street layers need special preparation before supporting fine-grained safety indicators.

### Key concepts

- **Micromobility vehicle**: e-scooter (CBS code 21), e-bike (23), bike (15), or mobility scooter (22).
- **E-scooter accident**: crash where at least one involved vehicle is an e-scooter.
- **Party**: all persons in one vehicle (or one pedestrian group) collapsed to a single party. Single-party = solo crash; two-party = e.g. e-scooter + car; multi-party = 3 or more parties.
- **Severity rate**: (serious + fatal cases) / (total accidents or persons in group). Shown alongside volume for every analytical lens.
- **Density metrics**: accidents per 100 m of street; accidents per km² of quarter — implemented as DAX measures in Power BI.

### Spatial integration

Both stages use Tel Aviv official administrative boundaries (9 quarters) and the OpenDataTLV street layer.

**Phase 3a**: spatial join (assigning quarter numbers to accident GPS coordinates) was a separate multi-step QGIS process, external to Power BI.

**Phase 3b**: the same logic runs as a **Python script inside Power BI**, updating automatically on each data refresh.

| Attribute | Value |
|---|---|
| **Geography** | Tel Aviv–Yafo |
| **Years** | 2013–2025 (full calendar years in 3b) |
| **Total accidents** | 9,769 |
| **Total people involved** | 19,613 |
| **Vehicle type** | E-Scooter (CBS code 21) |
| **Tools** | Power BI Desktop · QGIS · Python |
| **Data sources** | CBS Israel (3 tables) + OpenDataTLV (spatial layers) |

---

## Key findings (Phase 3a)

> Stable qualitative patterns over 12.5 years of CBS data. Quantitative values will change as data is updated.

### Volume ≠ Severity

Quarters, streets, and crash categories with the **highest number of e-scooter crashes are not always those with the highest severity rates**. Periods with many crashes (e.g. weekday daytime commutes) can have lower severity than less frequent but riskier conditions (e.g. night-time or weekend rides).

This confirms that severity is not proportional to exposure, and supports targeting both where crashes are frequent and where they are most likely to be severe.

### Who is involved

**E-scooter users:**
- Males are both more involved and more severely injured than females (~9.8 % vs 6.8 % severe rate)
- Passengers face the highest risk (~23 % severe), drivers lower (~7.5 %)

**Non-micromobility parties:**
- Pedestrians are the main group suffering serious harm in e-scooter crashes (~12.8 % severe)
- Car drivers/passengers rarely have severe outcomes (~0.5 % / 0 %)
- Females among non-micromobility parties show higher severity than males (4.2 % vs 2.3 %)

**Age:** involvement peaks at 25–29; children (0–14) and seniors (75+) — though fewer — suffer the highest severity, mostly as riders/passengers or pedestrians.

### Where crashes happen

- **Quarter 5**: highest crash density and volume
- **Quarter 11**: relatively low volume but the highest severity rate among quarters
- Top streets by density: **Rothschild, Ha-Melech George, Allenby**
- Severity varies sharply by street: Rothschild ~2.8 % severe vs Allenby ~11.1 %

### When crashes happen

| Dimension | Volume peak | Severity peak |
|---|---|---|
| Monthly | May–August | April (~15 %), November (~14 %) |
| Weekly | Monday (most crashes, lowest severity ~6.6 %) | Saturday (~15.6 %), holidays |
| Daily | 15:00–17:00 (busiest, moderate severity) | Night-time (fewer but more severe) |

### How crashes happen

- **Intersection-related crashes** show higher severity than mid-block crashes, underlining the importance of junction design
- **Without protection**: ~26 % severe — about twice the rate of helmeted riders
- The party-based interaction matrices reveal which counterpart combinations are most frequent and most severe

### Statistical highlights

- E-scooter crash severity rate: **~10 %**, significantly above bicycles (~6 %, p < 0.001)
- Two-proportion z-tests confirm e-scooters have the highest severity among micromobility modes
- **Key Influencers AI visual**: overturning identified as a **4.77× severity multiplier**; passenger role and junction types also flagged as strong drivers of severe outcomes

### Counterpart matrix

The crash counterpart matrix shows total counts, injury severity, and p-values for every vehicle-type pair in the CBS dataset. Designed as a general tool applicable to any crash type; filtered to micromobility rows in this report.

- **Phase 3a** coverage: single-party + two-party crashes (97 % of all accidents)
- **Phase 3b** coverage: single-party + two-party + **multi-party** (100 %)

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

## Implications

- **Targeted infrastructure**: separating volume from severity and using density measures identifies corridors for infrastructure upgrades and specific junctions where design changes can lower severe outcomes.
- **Focused enforcement and education**: time-of-day, day-of-week, and interaction-type patterns indicate where enforcement and education can be most effective.
- **Complementing national tools**: the dashboard extends (not replaces) ANYWAY SafetyData with a deep, mode-specific, city-level layer with party-based matrices, per-100-m measures, and AI-driven pattern detection.
- **Scalability**: built on CBS open data, OpenDataTLV, and freely available tools (Power BI, QGIS, Python). Scalable to other Israeli cities and vehicle categories.
- **Civic data analytics**: demonstrates how volunteer analysts, NGOs, and municipalities can build sophisticated yet transparent tools that complement national platforms.

---

## Data model

The model contains **54 tables** structured with strict prefixes:

| Prefix | Purpose |
|---|---|
| `fact_` | Core CBS data — `fact_Accidents`, `fact_Involved`, `fact_Vehicles` |
| `dim_` | Dimension/lookup tables — calendar, vehicle type, quarters, streets |
| `&` | Calculated bridge/helper tables (party count pipeline, matrix filters) |
| `_Measures` | All 244 DAX measures, organized into 10 display folders |

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
    └── README.md                                               ← 3a→3b changes + Phase 3b conclusions
```

> **Power BI files (.pbix) are not stored in this repo** due to file size (200+ MB).
> - **Phase 3a** (`escooter_TA_jan6_datadct_presented!.pbix`): [Download](https://1drv.ms/u/c/52d6cdf6c3e9894b/IQC9Nq_ki1O7SqYnUjclpe06AU2I56ePwlaWNwzokUWMFBI?e=F1Ai36)
> - **Phase 3b** (`escooter_TA_upd_0218_noQA.pbix`): [Download](https://1drv.ms/u/c/52d6cdf6c3e9894b/IQDMXQhTmmNISbaw4dqk5CxVAX6SE6758wyWlgIUiIocd-A?e=os5NsL)
>
> Maven Analytics showcase (interactive): [mavenshowcase.com/project/53880](https://mavenshowcase.com/project/53880)

---

## How this fits into the project

```
Phase 1  →  Phase 2  →  Phase 3a/3b  →  Phase 4
National     Tel Aviv     Tel Aviv         Tel Aviv
all modes    micromobility  e-scooters     full integration
                           (this repo)    + surveys + enforcement
```

← **Phase 2**: [Phase2-TLV-Micromobility-Crash-Analysis](../Phase2-TLV-Micromobility-Crash-Analysis)
→ **Phase 4**: [Phase4-Escooter-Risks-TelAviv-Multiple-Perspectives](https://github.com/NinaGarmash/Phase4-Escooter-Risks-TelAviv-Multiple-Perspectives)

---

## Author

**Nina Garmash, PhD**
ANYWAY Project (DATA FOR CHANGE) · HIT (Holon Institute of Technology)
[LinkedIn](https://www.linkedin.com/in/nina-garmash) · [Portfolio](https://ninagarmash.wixsite.com/data-showcase)
