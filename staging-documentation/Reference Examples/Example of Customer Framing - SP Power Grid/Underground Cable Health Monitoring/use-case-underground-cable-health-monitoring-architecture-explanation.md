# Architecture Explanation: Underground Cable Health Monitoring & Predictive Maintenance

## Use Case Overview

**Use Case:** Underground Cable Health Monitoring & Predictive Maintenance

**Role of AI:** Prediction, Detection, Anomaly Detection

**Primary Data Sources:** Distributed Temperature Sensing (DTS) fiber optic, partial discharge sensors, cable load data, joint temperature sensors, historical fault records, environmental sensors

**Data Type Produced:** Temperature profiles, partial discharge patterns, load curves, insulation resistance metrics, remaining useful life predictions

**End Users:** Asset managers, cable engineers, maintenance planners, operations managers, field crews

---

## Business Problem

Singapore's electricity grid is predominantly underground, presenting unique monitoring and maintenance challenges:

- **Limited visibility**: Unlike overhead lines, underground cables cannot be visually inspected
- **Connectivity constraints**: Sensors in underground conduits face communication challenges
- **High replacement costs**: Underground cable failures are 5-10x more expensive to repair than overhead lines
- **Extended outage duration**: Fault location and repair in underground systems takes significantly longer
- **Thermal limitations**: Underground cables have stricter thermal ratings due to limited heat dissipation

Traditional time-based maintenance approaches result in either premature replacement (wasted investment) or unexpected failures (costly outages). SP PowerGrid needs predictive capabilities to optimize cable asset management.

---

## 1. Data Sources to Ingest & Process

### Real-Time Streaming Data

| Data Source | Protocol | Frequency | Business Signal |
|-------------|----------|-----------|-----------------|
| **Distributed Temperature Sensing (DTS)** | Fiber optic interrogators → OPC-UA | Every 10 minutes | Temperature profile along entire cable length, hot spot detection |
| **Partial Discharge (PD) Sensors** | IEC 61850 / Modbus | Continuous (event-based) | Insulation degradation, joint/termination defects |
| **Cable Load Monitoring** | SCADA / IEC 60870-5-104 | Every 1 minute | Current loading, power factor, load cycles |
| **Joint Temperature Sensors** | LoRaWAN / NB-IoT | Every 5 minutes | Critical joint health, thermal stress |
| **Ambient Conditions** | Environmental sensors | Every 15 minutes | Soil temperature, moisture, ground movement |

### Batch/Reference Data

| Data Source | Frequency | Purpose |
|-------------|-----------|---------|
| **Cable Asset Registry** | Daily | Cable specifications, age, installation details, manufacturer data |
| **Historical Fault Records** | Weekly | Failure patterns, root cause analysis, repair history |
| **Maintenance Records** | Weekly | Inspection results, test records, repair actions |
| **GIS Data** | Monthly | Cable routes, depth, soil conditions, proximity to heat sources |
| **Manufacturer Aging Curves** | Static | Expected degradation rates by cable type and conditions |

### Connectivity Solutions for Underground Infrastructure

```
Underground Sensors → LoRaWAN Gateway (manhole) → Cellular Backhaul → IoT Hub
                   ↓
DTS Fiber → Substation Interrogator → Edge Gateway → IoT Hub
                   ↓
PD Sensors → IEC 61850 Gateway → Azure IoT Edge → IoT Hub
```

---

## 2. Analyze & Transform

### Eventstream Processing

**Eventstream** ingests streaming data from multiple underground sensor networks:

- **Protocol normalization**: Convert diverse protocols (OPC-UA, Modbus, LoRaWAN) to unified schema
- **Data quality filtering**: Remove sensor noise, validate temperature ranges, detect sensor failures
- **Spatial alignment**: Map DTS temperature points to cable segments and asset IDs
- **Windowed aggregations**: Calculate rolling averages, peak temperatures, load cycles per hour
- **Event correlation**: Link PD events to cable sections and environmental conditions

### Key Transformations

| Transformation | Logic | Output |
|----------------|-------|--------|
| **Hot Spot Detection** | Temperature > threshold OR deviation from neighbors > 10°C | Hot spot alerts with location |
| **Load Cycle Counting** | Rainflow counting algorithm on current measurements | Daily load cycles per cable |
| **PD Trend Analysis** | Sliding window statistics on PD magnitude/frequency | PD severity score |
| **Thermal Rating Calculation** | Real-time ampacity based on actual temperatures | Available capacity % |

### Eventhouse Storage

**Eventhouse** provides optimized time-series storage:

- **Partitioning**: By cable circuit and time (daily partitions)
- **Retention**: Raw data 90 days, aggregated data 10 years
- **Materialized views**: Pre-calculated health scores, daily statistics, trend summaries
- **Hot/warm/cold tiering**: Recent data in hot storage for fast queries

### OneLake Integration

- **Bi-directional sync**: Eventhouse ↔ OneLake for ML training and cross-system analytics
- **Parquet export**: Historical data in OneLake for long-term trending
- **Shortcuts**: Connect to existing historian data without migration

---

## 3. Model & Contextualize

### Digital Twin Builder

**Digital Twin Builder** creates a hierarchical model of the underground cable network:

```
Transmission Network
├── Cable Circuit A (132kV)
│   ├── Section 1 (Substation A → Joint Bay 1)
│   │   ├── Cable Segment 1.1
│   │   ├── Joint 1A
│   │   └── Cable Segment 1.2
│   ├── Joint Bay 1
│   │   ├── Joint 1B
│   │   └── Joint 1C
│   └── Section 2 (Joint Bay 1 → Substation B)
│       ├── Cable Segment 2.1
│       └── Termination B
└── Cable Circuit B (66kV)
    └── ...
```

**Twin Properties:**
- Static: Cable type, cross-section, installation year, depth, soil thermal resistivity
- Dynamic: Current temperature, load, PD activity, health score, RUL estimate

### Fabric Graph

**Fabric Graph** enables relationship-based analytics:

- **Spatial relationships**: Cables → Joint Bays → Substations → Feeders
- **Thermal coupling**: Cables sharing the same duct/trench (heat impact)
- **Failure propagation**: Which customers affected by cable failure
- **Maintenance history**: Link cables to past repairs, inspections, tests

**Example Graph Queries:**
- "Which cables share thermal environment with the overheating cable?"
- "What is the total load at risk if Joint Bay 17 fails?"
- "Show all cables installed in the same year with similar loading patterns"

---

## 4. Train & Score

### Machine Learning Models

| Model | Algorithm | Training Data | Output | Update Frequency |
|-------|-----------|---------------|--------|------------------|
| **Remaining Useful Life (RUL)** | Survival analysis (Cox PH) + Deep Learning | Historical failures, sensor trends, age, loading | Days to likely failure | Weekly retrain |
| **Partial Discharge Classification** | CNN on PD patterns | Labeled PD signatures (void, surface, corona) | Defect type + severity | Monthly retrain |
| **Hot Spot Prediction** | LSTM time-series | Temperature history, load forecasts, weather | 24-hour temperature forecast | Daily scoring |
| **Anomaly Detection** | Isolation Forest + Autoencoders | Normal operating patterns | Anomaly score (0-1) | Real-time scoring |

### Model Training Pipeline

```
OneLake (Historical Data) → Fabric Notebooks → Feature Engineering → Model Training
                                                        ↓
                                              Model Registry → Eventhouse (Scoring)
```

### Real-Time Scoring

Models score incoming data in near real-time:
- **Anomaly detection**: Every sensor reading scored for abnormality
- **RUL updates**: Daily recalculation based on latest sensor data
- **Health index**: Composite score combining temperature, PD, load, age factors

---

## 5. Visualize & Act

### Real-Time Dashboard

**Cable Health Operations Dashboard** provides:

| View | Content | Refresh Rate |
|------|---------|--------------|
| **Network Map** | Geographic view with color-coded health status | 1 minute |
| **Hot Spot Alerts** | List of active thermal alerts with location | 15 seconds |
| **RUL Watchlist** | Cables with < 180 days predicted life | 1 hour |
| **PD Activity Monitor** | Active partial discharge events | Real-time |
| **Load vs Capacity** | Current loading relative to thermal limits | 1 minute |

### Power BI Reports

**Strategic Asset Management Reports:**
- Cable fleet health distribution and trends
- Investment prioritization (worst performers)
- Failure rate analytics by cable type, age, manufacturer
- Maintenance effectiveness metrics
- Budget forecasting for cable replacement program

### Activator Triggers

| Trigger | Condition | Action |
|---------|-----------|--------|
| **Critical Hot Spot** | Temperature > 90°C for 15 minutes | SMS to on-call engineer + auto-create incident |
| **PD Threshold Exceeded** | PD magnitude > 500 pC sustained | Email to cable engineer + schedule inspection |
| **RUL Alert** | Predicted life < 90 days | Create maintenance work order in D365 Field Service |
| **Load Approaching Limit** | Current > 85% thermal rating | Alert control room + recommend load transfer |
| **Sensor Failure** | No data for 1 hour | Create sensor maintenance ticket |

---

## 6. Get Assisted & Interact

### Copilot Integration

**Natural language queries for cable engineers:**
- "Which cables are showing degradation trends similar to the one that failed last month?"
- "What is the thermal capacity available on Circuit 127 if we need to transfer load?"
- "Summarize the partial discharge activity on 66kV feeders over the past week"
- "Generate a report of all cables approaching end of life in the Eastern zone"

### Data Agents

| Agent | Function | Trigger |
|-------|----------|---------|
| **Cable Health Reporter** | Daily summary of cable fleet status, new alerts, resolved issues | Scheduled (6 AM) |
| **Incident Investigation Agent** | Correlates sensor data around fault time, identifies precursors | On fault event |
| **Maintenance Optimizer** | Recommends inspection priorities based on risk ranking | Weekly |
| **Capacity Planning Agent** | Identifies cables at risk under load growth scenarios | Monthly |

### Operations Agents

**Automated workflows:**
- When RUL prediction drops below threshold → Agent retrieves cable specifications → Creates draft replacement project → Assigns to asset manager for review
- When hot spot detected → Agent checks nearby cable loads → Recommends load reduction → Coordinates with control room

---

## Architecture Summary

```
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                    Underground Cable Health Monitoring Architecture                  │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│  ┌──────────────┐   ┌──────────────┐   ┌──────────────┐   ┌──────────────┐         │
│  │ DTS Fiber    │   │ PD Sensors   │   │ Load Sensors │   │ Joint Temps  │         │
│  │ Interrogator │   │ IEC 61850    │   │ SCADA        │   │ LoRaWAN      │         │
│  └──────┬───────┘   └──────┬───────┘   └──────┬───────┘   └──────┬───────┘         │
│         │                  │                  │                  │                  │
│         └──────────────────┴────────┬─────────┴──────────────────┘                  │
│                                     │                                               │
│                          ┌──────────▼──────────┐                                    │
│                          │    Azure IoT Hub    │                                    │
│                          └──────────┬──────────┘                                    │
│                                     │                                               │
│                          ┌──────────▼──────────┐                                    │
│                          │    Eventstream      │ ◄── Protocol normalization        │
│                          │    (Processing)     │     Hot spot detection            │
│                          └──────────┬──────────┘     PD event correlation          │
│                                     │                                               │
│         ┌───────────────────────────┼───────────────────────────┐                   │
│         │                           │                           │                   │
│         ▼                           ▼                           ▼                   │
│  ┌─────────────┐          ┌─────────────────┐          ┌───────────────┐            │
│  │ Eventhouse  │◄────────►│    OneLake      │          │ Digital Twin  │            │
│  │ (KQL DB)    │          │   (Data Lake)   │          │   Builder     │            │
│  └──────┬──────┘          └────────┬────────┘          └───────┬───────┘            │
│         │                          │                           │                    │
│         │                          ▼                           ▼                    │
│         │                 ┌─────────────────┐          ┌───────────────┐            │
│         │                 │   ML Models     │          │ Fabric Graph  │            │
│         │                 │ (RUL, Anomaly,  │          │ (Topology)    │            │
│         │                 │  PD Class)      │          └───────────────┘            │
│         │                 └────────┬────────┘                                       │
│         │                          │                                                │
│         └──────────────────────────┼────────────────────────────┐                   │
│                                    │                            │                   │
│                                    ▼                            ▼                   │
│                         ┌──────────────────┐          ┌─────────────────┐           │
│                         │  Real-Time       │          │    Power BI     │           │
│                         │  Dashboard       │          │    Reports      │           │
│                         └────────┬─────────┘          └─────────────────┘           │
│                                  │                                                  │
│                                  ▼                                                  │
│                         ┌──────────────────┐                                        │
│                         │    Activator     │──► Alerts, Work Orders, D365          │
│                         └──────────────────┘                                        │
│                                  │                                                  │
│                                  ▼                                                  │
│                         ┌──────────────────┐                                        │
│                         │ Copilot / Agents │──► Cable Engineers, Asset Managers    │
│                         └──────────────────┘                                        │
│                                                                                     │
└─────────────────────────────────────────────────────────────────────────────────────┘
```

---

## Business Outcomes

| Outcome | Metric | Expected Improvement |
|---------|--------|---------------------|
| **Reduced unplanned outages** | Cable failure rate | 30-50% reduction |
| **Extended cable life** | Average replacement age | 5-10 years extension through optimized loading |
| **Faster fault location** | Mean time to locate | 60-80% reduction |
| **Optimized maintenance** | Inspection efficiency | 40% reduction in routine inspections |
| **Avoided failures** | Prevented incidents | 10-15 critical failures prevented annually |
| **Capacity optimization** | Thermal utilization | 15-20% more capacity from existing cables |

**Estimated Annual Value: $8M - $20M**

---

## Implementation Considerations

### Phase 1 (0-6 months): Foundation
- Deploy DTS on critical 132kV circuits
- Integrate existing SCADA data into Eventstream
- Build basic dashboards and alerting

### Phase 2 (6-12 months): Intelligence
- Add partial discharge monitoring on priority circuits
- Develop and deploy RUL prediction models
- Implement Digital Twin for cable network

### Phase 3 (12-18 months): Optimization
- Expand sensor coverage to 66kV network
- Deploy Copilot and Operations Agents
- Integrate with D365 Field Service for automated work orders

### Connectivity Strategy for Underground
- Leverage existing fiber conduits for DTS and backhaul
- Deploy LoRaWAN gateways at manholes and joint bays
- Use Edge computing at substations for local processing
- Implement store-and-forward for intermittent connectivity
