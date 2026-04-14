# O-4 Traffic Analysis – EDA Stage

## Overview
This project analyzes vehicle telemetry data collected from TeslaMate to explore traffic behavior on a segment of the O-4 (TEM) highway.

This repository contains the **Exploratory Data Analysis (EDA)** stage of the project, focusing on:
- Data validation
- Spatial filtering
- Temporal analysis
- Preliminary hypothesis testing

---

## Data Source
The data was collected using TeslaMate, a self-hosted telemetry system that records vehicle location and speed over time.

⚠️ The raw dataset is **not included in this repository** because it contains sensitive location and timestamp information (e.g. personal routes).

---

## Methodology
- Extracted GPS and speed data from TeslaMate
- Applied spatial filtering to isolate a representative segment of the O-4 corridor
- Divided data into time windows:
  - Morning Peak (06:00–10:30)
  - Midday (10:30–14:30)
  - Evening Peak (15:00–20:00)
- Visualized spatial speed patterns
- Compared speed distributions across time windows

---

## Hypotheses
- **H1:** Average vehicle speed differs across time windows.
- **H2:** The proportion of low-speed observations (speed < 40 km/h) differs across time windows.

---

## Findings
The analysis shows clear temporal variation in traffic behavior along the selected segment.

Morning traffic appears relatively smooth, with higher average speeds and fewer low-speed observations. Midday exhibits the lowest average speeds and the highest proportion of slow observations, indicating that this segment is most constrained during that period. Evening traffic remains slower than morning, but is less constrained than midday in the current subset.

Interestingly, this result suggests that congestion is not fixed at a single location but instead evolves along the corridor. The primary morning congestion may occur upstream of the selected segment, while as traffic builds up and propagates throughout the day, it becomes more visible within this segment during midday and evening periods.

Additionally, certain locations consistently exhibit lower speeds across all time windows, suggesting the presence of recurring congestion zones.

---

## Limitations
- The analysis is based on a representative subsection of the O-4 corridor rather than the full route.
- Time windows are assigned based on observation timestamps, which may include partial segments of trips.
- Direction was approximated using time-of-day and will be refined in later stages.
- Results should be interpreted as preliminary and may change with full-route analysis.

---

## Reproducibility
To reproduce the analysis:

1. Export TeslaMate data locally
2. Restore it into a PostgreSQL database
3. Update the connection string in the notebook if necessary
4. Run the notebook `EDA.ipynb`

### Data Availability
The dataset used in this project is not included in the repository due to privacy considerations.

The data is available locally and can be demonstrated upon request.

---

## Notes
- Raw telemetry data is excluded to protect personal location privacy
- This stage focuses on EDA only; further analysis and modeling will be performed in later milestones
- 
## AI Usage Disclosure

AI tools were used for limited support during this project, including code suggestions, debugging assistance, and wording refinement.

All key decisions, data processing, and interpretations were performed and validated by the author.