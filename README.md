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
* **Git & GitHub:** Version control and repository documentation management.

## Data Dictionary

### Operational and Event-Level
* **`run_stops.csv`**: Primary fact-level table recording individual station stops. Tracks arrival/departure delays and distance-from-start metrics for every train run.
* **`run_stop_difficulties.csv`**: Bridge table associating operational incidents/difficulties with specific station stops for granular root-cause reporting.
* **`difficulties.csv`**: Lookup table mapping incident IDs to descriptive delay categories (e.g., equipment failure, infrastructure issues).
* **`service_line_segments.csv`**: Maps train runs to specific physical segments of railway lines to isolate corridor-specific bottlenecks.

### Network and Infrastructure
* **`railway_lines.csv`**: High-level metadata for railway lines (names, start/end points).
* **`line_stations.csv`**: Geographical coordinates and kilometrage of stations along the network.
* **`stations.csv`**: Master dimension table for stations, including passenger volume rankings and geolocation.
* **`platforms.csv`**: Physical characteristics (height, length) of station platforms and tracks.
* **`line_speeds.csv`**: Technical specifications regarding permissible speeds, used to analyze infrastructure constraints.
* **`speed_warnings.csv`**: Active speed restrictions (temporary/permanent) across the network.
* **`track_closures.csv`**: Records of scheduled/unscheduled track closures, enabling correlation between maintenance windows and delay spikes.

### Service and Environmental
* **`train_services.csv`**: Catalog of specific train services, categories, and route endpoints.
* **`train_categories.csv`**: Service classifications (e.g., IC, EIP) for performance segmentation.
* **`occupancies.csv`**: Standardized occupancy level categories.
* **`weather.csv`**: Granular hourly historical meteorological data (temperature, precipitation, wind speed) used to assess weather impacts on network punctuality.

## Visualizations & Dashboard
[Placeholder: Insert screenshots of your Power BI dashboard here]

## Future Development
* **Predictive Modeling:** Incorporating Machine Learning (via Python/R in Power BI) to forecast potential delays based on weather forecasts and historical closure patterns.
* **Real-time Integration:** Transitioning from static CSV ingestion to live API feeds from railway infrastructure providers.
