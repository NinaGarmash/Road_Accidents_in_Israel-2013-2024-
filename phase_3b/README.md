# Phase 3b — Model and Data Updates

This document summarizes all changes from Phase 3a to Phase 3b.

Full source: [Phase 3b Model Update Summary (Google Docs)](https://docs.google.com/document/d/1H_1aDwIcxfwuQ1YMFJ3R9Ie5GDlaWYs4imnv3xnI8UQ/edit?usp=sharing)

---

## What changed from Phase 3a

| | Phase 3a | Phase 3b |
|---|---|---|
| **Data cut** | CBS 2013–Jun 2025 | CBS 2013–Dec 2025 (full year) |
| **Spatial integration** | Multi-step QGIS process (external to PBI) | Python script inside Power BI — auto-updates on refresh |
| **Counterpart matrix coverage** | Single-party + two-party (97% of crashes) | Single-party + two-party + **multi-party** (100%) |
| **Model tooling** | Manual Power BI Desktop edits | Rebuilt with [MCP Power BI Modeling Server](https://github.com/jonathanscholtes/PowerBI-MCP-Server) |
| **Measure organization** | Flat (all measures unstructured) | 244 measures in 10 display folders |

---

## Data corrections

### Safety measures recoding
CBS data records "seatbelt" for e-scooter, e-bike, and bicycle riders — physically impossible.
Fixed: for vehicle types **21** (e-scooter), **23** (e-bike), **15** (bicycle), belt codes 1 and 2 are recoded to **Helmet**.
This correction applies to 141 rows and affects all severity and safety device analysis.

### Pedestrian counting
Previously, multiple individuals in a single pedestrian group were counted as separate accident parties, inflating two-party counts.
Fixed: pedestrian contribution capped at **MIN(1, n)** per accident using the `role_category` field.

---

## Multi-party accident coverage

Phase 3a's counterpart matrix covered only **single-party and two-party crashes** (97% of all accidents).

Phase 3b adds a new calculated table identifying **56,236 multi-party accidents** (VehicleCount + PedestrianCount ≥ 3).
The `fact_Accidents[PartyCount]` column now classifies every accident:

| Category | Count |
|---|---|

| Two-party | 545,052 |
| Multi-party | 56,236 |

> Note: these are national CBS totals across all vehicle types, not Tel Aviv e-scooter figures.
> The counterpart matrix is a general analytical tool — applicable to any crash type in the CBS dataset.
> In this report it is filtered to micromobility rows only.

---

## Spatial integration: QGIS → Python in PBI

In Phase 3a, assigning each accident to a Tel Aviv quarter required a manual spatial join in QGIS:
geocoded coordinates → QGIS point-in-polygon → export → load into Power BI.
This was a multi-step external process that had to be repeated manually on each data refresh.

In Phase 3b, the same logic runs as a **Python transformation inside Power BI**:
- Reads accident GPS coordinates directly from the data model
- Performs the point-in-polygon join against Tel Aviv quarter boundaries
- Outputs the quarter assignment as a calculated column — updates automatically on refresh

---

## Measure reorganization

All 244 DAX measures were reorganized from a flat list into **10 display folders**:

| Folder | Contents |
|---|---|
| Accidents | Total counts by party count category |
| Severity | Injury severity distribution |
| People Involved | Involvement counts by role and vehicle type |
| Statistical Analysis | p-values, chi-square tests for counterpart matrix |
| Spatial | Quarter and street aggregations |
| YoY / YTD | Year-over-year and year-to-date comparisons |
| Survey | Tel Aviv Municipality rider survey measures |
| Enforcement | Police enforcement ticket measures |
| Filters / Selectors | Slicer bridge measures |
| Helpers | Internal calculation helpers |

---

## Power BI report download

**[Download escooter_TA_upd_0218_noQA.pbix](https://1drv.ms/u/c/52d6cdf6c3e9894b/IQDMXQhTmmNISbaw4dqk5CxVAX6SE6758wyWlgIUiIocd-A?e=os5NsL)** (~227 MB, OneDrive)

Open with Power BI Desktop (free). The file does not include the Q&A linguistic schema (noQA build).
