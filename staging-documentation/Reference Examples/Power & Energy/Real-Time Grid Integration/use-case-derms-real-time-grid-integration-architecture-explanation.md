# Architecture Explanation: DERMS Real-Time Grid Integration

## Use Case Overview

**Use Case:** DERMS Real-Time Grid Integration

**Role of AI:** Optimization, Prediction, Control

**Primary Data Sources:** SCADA, DER controllers (inverters, batteries, EVs), AMI meters, grid topology models, voltage/frequency sensors, weather forecasts, market price signals

**Data Type Produced:** Grid state data, DER dispatch commands, voltage profiles, frequency data, constraint violations, optimization schedules

**End Users:** Distribution control center operators, DER operators, network planners, market operators, regulatory compliance teams

---

## Business Problem

Singapore is experiencing rapid growth in distributed energy resources (DERs):
- **Rooftop solar**: Growing installations across residential and commercial buildings
- **Battery storage**: Grid-scale and behind-the-meter systems
- **Electric vehicles**: Increasing EV adoption with charging infrastructure
- **Smart buildings**: Flexible loads and demand response capability

Without real-time management, these resources create grid challenges:
- **Voltage fluctuations**: Solar generation causing overvoltage on feeders
- **Reverse power flow**: Generation exceeding local demand
- **Frequency deviations**: Sudden changes in DER output
- **Hosting capacity limits**: Inability to connect new DERs
- **Wasted potential**: DERs not participating in grid services

A Distributed Energy Resource Management System (DERMS) provides the visibility, analytics, and control needed to integrate DERs safely while unlocking their value for grid services.

---

## 1. Data Sources to Ingest & Process

### Real-Time Streaming Data

| Data Source | Protocol | Frequency | Business Signal |
|-------------|----------|-----------|-----------------|
| **DER Inverters** | SunSpec Modbus / IEEE 2030.5 | Every 1-5 seconds | Active/reactive power, status, available capacity |
| **Battery Management Systems** | Proprietary APIs / OCPP | Every 1-10 seconds | State of charge, power output, temperature |
| **EV Chargers** | OCPP 2.0 | Every 30 seconds | Charging status, power draw, vehicle connectivity |
| **Smart Meters (AMI)** | DLMS/COSEM → Head-end | Every 15 minutes | Net consumption, voltage, power quality |
| **Distribution SCADA** | IEC 60870-5-104 | Every 2-4 seconds | Feeder loads, capacitor status, regulator taps |
| **Phasor Measurement Units** | IEEE C37.118 | 30-60 samples/second | Voltage angle, frequency, rate of change |
| **Weather Sensors** | REST API | Every 5 minutes | Irradiance, temperature, wind speed, cloud cover |

### Batch/Reference Data

| Data Source | Frequency | Purpose |
|-------------|-----------|---------|
| **Grid Topology Model** | Daily | Electrical connectivity, impedances, ratings |
| **DER Registry** | Daily | Registered DERs, capabilities, connection points |
| **Market Schedules** | Hourly | Day-ahead prices, ancillary service requirements |
| **Constraint Definitions** | Weekly | Voltage limits, thermal ratings, protection settings |
| **Weather Forecasts** | Hourly | Solar irradiance, temperature predictions |
| **Load Forecasts** | Hourly | Expected demand by feeder/zone |

### Data Volume Estimates

| Stream | Volume | Storage Requirement |
|--------|--------|---------------------|
| DER telemetry (10,000 DERs) | 500K events/minute | 50 GB/day |
| AMI data (500,000 meters) | 2M readings/hour | 10 GB/day |
| SCADA (1,000 points) | 50K events/minute | 5 GB/day |
| PMU data (50 units) | 90K samples/minute | 20 GB/day |

---

## 2. Analyze & Transform

### Eventstream Processing

**Eventstream** provides real-time stream processing:

**Stream 1: DER Aggregation**
- Aggregate individual DER readings by feeder, zone, and system-wide
- Calculate total DER generation vs total load
- Compute net exchange at grid interconnection points

**Stream 2: Power Quality Monitoring**
- Monitor voltage at all AMI meters and SCADA points
- Detect voltage violations (> ±5% nominal)
- Calculate voltage unbalance across phases

**Stream 3: Grid State Estimation**
- Combine SCADA, AMI, and DER data
- Estimate power flows on unmonitored segments
- Detect topology changes (switching operations)

**Stream 4: Event Detection**
- Identify sudden DER trips or ramp events
- Detect islanding conditions
- Flag communication failures

### Key Transformations

| Transformation | Input | Output | Window |
|----------------|-------|--------|--------|
| **Feeder Net Load** | DER gen + AMI consumption | Net load per feeder | 1 minute |
| **Voltage Statistics** | AMI voltage readings | Min/max/avg by transformer | 15 minutes |
| **Solar Forecast Deviation** | Actual vs predicted | Forecast error % | Real-time |
| **Hosting Capacity** | Load + DER + limits | Available DER capacity | 5 minutes |
| **Frequency Response** | PMU frequency | Rate of change (RoCoF) | 100ms |

### Eventhouse Configuration

- **Hot partition**: Last 24 hours, optimized for operational queries
- **Warm partition**: 90 days, for trend analysis
- **Materialized views**: 
  - Hourly DER generation by zone
  - Daily peak reverse power by feeder
  - Voltage violation statistics

### Data Factory Integration

**Batch pipelines:**
- Import grid model updates from GIS/network modeling tools
- Load market settlement data for performance verification
- Export DER performance data to regulatory reporting systems

---

## 3. Model & Contextualize

### Digital Twin Builder

**Grid Digital Twin** models the electrical network:

```
Transmission-Distribution Interface
├── Zone Substation A
│   ├── 22kV Feeder 1
│   │   ├── Distribution Transformer DT-101
│   │   │   ├── Solar PV System (50 kW)
│   │   │   ├── Battery (100 kWh)
│   │   │   └── Customer Meters [1-50]
│   │   ├── Distribution Transformer DT-102
│   │   │   ├── EV Charger (22 kW)
│   │   │   └── Customer Meters [51-100]
│   │   └── Capacitor Bank CB-1
│   ├── 22kV Feeder 2
│   │   └── ...
│   └── OLTC Transformer
└── Zone Substation B
    └── ...
```

**Twin Capabilities:**
- Real-time state visualization (power flows, voltages)
- Topology analysis (switching, contingencies)
- Impact simulation (what-if scenarios)
- Constraint tracking (thermal, voltage limits)

### Fabric Graph

**Graph relationships enable:**

| Relationship | Query Example |
|--------------|---------------|
| DER → Transformer → Feeder | "Which DERs affect Feeder 12 voltage?" |
| DER → Customer → Tariff | "What is the value of DER exports by rate class?" |
| Feeder → Protection Zone | "If this relay trips, which DERs must disconnect?" |
| DER → Communication Path | "Which DERs are on the same communication network?" |

**Graph-powered analytics:**
- Hosting capacity propagation
- Fault current contribution analysis
- Islanding detection zones
- DER curtailment priority ranking

---

## 4. Train & Score

### Optimization Models

| Model | Type | Function | Update |
|-------|------|----------|--------|
| **Optimal Power Flow (OPF)** | Convex optimization | Minimize losses while respecting constraints | Every 5 minutes |
| **Volt-VAR Optimization** | Rule-based + ML | Coordinate DER reactive power for voltage | Every 1 minute |
| **DER Dispatch Scheduler** | Mixed-integer LP | Schedule DER for market participation | Every 15 minutes |
| **Contingency Analysis** | Power flow simulation | Assess impact of outages on DER operation | Hourly |

### Predictive Models

| Model | Algorithm | Output | Horizon |
|-------|-----------|--------|---------|
| **Solar Generation Forecast** | Gradient Boosting + NWP | DER solar output by zone | 48 hours |
| **Net Load Forecast** | LSTM neural network | Feeder net load | 24 hours |
| **Voltage Prediction** | Random Forest | Voltage at critical nodes | 4 hours |
| **DER Availability** | Probabilistic (Bayesian) | Expected DER capacity | 24 hours |

### Real-Time Control Algorithms

**Droop-based Volt-VAR Control:**
```
IF voltage > 1.03 pu THEN
    Absorb reactive power (proportional to deviation)
ELSE IF voltage < 0.97 pu THEN
    Inject reactive power (proportional to deviation)
```

**Curtailment Algorithm:**
```
IF reverse power flow > feeder limit THEN
    Calculate required curtailment
    Distribute across DERs based on:
    - Proximity to constraint
    - Fairness (pro-rata or rotation)
    - Contractual priority
    Send setpoints to DER controllers
```

### Model Deployment

- Models run in **Eventhouse** using KQL for scoring
- Complex optimizations run in **Fabric Notebooks** with scheduled triggers
- Control commands published via **Eventstream** to DER management gateways

---

## 5. Visualize & Act

### Real-Time Dashboard

**DERMS Control Center Dashboard:**

| Panel | Content | Update Rate |
|-------|---------|-------------|
| **System Overview** | Total DER generation, net load, frequency | 5 seconds |
| **Geographic Map** | DER locations with status color coding | 30 seconds |
| **Voltage Heatmap** | Distribution network voltage levels | 1 minute |
| **Constraint Monitor** | Active/approaching violations | Real-time |
| **DER Fleet Status** | Online/offline/curtailed counts | 30 seconds |
| **Forecast vs Actual** | Solar/load forecast accuracy | 5 minutes |

**Operational Views:**
- Feeder-level detail with individual DER status
- Market participation status and settlements
- Communication status and data quality

### Power BI Reports

**Strategic Reports:**
- DER hosting capacity utilization trends
- Grid services revenue by DER type
- Voltage quality improvement metrics
- Carbon reduction from DER integration
- Investment deferral value (avoided grid upgrades)

### Activator Automation

| Trigger | Condition | Action |
|---------|-----------|--------|
| **Overvoltage** | V > 1.05 pu for 10 seconds | Auto-dispatch Volt-VAR commands |
| **Reverse Power Limit** | Export > 80% of limit | Warning to operator + pre-stage curtailment |
| **Frequency Event** | f < 49.5 Hz | Activate frequency response from batteries |
| **DER Communication Loss** | No data for 5 minutes | Alert + switch to conservative mode |
| **Forecast Error** | Actual - Forecast > 20% | Trigger re-forecast + adjust reserves |
| **Market Dispatch** | Market signal received | Execute DER schedule |

---

## 6. Get Assisted & Interact

### Copilot Integration

**Natural language for operators:**
- "What is causing the high voltage on Feeder 23?"
- "How much additional solar can connect to Substation B?"
- "Show me all DERs that failed to respond to the last dispatch signal"
- "What would happen if we lost communication with the Woodlands battery system?"
- "Generate a summary of DER performance for this week's regulatory report"

### Data Agents

| Agent | Function | Frequency |
|-------|----------|-----------|
| **DER Performance Monitor** | Daily report on DER availability, response accuracy | Daily 6 AM |
| **Constraint Forecast Agent** | Predict tomorrow's constraint violations | Daily 4 PM |
| **Market Settlement Agent** | Verify DER delivered vs scheduled quantities | Hourly |
| **Connection Impact Agent** | Assess new DER application impact | On request |

### Operations Agents

**Automated Coordination:**
- When new DER connects → Agent validates settings → Updates hosting capacity → Notifies network planning
- When curtailment needed → Agent coordinates across multiple DERs → Ensures fairness → Logs actions for settlement
- When fault detected → Agent checks DER fault ride-through → Verifies reconnection timing → Reports compliance

---

## Architecture Summary

```
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                        DERMS Real-Time Grid Integration Architecture                 │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│  ┌────────────┐  ┌────────────┐  ┌────────────┐  ┌────────────┐  ┌────────────┐    │
│  │ Solar PV   │  │ Batteries  │  │ EV Chargers│  │ Smart Load │  │ AMI Meters │    │
│  │ Inverters  │  │ (BESS)     │  │ (OCPP)     │  │ Controllers│  │ (500K)     │    │
│  └─────┬──────┘  └─────┬──────┘  └─────┬──────┘  └─────┬──────┘  └─────┬──────┘    │
│        │               │               │               │               │           │
│        └───────────────┴───────────────┴───────────────┴───────────────┘           │
│                                        │                                            │
│                      ┌─────────────────▼─────────────────┐                         │
│                      │        DER Aggregation Gateway     │                         │
│                      │     (IEEE 2030.5 / OpenADR)       │                         │
│                      └─────────────────┬─────────────────┘                         │
│                                        │                                            │
│   ┌────────────────┐                   │                   ┌────────────────┐       │
│   │ Distribution   │                   │                   │   Weather &    │       │
│   │    SCADA       │──────┐            │            ┌──────│   Market API   │       │
│   └────────────────┘      │            │            │      └────────────────┘       │
│                           │            │            │                               │
│                      ┌────▼────────────▼────────────▼────┐                         │
│                      │          Azure IoT Hub            │                         │
│                      └─────────────────┬─────────────────┘                         │
│                                        │                                            │
│                      ┌─────────────────▼─────────────────┐                         │
│                      │           Eventstream             │                         │
│                      │  • DER aggregation by zone        │                         │
│                      │  • Voltage monitoring             │                         │
│                      │  • Constraint detection           │                         │
│                      └─────────────────┬─────────────────┘                         │
│                                        │                                            │
│        ┌───────────────┬───────────────┼───────────────┬───────────────┐           │
│        │               │               │               │               │           │
│        ▼               ▼               ▼               ▼               ▼           │
│  ┌───────────┐  ┌───────────┐  ┌───────────────┐  ┌──────────┐  ┌───────────┐     │
│  │Eventhouse │  │ OneLake   │  │ Digital Twin  │  │  Fabric  │  │   ML      │     │
│  │  (KQL)    │◄►│  (Lake)   │  │   Builder     │  │  Graph   │  │  Models   │     │
│  └─────┬─────┘  └─────┬─────┘  └───────────────┘  └──────────┘  └─────┬─────┘     │
│        │              │                                               │            │
│        │              └───────────────────────────────────────────────┘            │
│        │                              │                                            │
│        │                    ┌─────────▼─────────┐                                  │
│        │                    │   OPF / Volt-VAR  │                                  │
│        │                    │   Optimization    │                                  │
│        │                    └─────────┬─────────┘                                  │
│        │                              │                                            │
│        └──────────────────────────────┼────────────────────────────┐              │
│                                       │                            │              │
│                            ┌──────────▼──────────┐      ┌──────────▼──────────┐   │
│                            │   Real-Time DERMS   │      │      Power BI       │   │
│                            │     Dashboard       │      │      Reports        │   │
│                            └──────────┬──────────┘      └─────────────────────┘   │
│                                       │                                           │
│                            ┌──────────▼──────────┐                                │
│                            │      Activator      │                                │
│                            │  • Auto Volt-VAR    │                                │
│                            │  • Curtailment      │                                │
│                            │  • Freq Response    │                                │
│                            └──────────┬──────────┘                                │
│                                       │                                           │
│                            ┌──────────▼──────────┐                                │
│                            │  DER Control        │──► Dispatch Commands to DERs  │
│                            │  Commands           │                                │
│                            └─────────────────────┘                                │
│                                       │                                           │
│                            ┌──────────▼──────────┐                                │
│                            │  Copilot / Agents   │──► Control Room Operators     │
│                            └─────────────────────┘                                │
│                                                                                    │
└────────────────────────────────────────────────────────────────────────────────────┘
```

---

## Business Outcomes

| Outcome | Metric | Expected Improvement |
|---------|--------|---------------------|
| **Increased hosting capacity** | MW of DER connected | 30-50% more DERs without upgrades |
| **Reduced curtailment** | MWh of DER output lost | 50-70% reduction |
| **Voltage quality** | Customers with violations | 80% reduction in violations |
| **Grid services revenue** | Revenue from DER participation | $2-5M new revenue stream |
| **Avoided grid investment** | Deferred infrastructure | $10-30M over 5 years |
| **Renewable integration** | % of load met by DERs | Enable 30%+ DER penetration |

**Estimated Annual Value: $4M - $12M**

---

## Integration with Siemens Systems

| Siemens Component | Integration Point | Data Flow |
|-------------------|-------------------|-----------|
| **Spectrum Power DMS** | Topology, switching states | Bidirectional via API |
| **SICAM A8000** | DER gateway aggregation | DER telemetry in, setpoints out |
| **EnergyIP** | AMI data, voltage readings | Inbound via Data Factory |
| **PSS®SINCAL** | Network model, load flow | Model import for validation |

---

## Implementation Roadmap

### Phase 1 (0-6 months): Visibility
- Integrate DER telemetry from existing solar and battery systems
- Build real-time dashboard with DER generation visibility
- Implement basic voltage monitoring and alerting

### Phase 2 (6-12 months): Analytics
- Deploy solar forecasting models
- Implement hosting capacity calculations
- Build Digital Twin of distribution network

### Phase 3 (12-18 months): Control
- Deploy Volt-VAR optimization
- Implement curtailment algorithms
- Enable market participation for grid services

### Phase 4 (18-24 months): Automation
- Deploy Operations Agents for autonomous operation
- Integrate with wholesale electricity market
- Enable V2G for EV fleet participation
