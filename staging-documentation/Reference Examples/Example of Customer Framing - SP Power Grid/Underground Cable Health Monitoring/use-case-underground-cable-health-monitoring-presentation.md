---
marp: true
theme: default
paginate: true
backgroundColor: #ffffff
header: 'Microsoft Fabric - Real-Time Intelligence'
footer: 'Underground Cable Health Monitoring | SP PowerGrid Singapore'
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

# Underground Cable Health Monitoring & Predictive Maintenance

## SP PowerGrid Singapore | Real-Time Intelligence

**Transforming Underground Grid Operations with AI-Powered Predictive Analytics**

---

# Business Challenge

## Singapore's Underground Grid Reality

- **100%** of transmission and distribution cables are underground
- **Reactive maintenance** leads to 4-8 hour average outage duration
- **Confined space** inspections are costly, risky, and infrequent
- **Cable failures** cost $500K-$2M+ per incident in repairs and penalties
- **Asset replacement** decisions made without condition data

### The Question
*How can SP PowerGrid predict cable failures before they occur and extend asset life?*

---

# Solution Overview

## AI-Powered Cable Health Monitoring

| Component | Function |
|-----------|----------|
| **DTS Fiber Optic** | 1-meter resolution temperature monitoring along cable routes |
| **Partial Discharge Sensors** | Detect insulation degradation before failure |
| **Eventstream + Eventhouse** | Real-time telemetry processing & time-series storage |
| **Digital Twin Builder** | 3D cable route visualization with health overlays |
| **ML Models** | Health Index scoring, RUL prediction, anomaly detection |
| **Activator** | Automated maintenance workflow triggering |
| **Copilot** | Natural language queries for engineers |

---

# Architecture Overview

![width:950px](media/use-case-underground-cable-health-monitoring-architecture-diagram.png)

<!-- Note: Export the .excalidraw file to PNG and place in media/ folder -->

---

# Data Flow Summary

## From Sensors to Decisions

1. **Ingest**: DTS/PD/Environmental sensors → IoT Hub (10,000+ sensors)
2. **Process**: Eventstream normalizes heterogeneous sensor protocols
3. **Store**: Eventhouse provides millisecond-precision time-series analytics
4. **Model**: Digital Twin creates spatial context for anomalies
5. **Predict**: ML models calculate Health Index and RUL for each segment
6. **Act**: Activator triggers condition-based maintenance workflows
7. **Assist**: Copilot enables natural language data exploration

---

# Business Outcomes

## Quantified Value for SP PowerGrid

| Metric | Impact |
|--------|--------|
| **Unplanned Outages** | 60-80% reduction |
| **Asset Life Extension** | 15-25% longer cable life |
| **Inspection Efficiency** | 50% fewer confined space entries |
| **Fault Location Time** | Reduced from hours to minutes |
| **Annual Value** | $8M-$20M in avoided failures & extended life |

### ROI Timeline
- **Phase 1** (6 months): Pilot on critical 66kV circuits
- **Phase 2** (12 months): Fleet-wide 22kV deployment
- **Phase 3** (18 months): Full predictive maintenance operations

---

# Next Steps

## Implementation Roadmap

1. **Discovery Workshop** (Week 1-2)
   - Identify pilot cable circuits and existing sensor infrastructure
   - Map integration points with existing SCADA/Spectrum Power

2. **Proof of Value** (Week 3-8)
   - Deploy Fabric workspace with sample DTS data
   - Demonstrate Health Index scoring on 3-5 cable circuits

3. **Production Pilot** (Month 3-6)
   - Scale to 50+ critical circuits
   - Integrate with maintenance management system

### Contact
Microsoft Energy & Utilities Team | [Schedule Discovery Session]

---

<!-- _class: lead -->

# Questions?

**Underground Cable Health Monitoring & Predictive Maintenance**

SP PowerGrid Singapore | Microsoft Fabric Real-Time Intelligence

