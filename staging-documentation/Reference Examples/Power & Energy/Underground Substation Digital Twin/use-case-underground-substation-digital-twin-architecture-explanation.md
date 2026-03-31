# Architecture Explanation: Underground Substation Digital Twin

## Use Case Overview

**Use Case:** Underground Substation Digital Twin

**Role of AI:** Prediction, Optimization, Simulation

**Primary Data Sources:** Transformer sensors (DGA, temperature, load), switchgear status, protection relay data, environmental sensors, SCADA, maintenance records

**Data Type Produced:** Equipment health scores, thermal models, failure probabilities, maintenance recommendations, operational simulations

**End Users:** Substation engineers, asset managers, maintenance crews, operations managers, reliability engineers

---

## Business Problem

Singapore's underground substations present unique operational challenges:

**Physical Access Constraints:**
- Located in underground chambers, basements, or enclosed spaces
- Limited ventilation affecting equipment thermal performance
- Difficult and time-consuming physical inspections
- Safety considerations for confined space entry
- No visual indicators of equipment condition from outside

**Asset Management Challenges:**
- Transformers are critical, expensive assets ($2-5M replacement cost)
- Long lead times for replacement (12-18 months)
- Aging infrastructure with unknown remaining life
- Reactive maintenance leads to unplanned outages
- Over-maintenance wastes resources; under-maintenance risks failures

**Operational Visibility Gaps:**
- Limited real-time monitoring beyond basic SCADA
- No integrated view of equipment health
- Siloed data across multiple systems
- Difficult to simulate operational scenarios

A Digital Twin provides continuous virtual representation of underground substations, enabling remote monitoring, predictive maintenance, and operational optimization without physical access.

---

## 1. Data Sources to Ingest & Process

### Real-Time Streaming Data

| Data Source | Sensor/System | Protocol | Frequency | Business Signal |
|-------------|---------------|----------|-----------|-----------------|
| **Dissolved Gas Analysis (DGA)** | Online DGA monitor | Modbus / IEC 61850 | Every 4 hours | Transformer insulation health, fault gases |
| **Transformer Temperature** | Winding/oil temperature sensors | IEC 60870-5-104 | Every 1 minute | Thermal stress, loading capacity |
| **Load Current** | CT measurements via SCADA | IEC 60870-5-104 | Every 4 seconds | Transformer loading, overload risk |
| **Bushing Capacitance** | Online bushing monitors | Modbus | Every 1 hour | Bushing insulation condition |
| **Switchgear Status** | Position indicators, SF6 pressure | IEC 61850 GOOSE | Event-based | Switching operations, gas leaks |
| **Protection Relay** | Relay events, fault records | IEC 61850 MMS | Event-based | Fault events, protection operations |
| **Partial Discharge** | UHF/acoustic PD sensors | Proprietary | Continuous | Insulation defects, corona |
| **Environmental** | Temperature, humidity, flood sensors | LoRaWAN | Every 5 minutes | Ambient conditions, water ingress |
| **Battery Systems** | DC system voltage, impedance | Modbus | Every 15 minutes | Backup power health |

### Batch/Reference Data

| Data Source | Frequency | Purpose |
|-------------|-----------|---------|
| **Asset Registry** | Daily | Equipment specifications, ratings, age, manufacturer |
| **Maintenance Records** | Weekly | Work orders, inspections, test results, repairs |
| **Nameplate Data** | Static | Design parameters, thermal curves, limits |
| **Oil Test Results** | Monthly | Lab analysis of oil samples |
| **Thermography Reports** | Quarterly | Infrared inspection findings |
| **Protection Settings** | On change | Relay configurations, coordination studies |
| **Single Line Diagrams** | On change | Electrical topology, connectivity |

### Underground-Specific Sensors

| Challenge | Sensor Solution | Installation Location |
|-----------|-----------------|----------------------|
| Limited ventilation | Ambient temperature array | Multiple chamber points |
| Confined space hazards | Gas detection (O2, H2S, CO) | Entry points, cable basement |
| Water ingress risk | Flood/moisture sensors | Sump, cable entries, floor level |
| No visual inspection | PTZ cameras with analytics | Key equipment views |
| Vibration monitoring | Accelerometers | Transformer tank, cooling fans |

---

## 2. Analyze & Transform

### Eventstream Processing

**Stream 1: Transformer Telemetry**
```yaml
Input: Temperature, load, DGA, PD measurements
Transform:
  - Calculate top oil temperature rise
  - Compute hot spot temperature (IEEE/IEC thermal model)
  - Derive loading percentage vs rating
  - Trend DGA gas ratios (Duval triangle, Rogers ratios)
  - Aggregate PD activity statistics
Output: Enriched transformer state to Eventhouse
```

**Stream 2: Equipment Events**
```yaml
Input: Protection trips, switching operations, alarms
Transform:
  - Correlate related events (trip → breaker open → alarm)
  - Classify event severity
  - Link to affected equipment
  - Calculate event sequences and timing
Output: Event chains to Eventhouse + Activator
```

**Stream 3: Environmental Monitoring**
```yaml
Input: Chamber temperature, humidity, gas levels, water sensors
Transform:
  - Compare to safe operating limits
  - Detect anomalous patterns
  - Calculate ventilation effectiveness
  - Identify trends toward unsafe conditions
Output: Environmental status to Digital Twin
```

### Thermal Modeling

**Real-time thermal calculations:**
```
Hot Spot Temperature = 
  Top Oil Temp + (Hot Spot Rise × Load Factor^exponent)

where:
  Hot Spot Rise = f(winding design, cooling type)
  Load Factor = Actual Load / Rated Load
  exponent = 1.6 (ONAN), 1.3 (ONAF), 2.0 (OFAF)
```

**Loss of Life Calculation:**
```
Aging Rate = exp((15000/383) - (15000/(θh + 273)))
where θh = hot spot temperature (°C)

Daily Loss of Life = ∫(Aging Rate × dt) over 24 hours
```

### Eventhouse Configuration

| Table | Retention | Purpose |
|-------|-----------|---------|
| `transformer_telemetry` | 90 days hot, 10 years warm | Operational data |
| `dga_readings` | 10 years | Long-term oil health trending |
| `protection_events` | 10 years | Fault history, reliability analysis |
| `environmental_data` | 1 year | Chamber conditions |
| `health_scores` | 10 years | Equipment health history |

### OneLake Integration

- **Model training data**: Historical failure patterns, maintenance outcomes
- **Regulatory reporting**: Equipment test history, compliance records
- **Cross-asset analytics**: Fleet-wide comparisons, aging curves

---

## 3. Model & Contextualize

### Digital Twin Builder

**Substation Digital Twin Hierarchy:**
```
Underground Substation US-001
├── 66kV Switchgear Room
│   ├── 66kV Switchboard SB-01
│   │   ├── Incomer CB-01 (Circuit Breaker)
│   │   │   ├── Current Transformers (3x)
│   │   │   ├── Voltage Transformers (3x)
│   │   │   └── Protection Relay PR-01
│   │   ├── Bus Section CB-02
│   │   └── Outgoing Feeders (CB-03 to CB-08)
│   └── 66kV Switchboard SB-02
│       └── ...
├── Transformer Chamber
│   ├── Power Transformer TX-01 (66/22kV, 40MVA)
│   │   ├── Main Tank
│   │   │   ├── Windings (HV, LV, Tertiary)
│   │   │   ├── Core
│   │   │   ├── Oil System
│   │   │   └── Bushings (HV: 3x, LV: 3x)
│   │   ├── Cooling System
│   │   │   ├── Radiators/Coolers
│   │   │   └── Fans/Pumps
│   │   ├── Tap Changer (OLTC)
│   │   └── Monitoring Equipment
│   │       ├── DGA Monitor
│   │       ├── Temperature Sensors
│   │       └── Bushing Monitors
│   └── Power Transformer TX-02
│       └── ...
├── 22kV Switchgear Room
│   └── 22kV Switchboards (SB-03 to SB-06)
├── Control Room
│   ├── Protection Panels
│   ├── SCADA RTU
│   └── DC System (Batteries, Charger)
└── Auxiliary Systems
    ├── Ventilation/HVAC
    ├── Fire Suppression
    ├── Lighting
    └── Sump Pumps
```

**Twin Properties per Asset:**

| Asset | Static Properties | Dynamic Properties |
|-------|-------------------|-------------------|
| **Transformer** | Rating, impedance, cooling type, year, manufacturer | Load %, temperature, DGA levels, health score |
| **Circuit Breaker** | Rating, type, interrupting capacity | Operations count, SF6 pressure, timing |
| **Protection Relay** | Settings, firmware version | Last trip, fault records |
| **Chamber** | Volume, ventilation capacity | Temperature, humidity, gas levels |

### Fabric Graph

**Relationship Modeling:**
```
Transformer ──[cooled_by]──► Cooling_System
Transformer ──[protected_by]──► Protection_Relay
Transformer ──[supplied_by]──► Incomer_Breaker
Transformer ──[feeds]──► 22kV_Switchboard
Transformer ──[located_in]──► Chamber
Chamber ──[ventilated_by]──► HVAC_System
Breaker ──[controls]──► Transformer
Relay ──[trips]──► Breaker
```

**Graph-Enabled Analytics:**
- "If TX-01 fails, which feeders lose supply?"
- "What equipment shares the same chamber (thermal interaction)?"
- "Which transformers depend on the same ventilation system?"
- "Show all equipment due for maintenance in this substation"

---

## 4. Train & Score

### Health Index Calculation

**Transformer Health Index (HI):**
```
HI = Σ(Wi × Si) / Σ(Wi)

where:
  Wi = Weight for factor i
  Si = Score for factor i (0-100)
  
Factors:
  - DGA analysis (W=25): Duval triangle, key gas ratios
  - Oil quality (W=15): Moisture, acidity, dielectric strength
  - Age (W=10): Years in service vs expected life
  - Loading history (W=15): Cumulative overload stress
  - Thermal performance (W=15): Hot spot history, cooling effectiveness
  - Electrical tests (W=10): Insulation resistance, tan delta, FRA
  - Visual/physical (W=10): Oil leaks, corrosion, noise
```

### Machine Learning Models

| Model | Algorithm | Training Data | Output | Update |
|-------|-----------|---------------|--------|--------|
| **Remaining Useful Life (RUL)** | Survival analysis + LSTM | Historical failures, condition data | Days to recommended action | Weekly |
| **DGA Fault Classification** | Random Forest | Labeled DGA patterns | Fault type (thermal, electrical, PD) | Monthly |
| **Thermal Anomaly Detection** | Isolation Forest | Normal thermal profiles | Anomaly score (0-1) | Real-time |
| **Cooling System Degradation** | Regression | Cooling performance history | Efficiency % | Daily |
| **Failure Probability** | Logistic Regression | Fleet failure data | 30/90/365-day failure probability | Weekly |

### Simulation Capabilities

**What-If Scenarios:**
- "What happens to transformer temperature if ambient rises 5°C?"
- "Can we load TX-01 to 120% for 4 hours given current conditions?"
- "If cooling pump fails, how long before thermal limits exceeded?"
- "Impact of planned maintenance on substation capacity?"

**Digital Twin Simulation Engine:**
```
Input: Current state + scenario parameters
Process: 
  - Run thermal model forward in time
  - Apply load profile or contingency
  - Calculate equipment response
Output: Predicted temperatures, limits, alarms
```

---

## 5. Visualize & Act

### Real-Time Dashboard

**Substation Overview Dashboard:**

| Panel | Content | Update Rate |
|-------|---------|-------------|
| **3D Substation View** | Interactive model with live status colors | 30 seconds |
| **Transformer Health** | Health index gauges for all transformers | 5 minutes |
| **Temperature Trend** | Hot spot, top oil, ambient temperatures | 1 minute |
| **DGA Status** | Gas concentrations with trend arrows | 4 hours |
| **Loading Profile** | Current load vs rating, 24-hour profile | 1 minute |
| **Environmental** | Chamber conditions, ventilation status | 5 minutes |
| **Active Alarms** | Current alarms with equipment linkage | Real-time |
| **Maintenance Due** | Upcoming scheduled maintenance items | Daily |

**Transformer Deep-Dive View:**
- Detailed thermal profile (winding diagram with temperatures)
- DGA trend charts with interpretation
- Loss of life accumulation
- Switching operations history
- Protection event timeline

### Power BI Reports

**Asset Management Reports:**
- Fleet health score distribution
- Aging analysis and replacement planning
- Maintenance effectiveness (planned vs unplanned)
- Chamber environmental compliance
- Reliability statistics by substation

**Financial Reports:**
- Maintenance cost by asset
- Avoided failure value (prevented outages)
- Life extension achieved through monitoring
- Investment prioritization ranking

### Activator Automation

| Trigger | Condition | Action |
|---------|-----------|--------|
| **DGA Alert** | Key gas > threshold or rapid rise | Email to transformer specialist, create investigation task |
| **Thermal Warning** | Hot spot > 110°C | Alert control room, recommend load reduction |
| **Critical Thermal** | Hot spot > 130°C | SMS to on-call, auto-notify control room to reduce load |
| **Environmental Alarm** | Chamber temp > 45°C or water detected | Alert facilities, create emergency work order |
| **Health Score Drop** | HI drops > 10 points in 30 days | Notify asset manager, schedule inspection |
| **Predicted Failure** | 30-day failure probability > 20% | Escalate to engineering, plan contingency |
| **Maintenance Due** | Days to due < 14 | Create work order in D365 Field Service |

---

## 6. Get Assisted & Interact

### Copilot Integration

**Natural Language Queries:**
- "What is the current health status of all transformers at Substation US-001?"
- "Show me the DGA trend for TX-01 over the past 6 months"
- "Which transformers are approaching thermal limits today?"
- "Compare the loading patterns of TX-01 and TX-02"
- "Generate a condition assessment report for TX-03"
- "What would be the impact if we increase load on TX-01 by 20%?"

**AI-Assisted Analysis:**
- Automatic interpretation of DGA results with fault diagnosis
- Natural language explanation of health score changes
- Recommendations for maintenance actions
- Anomaly investigation assistance

### Data Agents

| Agent | Function | Trigger |
|-------|----------|---------|
| **Daily Health Report Agent** | Generate fleet health summary with changes | Daily 6 AM |
| **DGA Interpretation Agent** | Analyze new DGA readings, flag concerns | On new DGA data |
| **Thermal Advisory Agent** | Predict thermal stress for next 24 hours | Every 6 hours |
| **Maintenance Scheduler Agent** | Optimize maintenance schedule across fleet | Weekly |
| **Inspection Report Agent** | Pre-populate inspection forms with current data | On request |

### Operations Agents

**Automated Workflows:**
- When health score drops → Agent retrieves historical data → Compares to similar transformers → Recommends investigation scope → Assigns to engineer
- When DGA shows concerning trend → Agent checks loading history → Correlates with thermal data → Generates diagnostic report → Schedules follow-up test
- When maintenance due → Agent checks equipment criticality → Considers loading forecast → Proposes optimal timing → Creates work order draft

---

## Architecture Summary

```
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                    Underground Substation Digital Twin Architecture                  │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│  UNDERGROUND SUBSTATION                                                             │
│  ┌─────────────────────────────────────────────────────────────────────────────┐   │
│  │                                                                             │   │
│  │  ┌───────────┐  ┌───────────┐  ┌───────────┐  ┌───────────┐               │   │
│  │  │ DGA       │  │ Temp      │  │ Protection│  │ Chamber   │               │   │
│  │  │ Monitor   │  │ Sensors   │  │ Relays    │  │ Sensors   │               │   │
│  │  └─────┬─────┘  └─────┬─────┘  └─────┬─────┘  └─────┬─────┘               │   │
│  │        │              │              │              │                      │   │
│  │  ┌─────┴──────────────┴──────────────┴──────────────┴─────┐               │   │
│  │  │                    SICAM RTU / Gateway                  │               │   │
│  │  │              (IEC 61850 / IEC 60870-5-104)              │               │   │
│  │  └───────────────────────────┬────────────────────────────┘               │   │
│  │                              │                                             │   │
│  └──────────────────────────────┼─────────────────────────────────────────────┘   │
│                                 │                                                  │
│                      ┌──────────▼──────────┐                                       │
│                      │    Azure IoT Hub    │                                       │
│                      └──────────┬──────────┘                                       │
│                                 │                                                  │
│                      ┌──────────▼──────────┐                                       │
│                      │     Eventstream     │                                       │
│                      │ • Thermal modeling  │                                       │
│                      │ • Event correlation │                                       │
│                      │ • Data enrichment   │                                       │
│                      └──────────┬──────────┘                                       │
│                                 │                                                  │
│         ┌───────────────────────┼───────────────────────┐                          │
│         │                       │                       │                          │
│         ▼                       ▼                       ▼                          │
│  ┌─────────────┐        ┌─────────────┐        ┌───────────────┐                  │
│  │ Eventhouse  │◄──────►│  OneLake    │        │ Digital Twin  │                  │
│  │ (KQL DB)    │        │ (Lake)      │        │   Builder     │                  │
│  └──────┬──────┘        └──────┬──────┘        └───────┬───────┘                  │
│         │                      │                       │                          │
│         │                      │                       ▼                          │
│         │                      │               ┌───────────────┐                  │
│         │                      │               │ Fabric Graph  │                  │
│         │                      │               │ (Topology)    │                  │
│         │                      │               └───────────────┘                  │
│         │                      │                                                  │
│         │               ┌──────▼──────┐                                           │
│         │               │  ML Models  │                                           │
│         │               │ • RUL       │                                           │
│         │               │ • DGA Class │                                           │
│         │               │ • Thermal   │                                           │
│         │               └──────┬──────┘                                           │
│         │                      │                                                  │
│         └──────────────────────┼────────────────────────┐                         │
│                                │                        │                         │
│                    ┌───────────▼───────────┐   ┌────────▼────────┐               │
│                    │    Real-Time          │   │    Power BI     │               │
│                    │    Dashboard          │   │    Reports      │               │
│                    │ • 3D Substation View  │   │ • Fleet Health  │               │
│                    │ • Health Gauges       │   │ • Aging Analysis│               │
│                    │ • Thermal Trends      │   │ • Cost Reports  │               │
│                    └───────────┬───────────┘   └─────────────────┘               │
│                                │                                                  │
│                    ┌───────────▼───────────┐                                      │
│                    │      Activator        │                                      │
│                    │ • DGA Alerts          │──► Asset Engineers                   │
│                    │ • Thermal Warnings    │──► Control Room                      │
│                    │ • Maintenance Triggers│──► D365 Field Service               │
│                    └───────────┬───────────┘                                      │
│                                │                                                  │
│                    ┌───────────▼───────────┐                                      │
│                    │   Copilot / Agents    │                                      │
│                    │ • Health Reports      │──► Asset Managers                    │
│                    │ • DGA Interpretation  │──► Transformer Engineers             │
│                    │ • Maintenance Opt.    │──► Planners                          │
│                    └───────────────────────┘                                      │
│                                                                                    │
└────────────────────────────────────────────────────────────────────────────────────┘
```

---

## Business Outcomes

| Outcome | Metric | Expected Improvement |
|---------|--------|---------------------|
| **Reduced inspections** | Physical inspection frequency | 50-70% reduction |
| **Extended asset life** | Transformer replacement age | 5-10 years extension |
| **Prevented failures** | Unplanned transformer outages | 80% reduction |
| **Faster diagnostics** | Time to diagnose issues | 75% reduction |
| **Maintenance optimization** | Maintenance cost per asset | 30% reduction |
| **Improved safety** | Confined space entries | 60% reduction |
| **Better planning** | Asset replacement forecast accuracy | 40% improvement |

**Estimated Annual Value: $3M - $10M**

---

## Implementation Roadmap

### Phase 1 (0-6 months): Critical Asset Monitoring
- Deploy enhanced monitoring on 10 critical transformers
- Integrate DGA, temperature, and load data into Fabric
- Build Digital Twin for pilot substations
- Implement basic health scoring

### Phase 2 (6-12 months): Fleet Expansion
- Extend monitoring to all 66kV transformers
- Deploy chamber environmental monitoring
- Implement ML-based RUL predictions
- Build 3D visualization dashboards

### Phase 3 (12-18 months): Intelligent Operations
- Integrate with D365 Field Service for work orders
- Deploy Copilot for natural language interaction
- Implement simulation capabilities
- Enable automated maintenance scheduling

### Sensor Retrofit Strategy
| Priority | Equipment | Sensors | Benefit |
|----------|-----------|---------|---------|
| High | Critical transformers (>40MVA) | Online DGA, bushing monitors | Prevent catastrophic failures |
| Medium | Secondary transformers | Temperature, load monitoring | Optimize maintenance |
| Lower | Switchgear, aux. systems | Status, environmental | Complete visibility |
