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

## AI Usage Disclosure

AI tools were used for limited support during this project, including code suggestions, debugging assistance, and wording refinement.

All key decisions, data processing, and interpretations were performed and validated by the author.


# O-4 Traffic Analysis – ML Methods Stage


## Overview

This stage continues the O-4/TEM traffic analysis using the expanded TeslaMate dataset in `ML_Milestone.ipynb`.

Compared with the EDA stage, the dataset cutoff was extended from **31.03.2026** to **04.05.2026**, increasing the amount of available TeslaMate telemetry for the ML milestone.

The goal is to apply statistical and machine learning methods to identify recurring low-speed patterns on the cleaned O-4 mainline dataset.

---

## Notebook

- `ML_Milestone.ipynb`

---

## Data Source

The data was collected using TeslaMate, a self-hosted telemetry system that records vehicle location, timestamp, and speed over time.

The raw dataset is **not uploaded to this repository** because it contains sensitive location and timestamp information, including personal driving routes. The data is stored locally and can be demonstrated upon request.

---

## What Was Done

- Expanded the dataset cutoff from **31.03.2026** to **04.05.2026**.
- Loaded the expanded TeslaMate GPS telemetry dataset from a local PostgreSQL database.
- Applied a broad spatial filter around the O-4/TEM corridor.
- Estimated the O-4 mainline using nighttime observations with `speed > 80 km/h`.
- Filtered GPS points within a 50 m buffer of the estimated O-4 centerline.
- Assigned travel direction using route-position changes across trip-like groups.
- Created morning peak, midday, and evening peak time-window labels.
- Applied one-way ANOVA to compare mean speeds across time windows.
- Applied a chi-square test to compare slow-point proportions across time windows.
- Applied DBSCAN clustering to low-speed observations.
- Tested `speed < 40`, `speed < 45`, and `speed < 50` thresholds for sensitivity.

---

## Route Filtering Method

The expanded GPS data includes O-4 mainline driving, but also nearby ramps, exits, connector roads, and local-road movement.

To reduce this contamination, nighttime high-speed observations were used to estimate the main O-4 trajectory. These points were used only to estimate the highway geometry, not to remove congestion observations.

After estimating the centerline, all candidate GPS points within a 50 m buffer were kept as the working route dataset. This preserves both high-speed and low-speed observations near the O-4 mainline while removing most non-highway movement.

---

## Direction Splitting

Direction was inferred using projected route position along the data-derived O-4 centerline.

- Increasing route position → **Eastbound**
- Decreasing route position → **Westbound**
- Very small displacement → **Undetermined**

This replaces the earlier time-based direction approximation with a more data-driven method.

---

## Hypothesis Testing

### Test 1: Mean speed across time windows

- **H0:** Mean speed is equal across morning peak, midday, and evening peak.
- **HA:** At least one time window has a different mean speed.

A one-way ANOVA test was used. The result was statistically significant, so the null hypothesis was rejected. This means the data provides evidence that mean speeds are not all equal across the three time windows.

Pairwise t-tests were included as follow-up comparisons to examine which specific time-window pairs differ.

### Test 2: Slow-point proportion across time windows

- **H0:** Time window and slow/not-slow status are independent.
- **HA:** Time window and slow/not-slow status are associated.

A chi-square test of independence was used. The result was statistically significant, so the null hypothesis was rejected. This means the data provides evidence that slow-point frequency is related to time window.

In some outputs, the ANOVA p-value may appear as `0.0`. This should be interpreted as `p < 0.001`, not as an exact probability of zero.

---

## DBSCAN Clustering

DBSCAN was applied to low-speed observations to identify dense spatial regions where slow traffic repeatedly occurs.

The main threshold used was:

`speed < 45 km/h`

The detected clusters are interpreted as candidate congestion zones, not as proven causes of congestion.

The cluster summary includes point count, mean speed, median speed, minimum speed, center coordinates, dominant time window, and dominant travel direction.

---

## Threshold Sensitivity

To avoid relying on a single cutoff, DBSCAN was also tested with:

- `speed < 40 km/h`
- `speed < 45 km/h`
- `speed < 50 km/h`

This checks whether the detected clusters remain reasonably stable under stricter or looser definitions of slow traffic.

---

## Current Scope

This milestone focuses on the cleaned O-4 mainline dataset. Ramp and local-road behavior is intentionally excluded because those points can create artificial congestion clusters.

The final project may refine this further with more detailed route segmentation, endpoint-level map matching, and direction-specific hotspot interpretation.

---

## Reproducibility

To reproduce the ML milestone:

1. Export the TeslaMate database locally.
2. Restore it into PostgreSQL.
3. Update the database name in `ML_Milestone.ipynb` if necessary.
4. Install dependencies with `pip install -r requirements.txt`.
5. Run `ML_Milestone.ipynb`.

---

## Data Availability

The raw telemetry dataset is not uploaded to this repository because it contains sensitive personal location and timestamp information.

The original data is stored locally and can be demonstrated upon request.

Safe summary outputs may be included, but raw trajectory-level exports, SQL database dumps, and private GPS files are excluded through `.gitignore`.

---

## Limitations

- The dataset comes from a single vehicle.
- Route filtering prioritizes O-4 mainline precision and may exclude ramp-related congestion.
- GPS observations are sequential and not fully independent.
- DBSCAN results depend on the selected speed threshold and clustering parameters.
- The current analysis identifies candidate congestion hotspots, but does not prove the exact cause of congestion.

---

## AI Usage Disclosure

AI tools were used for limited support during this stage, including code suggestions, debugging assistance, visualization guidance, and wording refinement.

All outputs were reviewed, modified, and validated before inclusion in the final submission.