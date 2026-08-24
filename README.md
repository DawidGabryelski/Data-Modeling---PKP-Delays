# PKP Intercity Train Delays Analysis in Power BI

## Executive Summary
This project delivers a comprehensive data modeling and business intelligence solution built in **Power BI**, focusing on the monitoring, analysis, and visualization of train delays and punctuality for **PKP Intercity**. By transforming raw operational transport data into a structured relational star schema, the project uncovers key operational bottlenecks, reliability trends across major Polish rail corridors, and patterns in schedule disruptions.

## Business Objectives
* **Punctuality Tracking:** Monitor core Key Performance Indicators (KPIs) such as On-Time Performance (OTP) and average delay duration across daily, weekly, and monthly timeframes.
* **Bottleneck Identification:** Pinpoint specific railway stations, routes, and connections experiencing the highest frequency and magnitude of delays.
* **Operational Insights:** Correlate delays with temporal factors, route characteristics, and external operating conditions to provide actionable intelligence for transport analysts.
* **Interactive Exploration:** Provide an intuitive analytical interface for stakeholders to slice and dice performance metrics by carrier, train category, and geographic region.

## Technology Stack & Architecture
* **Power BI Desktop:** Core environment for relational data modeling, DAX measure development, and report design.
* **Power Query (M):** ETL pipeline used for data ingestion, cleaning, transformation, and shaping.
* **Data Modeling:** Star schema architecture optimized for high-performance analytical queries and robust filter propagation.

## Data Dictionary

* **`run_stops.csv`**: Primary fact-level table recording individual station stops. Tracks arrival/departure delays and distance-from-start metrics for every train run.
* **`run_stop_difficulties.csv`**: Bridge table associating operational incidents/difficulties with specific station stops for granular root-cause reporting.
* **`difficulties.csv`**: Lookup table mapping incident IDs to descriptive delay categories (e.g., equipment failure, infrastructure issues).
* **`service_line_segments.csv`**: Maps train runs to specific physical segments of railway lines to isolate corridor-specific bottlenecks.
* **`railway_lines.csv`**: High-level metadata for railway lines (names, start/end points).
* **`line_stations.csv`**: Geographical coordinates and kilometrage of stations along the network.
* **`stations.csv`**: Master dimension table for stations, including passenger volume rankings and geolocation.
* **`platforms.csv`**: Physical characteristics (height, length) of station platforms and tracks.
* **`line_speeds.csv`**: Technical specifications regarding permissible speeds, used to analyze infrastructure constraints.
* **`speed_warnings.csv`**: Active speed restrictions (temporary/permanent) across the network.
* **`track_closures.csv`**: Records of scheduled/unscheduled track closures, enabling correlation between maintenance windows and delay spikes.
* **`train_services.csv`**: Catalog of specific train services, categories, and route endpoints.
* **`train_categories.csv`**: Service classifications (e.g., IC, EIP) for performance segmentation.
* **`occupancies.csv`**: Standardized occupancy level categories.
* **`weather.csv`**: Granular hourly historical meteorological data (temperature, precipitation, wind speed) used to assess weather impacts on network punctuality.


## Future Development
* **Predictive Modeling:** Incorporating Machine Learning (via Python/R in Power BI) to forecast potential delays based on weather forecasts and historical closure patterns.
* **Real-time Integration:** Transitioning from static CSV ingestion to live API feeds from railway infrastructure providers.

## Data Modeling

### Fact Tables 

The data model is built around a star schema centered on operational events, service trajectories, and infrastructure constraints. Below is the summary of all core fact tables established within the Power BI data model:

#### 1. `fact_run_stops`
* **Source File:** `run_stops.csv`
* **Description:** Records individual station stops for train runs, filtered to isolate active delay anomalies (excluding zero-delay and null records). It captures arrival and departure delays in minutes alongside cumulative distance.

#### 2. `fact_service_lines`
* **Source File:** `service_line_segments.csv`
* **Description:** Maps specific train services to physical railway line segments. It defines the route order, origin/destination station names, kilometrage bounds, and data confidence levels.

#### 3. `fact_track_closures`
* **Source File:** `track_closures.csv`
* **Description:** Details scheduled and emergency track blockades across the network, including line numbers, location descriptions, work descriptions, and total closure durations.

#### 4. `fact_weather`
* **Source File:** `weather.csv`
* **Description:** Provides hourly historical meteorological conditions (temperature, precipitation, snowfall, wind speed) mapped to capture environmental impacts on schedule reliability.

#### 5. `fact_speed_warnings`
* **Source File:** `speed_warnings.csv`
* **Description:** Tracks active temporary and permanent speed restrictions across specific line segments, identifying operational infrastructure bottlenecks.

#### 6. `fact_train_services`
* **Source File:** `train_services.csv`
* **Description:** Contains records regarding individual train services operated by PKP Intercity, defining operational service IDs, official train numbers, names, category classifications, domestic status flags, and origin/destination station identifiers.

### Dimension Tables Overview

The data model incorporates several dimension tables to provide descriptive context, lookups, and hierarchical groupings for operational filtering and reporting. Below is the complete summary of all core dimension tables established within the Power BI data model:

#### 1. `dim_train_services`
* **Source File:** `train_services.csv`
* **Description:** Contains descriptive attributes for train services, supporting operational categorization, service naming, and official train numbers.

#### 2. `dim_line_stations`
* **Source File:** `line_stations.csv`
* **Description:** Details station locations along specific railway lines, including axis kilometrage, geographic coordinates (latitude and longitude), and renamed station identifiers (`line_station_id`).

#### 3. `dim_train_categories`
* **Source File:** `train_categories.csv`
* **Description:** Contains classification details for train categories. The source data was loaded directly without structural modifications or column exclusions, retaining its original schema while serving as a key descriptive dimension.

#### 4. `dim_difficulties`
* **Source File:** `difficulties.csv`
* **Description:** Contains classification records regarding route difficulties and operational obstructions. The source data was loaded directly without structural modifications, preserving the original structure to serve as a supporting lookup dimension.

#### 5. `dim_stations`
* **Source File:** `stations.csv`
* **Description:** Contains master data for railway stations across the network. The transformation removed unnecessary operational columns (`passenger_volume_rank` and `is_domestic`), retaining core station attributes for geographic and route mapping.

#### 6. `dim_line_summary`
* **Source File:** Derived from `line_stations`
* **Description:** Designed specifically to resolve and avoid many-to-many relationships within the model. It aggregates station data per line, providing the line number, total station count, first and last stations along the route, and their corresponding starting and ending kilometrage bounds.


## Data Model Relationships

The star schema is structured around operational events connected through well-defined relationships linking fact tables to descriptive and temporal dimensions. Most relationships are active many-to-one connections, with specific routing paths configured as inactive to prevent ambiguous calculations in DAX measures. Time-based intelligence is unified across all main operational events and meteorological logs via a centralized `dim_date` table.

| From Table | From Column | To Table | To Column | Status / Cardinality |
| :--- | :--- | :--- | :--- | :--- |
| `fact_train_service` | `category_id` | `dim_train_category` | `train_category_id` | Active (M:1) |
| `fact_train_service` | `start_station_id` | `dim_stations` | `id` | Active (M:1) |
| `fact_train_service` | `end_station_id` | `dim_stations` | `id` | Inactive (M:1) |
| `fact_run_stops` | `difficulty_id` | `dim_difficulties` | `id` | Active (M:1) |
| `fact_run_stops` | `station_id` | `dim_stations` | `id` | Active (M:1) |
| `fact_line_service` | `line_number` | `dim_lines` | `line_number` | Active (M:1) |
| `fact_speed_warnings` | `line_number` | `dim_lines` | `line_number` | Active (M:1) |
| `fact_clousures` | `line_number` | `dim_lines` | `line_number` | Active (M:1) |
| `dim_line_stations` | `line_number` | `dim_lines` | `line_number` | Active (M:1) |
| `fact_train_service` | `date` | `dim_date` | `Date` | Active (M:1) |
| `fact_line_service` | `date` | `dim_date` | `Date` | Active (M:1) |
| `fact_run_stops` | `date` | `dim_date` | `Date` | Active (M:1) |
| `fact_speed_warnings` | `date` | `dim_date` | `Date` | Active (M:1) |
| `fact_weather` | `timestamp_hour` | `dim_date` | `Date` | Active (M:1) |


## Core DAX Measures

The analytical model relies on a set of core DAX measures to aggregate operational events, evaluate service intensity, and track infrastructure constraints. Below is the summary of the implemented measures along with their expressions and formats:

| Measure Name | DAX Expression | Format | Description |
| :--- | :--- | :--- | :--- |
| `total closures` | `DISTINCTCOUNT(fact_clousures[id])` | Integer | Counts total unique track closures across the network. |
| `total line services` | `DISTINCTCOUNT(fact_line_service[service_id])` | Integer | Counts total unique service line operations. |
| `total run difficulties` | `DISTINCTCOUNT(fact_run_stops[id])` | #,0 | Quantifies unique disruptions and difficulties recorded during train runs. |
| `total train services` | `DISTINCTCOUNT(fact_train_service[train_service_id])` | Integer | Counts total unique train services operated by PKP Intercity. |
| `total warnings` | `DISTINCTCOUNT(fact_speed_warnings[id])` | Integer | Counts total unique speed restrictions and warnings issued. |
| `most serviced lines` | `CALCULATE([total line services], FILTER(VALUES(dim_lines[line_number]), [total line services] > 100))` | Integer | Filters and aggregates lines with high traffic volume exceeding 100 service operations. |
| `% of new warnings` | `VAR _allwarnings = COUNTROWS(fact_speed_warnings) `<br>`VAR _newwarning = CALCULATE(COUNTROWS(fact_speed_warnings), fact_speed_warnings[is_new] = TRUE) `<br>`RETURN DIVIDE(_newwarning, _allwarnings)` | Decimal / Percentage | Calculates the proportion of newly flagged speed warnings relative to the total volume using local variables. |

## Project Summary

This project delivers a comprehensive Power BI analysis of operational delays within the PKP Intercity network. The primary objective is to visualize key performance indicators related to service punctuality, infrastructure bottlenecks, and external factors influencing train operations.

### Key Features
* Delay Overview: Visualizes average arrival and departure delays across the network.
* Service Distribution: Analyzes the traffic share by train category (EIC, EIP, IC, TLK), highlighting the operational proportion of InterCity services.
* Infrastructure Impact: Connects delays with track closures, difficulties, and speed warnings.
* Route Analysis: Identifies the most serviced lines to pinpoint high-traffic areas.

---

### Dashboard Preview

Below is a preview of the main report page, providing a high-level overview of the network's performance.

<img width="1117" height="627" alt="Zrzut ekranu 2026-08-19 132040" src="https://github.com/user-attachments/assets/580e995e-8e44-4fd5-948c-35ee957d1da4" />

