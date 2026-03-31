---
marp: true
theme: default
paginate: true
backgroundColor: #ffffff
header: 'Microsoft Fabric - Real-Time Intelligence'
footer: 'DERMS Real-Time Grid Integration | SP PowerGrid Singapore'
style: |
  section {
    font-family: 'Segoe UI', sans-serif;
  }
  h1 {
    color: #0078d4;
  }
  h2 {
    color: #106ebe;
  }
  table {
    font-size: 0.8em;
  }
  img {
    display: block;
    margin: 0 auto;
  }
---

<!-- _class: lead -->

# DERMS Real-Time Grid Integration & Virtual Power Plant

## SP PowerGrid Singapore | Real-Time Intelligence

**Orchestrating 10,000+ Distributed Energy Resources as a Unified Grid Asset**

---

# Business Challenge

## Singapore's Distributed Energy Transition

- **Rapid DER growth**: Solar PV, batteries, EV chargers scaling across the grid
- **Grid stability risks**: Variable renewable generation creates balancing challenges
- **Curtailment losses**: Excess solar wasted without storage coordination
- **Network constraints**: Distribution feeders approaching hosting capacity limits
- **Regulatory pressure**: Singapore Green Plan 2030 targets require DER integration

### The Question
*How can SP PowerGrid transform distributed chaos into coordinated grid flexibility?*

---

# Solution Overview

## AI-Powered Virtual Power Plant

| Component | Function |
|-----------|----------|
| **Multi-Protocol Ingestion** | IEEE 2030.5, OpenADR, OCPP, Modbus unification |
| **Eventstream + Eventhouse** | Sub-second DER telemetry processing |
| **Digital Twin Builder** | VPP geographic visualization & capacity mapping |
| **Fabric Graph** | Electrical topology for feasibility validation |
| **ML Models** | Solar forecast, load prediction, Volt-VAR optimization |
| **Activator** | Closed-loop DER dispatch commands |
| **Copilot** | Natural language grid operator queries |

---

# Architecture Overview

![width:950px](media/use-case-derms-real-time-grid-integration-architecture-diagram.png)

<!-- Note: Export the .excalidraw file to PNG and place in media/ folder -->

---

# Data Flow Summary

## From DERs to Optimized Dispatch

1. **Ingest**: Solar/BESS/EV/Meters/Weather → IoT Hub (10,000+ devices)
2. **Normalize**: Eventstream converts heterogeneous protocols to unified format
3. **Analyze**: Eventhouse enables 4-second response analytics
4. **Model**: Digital Twin visualizes VPP capacity by grid region
5. **Optimize**: ML models predict generation/load and optimize dispatch
6. **Execute**: Activator sends DER commands (charge/discharge/curtail)
7. **Feedback**: Closed-loop monitoring validates DER response
8. **Assist**: Copilot enables operator queries on grid flexibility

---

# Business Outcomes

## Quantified Value for SP PowerGrid

| Metric | Impact |
|--------|--------|
| **Peak Demand Reduction** | 15-30% through coordinated dispatch |
| **Renewable Utilization** | 8-12% improvement (less curtailment) |
| **Frequency Regulation** | 4-second response capability |
| **DER Integration** | Unified orchestration of 10,000+ devices |
| **Annual Value** | $4M-$12M in peak shaving & ancillary services |

### Singapore Green Plan Alignment
- Enables 2 GWp solar deployment target
- Supports EV charging infrastructure scaling
- Creates foundation for grid services market

---

# VPP Optimization Capabilities

## Real-Time Coordination

### Solar Forecast Integration
- 15-minute to 24-hour generation predictions
- Satellite imagery + historical pattern ML

### Battery Dispatch Optimization
- Peak shaving during evening demand
- Solar absorption during midday surplus
- Frequency response readiness

### EV Charging Management
- Shift charging to off-peak hours
- V2G readiness for grid services
- Session-aware flexibility signals

---

# Next Steps

## Implementation Roadmap

1. **Discovery Workshop** (Week 1-2)
   - Inventory existing DER communication infrastructure
   - Define VPP pilot geography and participating resources

2. **Proof of Value** (Week 3-8)
   - Deploy Fabric workspace with sample DER data
   - Demonstrate Volt-VAR optimization on pilot feeder

3. **Production Pilot** (Month 3-6)
   - Scale to 1,000+ DER devices
   - Integrate with Energy Market Authority (EMA) systems

### Contact
Microsoft Energy & Utilities Team | [Schedule Discovery Session]

---

<!-- _class: lead -->

# Questions?

**DERMS Real-Time Grid Integration & Virtual Power Plant**

SP PowerGrid Singapore | Microsoft Fabric Real-Time Intelligence

