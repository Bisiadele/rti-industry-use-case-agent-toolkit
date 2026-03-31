# Architecture Explanation: Grid Surveillance & Fault Location

## Use Case Overview

**Use Case:** Grid Surveillance & Fault Location

**Role of AI:** Detection, Classification, Location

**Primary Data Sources:** Fault recorders, traveling wave sensors, impedance measurements, SCADA alarms, protection relay events, AMI last-gasp signals

**Data Type Produced:** Fault signatures, waveforms, event sequences, fault location estimates, outage extent, restoration status

**End Users:** Control room operators, fault response teams, field crews, protection engineers, operations managers

---

## Business Problem

Fault location in underground networks is exceptionally challenging:

**Underground-Specific Challenges:**
- **No visual indicators**: Unlike overhead lines, underground faults are invisible
- **Complex cable routes**: Cables follow non-linear paths through ducts and chambers
- **Multiple joints**: Each joint is a potential failure point with unique characteristics
- **Limited access**: Must excavate or access chambers to inspect/repair
- **High repair costs**: Underground repairs are 5-10x more expensive than overhead
- **Extended outage duration**: Average restoration time significantly longer

**Current State Issues:**
- Fault location accuracy often ±500m or worse
- Multiple excavations sometimes needed to find fault
- Reliance on cable route drawings that may be inaccurate
- Limited integration between protection systems and fault location
- Manual correlation of data from multiple sources
- Customer impact difficult to assess quickly

**Business Impact:**
- Extended Customer Minutes Interrupted (CMI)
- High repair costs from multiple excavations
- Safety risks from unknown fault locations
- Regulatory scrutiny on SAIDI/SAIFI performance
- Customer dissatisfaction during outages

---

## 1. Data Sources to Ingest & Process

### Real-Time Streaming Data

| Data Source | Technology | Protocol | Frequency | Business Signal |
|-------------|------------|----------|-----------|-----------------|
| **Traveling Wave Fault Locators** | Time-synchronized recorders | IEEE C37.111 COMTRADE | Event-triggered | Precise fault location via wave timing |
| **Distance Protection Relays** | Impedance-based | IEC 61850 MMS | Event-triggered | Fault distance estimate from relay |
| **Fault Recorders** | Digital fault recorders | COMTRADE | Event-triggered | Detailed fault waveforms |
| **Protection Events** | Relay trip/reclose | IEC 61850 GOOSE | Real-time | Protection operations sequence |
| **SCADA Alarms** | RTU/Gateway | IEC 60870-5-104 | Real-time | Equipment status changes |
| **AMI Last-Gasp** | Smart meters | RF mesh / cellular | Within 2 min | Customer outage confirmation |
| **Fault Indicators** | Directional fault passage | LoRaWAN / cellular | Event-triggered | Fault path identification |
| **DAS Fiber Sensing** | Distributed Acoustic Sensing | Interrogator | Continuous | Vibration signatures |

### Batch/Reference Data

| Data Source | Frequency | Purpose |
|-------------|-----------|---------|
| **Cable Route GIS** | Weekly | Precise cable paths, joint locations, depths |
| **Network Model** | Daily | Electrical topology, impedances, ratings |
| **Historical Faults** | On change | Past fault patterns, common failure modes |
| **Cable Specifications** | Static | Propagation velocities, impedance characteristics |
| **Joint Registry** | Monthly | Joint locations, types, installation dates |
| **Customer Connectivity** | Daily | Transformer-customer mapping |

### Fault Location Technologies

| Technology | Accuracy | Coverage | Best For |
|------------|----------|----------|----------|
| **Traveling Wave** | ±1% of distance | Requires sensors at both ends | High-voltage cables |
| **Impedance-based** | ±5-10% | Standard relay capability | General fault estimation |
| **Murray Loop** | ±1-2% | Requires healthy parallel cable | Post-fault verification |
| **Time Domain Reflectometry** | ±1-3% | Portable equipment | Pre-energization testing |
| **Distributed Sensing** | ±10-50m | Requires fiber co-location | Cables with fiber |
| **Fault Indicators** | Zone level | Distributed along route | Narrowing search area |

---

## 2. Analyze & Transform

### Eventstream Processing

**Stream 1: Fault Detection & Classification**
```yaml
Input: Protection trips, fault currents, voltages
Transform:
  - Detect fault inception from current/voltage changes
  - Classify fault type (3-phase, L-G, L-L, L-L-G)
  - Identify faulted phases
  - Calculate fault current magnitude
  - Determine fault duration
Output: Fault classification record to Eventhouse
```

**Stream 2: Fault Location Calculation**
```yaml
Input: Traveling wave timestamps, impedance measurements
Transform:
  - Time-synchronize traveling wave arrivals
  - Calculate distance from wave propagation
  - Refine with impedance-based estimate
  - Apply cable model for accuracy
  - Convert electrical distance to physical location
Output: Fault location estimate with confidence interval
```

**Stream 3: Outage Extent Assessment**
```yaml
Input: AMI last-gasp, breaker status, network topology
Transform:
  - Identify de-energized sections
  - Count affected customers
  - Classify customers by priority (hospitals, critical loads)
  - Calculate load lost
  - Identify restoration options
Output: Outage impact assessment
```

**Stream 4: Event Correlation**
```yaml
Input: Multiple protection events, SCADA alarms
Transform:
  - Correlate related events (trip sequences)
  - Identify root cause device
  - Distinguish primary fault from sympathetic trips
  - Build event timeline
Output: Correlated fault event chain
```

### Traveling Wave Analysis

**Fault Location Algorithm:**
```
Distance to Fault = (T1 - T2 + L/v) × v / 2

where:
  T1 = Time of first wave arrival at near end
  T2 = Time of first wave arrival at far end
  L = Total cable length
  v = Wave propagation velocity (~150-170 m/μs for XLPE)
```

**Multi-sensor Triangulation:**
- With 3+ sensors, use time-difference-of-arrival
- Iterative least-squares solution for best fit
- Confidence interval based on timing uncertainty

### Impedance-Based Refinement

**Takagi Algorithm (single-ended):**
```
m = Im{Vs × (Is - k×I0)*} / Im{Zl × Is × (Is - k×I0)*}

where:
  m = per-unit distance to fault
  Vs = Measured voltage at relay
  Is = Measured current at relay
  Zl = Line impedance per unit length
  I0 = Zero-sequence current
  k = Zero-sequence compensation factor
```

### Eventhouse Configuration

| Table | Retention | Purpose |
|-------|-----------|---------|
| `fault_events` | 10 years | Complete fault event history |
| `fault_waveforms` | 5 years | COMTRADE files for analysis |
| `fault_locations` | 10 years | Location estimates and actuals |
| `outage_extent` | 10 years | Customer impact records |
| `ami_lastgasp` | 90 days | Smart meter outage signals |
| `restoration_events` | 10 years | Restoration timeline records |

---

## 3. Model & Contextualize

### Digital Twin Builder

**Underground Network Model:**
```
Distribution Network
├── Feeder F-101 (22kV Underground)
│   ├── Source: Substation SS-01, Breaker CB-101
│   ├── Section 1 (SS-01 to Joint Bay JB-01)
│   │   ├── Cable Segment CS-01 (450m, 630mm² Al XLPE)
│   │   │   ├── Start: SS-01 Cable Termination
│   │   │   ├── Route: Along Main Street duct bank
│   │   │   └── End: Joint J-01 in JB-01
│   │   └── Fault Indicator FI-01
│   ├── Joint Bay JB-01
│   │   ├── Joint J-01 (Heat-shrink, installed 2018)
│   │   └── Joint J-02 (Cold-shrink, installed 2015)
│   ├── Section 2 (JB-01 to RMU-01)
│   │   ├── Cable Segment CS-02 (380m, 630mm² Al XLPE)
│   │   └── Branch Point BP-01
│   │       ├── Branch 2A → RMU-01
│   │       └── Branch 2B → RMU-02
│   └── Ring Main Units (RMU-01 to RMU-05)
│       └── Distribution Transformers
│           └── Customer Connections
└── Feeder F-102 (22kV Underground)
    └── ...
```

**Twin Properties:**
- Cable segments: Length, type, impedance, propagation velocity
- Joints: Location (GPS), type, age, installation contractor
- Route: GIS coordinates, depth profile, crossing utilities
- Sensors: Fault indicators, traveling wave detectors

### Fabric Graph

**Fault Analysis Relationships:**
```
Cable_Segment ──[connects_to]──► Joint
Joint ──[located_in]──► Joint_Bay
Cable_Segment ──[feeds]──► Distribution_Transformer
Distribution_Transformer ──[serves]──► Customer
Feeder ──[protected_by]──► Protection_Relay
Feeder ──[monitored_by]──► Fault_Indicator
Cable_Segment ──[parallel_to]──► Cable_Segment (for Murray loop)
Joint_Bay ──[accessible_from]──► Access_Point
```

**Graph-Powered Queries:**
- "Which joints are within 100m of the estimated fault location?"
- "What is the fastest access route to this cable section?"
- "Which customers will be restored if we switch via RMU-03?"
- "What cables share the same duct bank (potential sympathetic damage)?"
- "Show historical faults within 500m of this location"

---

## 4. Train & Score

### Fault Classification Model

**ML-based Fault Type Classification:**
| Input Features | Algorithm | Output |
|----------------|-----------|--------|
| Voltage/current waveforms | CNN | Fault type (LG, LL, LLG, LLL) |
| Sequence components | Random Forest | Faulted phases |
| Waveform shape | Autoencoder | Fault cause (cable, joint, external) |
| Pre-fault conditions | XGBoost | Likely failure mechanism |

### Fault Location Refinement Model

**Ensemble Fault Locator:**
```
Final Location = weighted_average(
    TW_location × w1,      # Traveling wave
    IMP_location × w2,     # Impedance-based
    ML_location × w3       # ML pattern matching
)

where weights based on:
  - Sensor availability
  - Signal quality
  - Historical accuracy
```

**ML Location Enhancement:**
- Train on historical faults with known locations
- Learn cable-specific propagation characteristics
- Account for joint reflections and wave distortion
- Improve accuracy over time with feedback

### Outage Prediction Model

| Model | Purpose | Input | Output |
|-------|---------|-------|--------|
| **Restoration Time** | Predict repair duration | Fault type, location, weather, crew availability | Estimated minutes to restore |
| **Fault Recurrence** | Predict if fault will recur | Fault characteristics, cable age, history | Recurrence probability |
| **Cascade Risk** | Assess risk of related failures | Fault current, thermal stress, adjacent cables | Risk score |

### Real-Time Scoring

Fault events trigger immediate ML scoring:
1. **T+0 sec**: Protection trips, initial location estimate
2. **T+5 sec**: Traveling wave analysis complete
3. **T+30 sec**: ML refinement with historical patterns
4. **T+60 sec**: Full assessment with customer impact
5. **T+5 min**: Crew dispatch recommendation

---

## 5. Visualize & Act

### Real-Time Dashboard

**Fault Response Dashboard:**

| Panel | Content | Update Rate |
|-------|---------|-------------|
| **Network Map** | Geographic view with fault location marker | Real-time |
| **Fault Details** | Type, phases, current, duration | Event-triggered |
| **Location Confidence** | Estimated location with ±X meter range | Event-triggered |
| **Customer Impact** | Affected customers, priority loads | 1 minute |
| **Access Routes** | Nearest access points, directions | Event-triggered |
| **Crew Status** | Available crews, locations, ETA | 1 minute |
| **Switching Options** | Restoration scenarios with customer counts | Event-triggered |
| **Historical Context** | Past faults in area, patterns | On-demand |

**Fault Location Detail View:**
- Cable route overlay on satellite/street map
- Estimated fault zone highlighted
- Nearby joint locations marked
- Access points with distance
- Underground utility crossings
- Historical fault markers

### Power BI Reports

**Reliability Analysis Reports:**
- SAIDI/SAIFI trends by feeder
- Fault cause breakdown (cable vs joint vs external)
- Location accuracy analysis (estimated vs actual)
- Repeat fault analysis
- Contractor performance (joint failures by installer)
- Cable age vs failure rate

**Operational Performance Reports:**
- Average time to locate
- Number of excavations per fault
- Restoration time trends
- Crew response metrics
- Fault location system accuracy

### Activator Automation

| Trigger | Condition | Action |
|---------|-----------|--------|
| **Fault Detected** | Protection trip confirmed | Initiate fault location sequence |
| **Location Ready** | Confidence > 80% | Notify control room with location |
| **Priority Customer** | Hospital/critical load affected | Escalate to supervisor, SMS to customer |
| **Crew Dispatch** | Location confirmed, crew available | Auto-assign nearest crew, send details |
| **Access Arranged** | Excavation permit required | Trigger permit request workflow |
| **Restoration Option** | Alternative supply available | Present switching options to operator |
| **Fault Cleared** | All customers restored | Close incident, trigger report |

### Field Crew Mobile App

**Push notifications with:**
- Fault location coordinates
- Nearest access point directions
- Cable specifications and depth
- Historical information
- Safety hazards nearby
- Contact details for access

---

## 6. Get Assisted & Interact

### Copilot Integration

**Natural Language for Operators:**
- "Where exactly is the fault on Feeder 101?"
- "How confident are we in the location estimate?"
- "Show me all joints within the fault zone"
- "What is the fastest way to restore power to the hospital?"
- "Have we had faults in this area before?"
- "What caused the last fault on this cable?"
- "Generate a preliminary fault report for this incident"

**AI-Assisted Diagnosis:**
- Automatic analysis of fault waveforms
- Pattern matching with historical faults
- Natural language explanation of findings
- Recommended investigation steps

### Data Agents

| Agent | Function | Trigger |
|-------|----------|---------|
| **Fault Reporter** | Generate preliminary fault report | On fault clearance |
| **Location Optimizer** | Refine location estimate with multiple methods | On fault detection |
| **Restoration Advisor** | Recommend optimal switching sequence | When requested |
| **Historical Analyzer** | Find similar past faults | On fault detection |
| **Permit Coordinator** | Initiate excavation permit process | When location confirmed |
| **Customer Communicator** | Generate customer notifications | On outage > 30 min |

### Operations Agents

**Automated Fault Response Workflow:**
```
Fault Detected
    │
    ├──► Location Agent calculates fault position
    │       └──► Confidence check
    │               ├─► High: Proceed to dispatch
    │               └─► Low: Request additional data
    │
    ├──► Impact Agent assesses customer impact
    │       └──► Priority customer check
    │               └─► Yes: Escalate notification
    │
    ├──► Restoration Agent evaluates options
    │       └──► Switching possible?
    │               ├─► Yes: Present options
    │               └─► No: Direct repair needed
    │
    └──► Dispatch Agent assigns crew
            └──► Nearest available crew
                    └──► Send location, route, details
```

---

## Architecture Summary

```
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                    Grid Surveillance & Fault Location Architecture                   │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│  UNDERGROUND NETWORK                                                                │
│  ┌─────────────────────────────────────────────────────────────────────────────┐   │
│  │                                                                             │   │
│  │  ┌───────────┐  ┌───────────┐  ┌───────────┐  ┌───────────┐               │   │
│  │  │ Traveling │  │ Distance  │  │ Fault     │  │ Fault     │               │   │
│  │  │ Wave      │  │ Protection│  │ Recorders │  │ Indicators│               │   │
│  │  │ Sensors   │  │ Relays    │  │ (DFR)     │  │ (FI)      │               │   │
│  │  └─────┬─────┘  └─────┬─────┘  └─────┬─────┘  └─────┬─────┘               │   │
│  │        │              │              │              │                      │   │
│  │  ┌─────┴──────────────┴──────────────┴──────────────┴─────┐               │   │
│  │  │                    SICAM RTU / IEC 61850                │               │   │
│  │  └───────────────────────────┬────────────────────────────┘               │   │
│  │                              │                                             │   │
│  │  ┌───────────┐  ┌────────────┴────────────┐  ┌───────────┐               │   │
│  │  │ AMI Smart │  │                         │  │ DAS Fiber │               │   │
│  │  │ Meters    │  │                         │  │ Sensing   │               │   │
│  │  │ (Last-Gasp)  │                         │  │           │               │   │
│  │  └─────┬─────┘  │                         │  └─────┬─────┘               │   │
│  │        │        │                         │        │                      │   │
│  └────────┼────────┼─────────────────────────┼────────┼──────────────────────┘   │
│           │        │                         │        │                          │
│           └────────┴───────────┬─────────────┴────────┘                          │
│                                │                                                  │
│                     ┌──────────▼──────────┐                                       │
│                     │    Azure IoT Hub    │                                       │
│                     └──────────┬──────────┘                                       │
│                                │                                                  │
│                     ┌──────────▼──────────┐                                       │
│                     │     Eventstream     │                                       │
│                     │ • Fault detection   │                                       │
│                     │ • TW analysis       │                                       │
│                     │ • Event correlation │                                       │
│                     │ • Outage extent     │                                       │
│                     └──────────┬──────────┘                                       │
│                                │                                                  │
│        ┌───────────────────────┼───────────────────────┐                          │
│        │                       │                       │                          │
│        ▼                       ▼                       ▼                          │
│ ┌─────────────┐        ┌─────────────┐        ┌───────────────┐                  │
│ │ Eventhouse  │◄──────►│  OneLake    │        │ Digital Twin  │                  │
│ │ • Fault DB  │        │ • History   │        │ • Cable Routes│                  │
│ │ • Waveforms │        │ • Patterns  │        │ • Joints      │                  │
│ └──────┬──────┘        └──────┬──────┘        └───────┬───────┘                  │
│        │                      │                       │                          │
│        │                      │                       ▼                          │
│        │                      │               ┌───────────────┐                  │
│        │                      │               │ Fabric Graph  │                  │
│        │                      │               │ • Topology    │                  │
│        │                      │               │ • Customer map│                  │
│        │                      │               └───────────────┘                  │
│        │               ┌──────▼──────┐                                           │
│        │               │  ML Models  │                                           │
│        │               │ • Fault Type│                                           │
│        │               │ • Location  │                                           │
│        │               │ • Duration  │                                           │
│        │               └──────┬──────┘                                           │
│        │                      │                                                  │
│        └──────────────────────┼────────────────────────┐                         │
│                               │                        │                         │
│                   ┌───────────▼───────────┐   ┌────────▼────────┐               │
│                   │    Real-Time          │   │    Power BI     │               │
│                   │    Dashboard          │   │    Reports      │               │
│                   │ • Fault Map           │   │ • SAIDI/SAIFI   │               │
│                   │ • Location Display    │   │ • Accuracy      │               │
│                   │ • Customer Impact     │   │ • Trends        │               │
│                   │ • Restoration Options │   │                 │               │
│                   └───────────┬───────────┘   └─────────────────┘               │
│                               │                                                  │
│                   ┌───────────▼───────────┐                                      │
│                   │      Activator        │                                      │
│                   │ • Fault Notification  │──► Control Room                      │
│                   │ • Crew Dispatch       │──► Field Crews                       │
│                   │ • Customer Alert      │──► Priority Customers                │
│                   │ • Permit Request      │──► Excavation Team                   │
│                   └───────────┬───────────┘                                      │
│                               │                                                  │
│                   ┌───────────▼───────────┐                                      │
│                   │   Copilot / Agents    │                                      │
│                   │ • Location Analysis   │──► Protection Engineers              │
│                   │ • Restoration Advisor │──► Control Room                      │
│                   │ • Fault Reports       │──► Operations Managers               │
│                   └───────────────────────┘                                      │
│                                                                                   │
└───────────────────────────────────────────────────────────────────────────────────┘
```

---

## Business Outcomes

| Outcome | Metric | Expected Improvement |
|---------|--------|---------------------|
| **Faster fault location** | Time from trip to location | 60-80% reduction |
| **Fewer excavations** | Digs per fault event | 50% reduction (avg 2 → 1) |
| **Reduced outage duration** | SAIDI contribution | 40-50% improvement |
| **Lower repair costs** | Cost per fault repair | 30% reduction |
| **Improved accuracy** | Location estimate accuracy | 90%+ within 50m |
| **Better customer comms** | Time to first customer update | 80% faster |
| **Predictive insights** | Repeat faults prevented | 20% reduction |

**Estimated Annual Value: $2M - $8M**

---

## Implementation Roadmap

### Phase 1 (0-6 months): Foundation
- Integrate existing protection relay data
- Build fault event database in Eventhouse
- Implement impedance-based fault location
- Create basic fault location dashboard

### Phase 2 (6-12 months): Enhanced Location
- Deploy traveling wave sensors on critical feeders
- Implement ML-based fault classification
- Integrate AMI last-gasp signals
- Build Fabric Graph for network topology

### Phase 3 (12-18 months): Intelligent Response
- Deploy fault indicators across network
- Implement automated crew dispatch via Activator
- Add Copilot for natural language analysis
- Integrate with field crew mobile app
- Connect to excavation permit system

### Sensor Deployment Strategy

| Priority | Feeders | Technology | Investment |
|----------|---------|------------|------------|
| Highest | 132kV cables | Traveling wave at both ends | $200K per feeder |
| High | Critical 22kV | Traveling wave + fault indicators | $100K per feeder |
| Medium | Standard 22kV | Fault indicators every 500m | $50K per feeder |
| Lower | LV network | AMI-based outage detection | Existing infrastructure |

---

## Singapore Underground-Specific Considerations

### Cable Route Documentation
- Integrate with Singapore's Underground Utility Survey (UUS)
- LTA cable duct records
- PUB drainage crossing records
- SingTel/M1 fiber co-location opportunities

### Access Point Management
- Coordinate with Land Transport Authority for road excavations
- Building management system integration for indoor substations
- NEA permit requirements for certain areas

### Regulatory Alignment
- EMA reliability reporting requirements
- SCDF safety requirements for confined spaces
- MOM workplace safety regulations for excavations
