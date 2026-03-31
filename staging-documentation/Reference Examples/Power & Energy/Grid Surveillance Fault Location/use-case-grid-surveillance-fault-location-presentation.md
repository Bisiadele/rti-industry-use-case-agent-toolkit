---
marp: true
theme: default
paginate: true
backgroundColor: #ffffff
header: 'Microsoft Fabric - Real-Time Intelligence'
footer: 'Grid Surveillance & Fault Location | SP PowerGrid Singapore'
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

# Grid Surveillance & Intelligent Fault Location

## SP PowerGrid Singapore | Real-Time Intelligence

**Locating Underground Faults Within ±5 Meters for Rapid Restoration**

---

# Business Challenge

## Underground Fault Response Reality

- **Hidden infrastructure**: Cables buried 1-3 meters below urban streets
- **Fault location difficulty**: Traditional methods take hours of searching
- **Customer impact**: 4-8 hour average restoration time
- **Traffic disruption**: Multiple excavation attempts in wrong locations
- **Cost exposure**: $500K-$2M per fault in repairs, penalties, customer impact

### The Question
*How can SP PowerGrid pinpoint fault locations instantly and restore power faster?*

---

# Solution Overview

## Multi-Modal Intelligent Fault Location

| Component | Function |
|-----------|----------|
| **TWFL Devices** | Traveling wave fault location (±5m accuracy) |
| **DTS Fiber** | Thermal anomaly detection along cable routes |
| **DAS Acoustic** | Fault sound and vibration detection |
| **Digital Relays** | Fault current waveforms and protection sequences |
| **Eventhouse** | High-speed waveform storage and analysis |
| **ML Models** | Location algorithm enhancement, fault classification |
| **Activator** | Automated crew dispatch with GPS coordinates |

---

# Architecture Overview

![width:950px](media/use-case-grid-surveillance-fault-location-architecture-diagram.png)

<!-- Note: Export the .excalidraw file to PNG and place in media/ folder -->

---

# Fault Location Technology

## How TWFL Works

### Traveling Wave Principle
1. **Fault occurs**: Creates voltage/current transient
2. **Wave travels**: Transient propagates at ~150-200 m/μs in cables
3. **Detection**: TWFL devices capture arrival times at multiple points
4. **Triangulation**: Time differences calculate fault distance

### Multi-Modal Confirmation
- **DTS**: Confirms thermal anomaly at calculated location
- **DAS**: Acoustic signature validates fault position
- **Protection**: Fault current magnitude indicates severity

### Result: ±5 meter accuracy vs. ±500 meter traditional methods

---

# Real-Time Fault Response

## From Detection to Dispatch

| Step | Time | Action |
|------|------|--------|
| **1. Detection** | 0 ms | TWFL captures fault transient |
| **2. Calculation** | <100 ms | ML algorithm calculates location |
| **3. Classification** | <500 ms | Fault type identified (SLG, LL, 3Φ) |
| **4. Visualization** | <1 sec | Location displayed on control room map |
| **5. Dispatch** | <2 min | Activator generates crew dispatch package |
| **6. Arrival** | 30-60 min | Crew arrives at GPS coordinates |

### Compared to Traditional: 2-4 hours to locate fault

---

# Business Outcomes

## Quantified Value for SP PowerGrid

| Metric | Impact |
|--------|--------|
| **Fault Location Accuracy** | ±5 meters vs. ±500 meters traditional |
| **Restoration Time** | Reduced from 4-8 hours to <2 hours |
| **Customer Minutes Interrupted** | 50-70% reduction |
| **Excavation Attempts** | Typically first-time success |
| **Annual Value** | $2M-$8M in faster restoration |

### Regulatory Benefit
*Improved SAIDI/SAIFI metrics for Energy Market Authority reporting*

---

# Crew Dispatch Automation

## Activator-Driven Response

### Dispatch Package Contents
- **GPS Coordinates**: Exact fault location on map
- **Access Point**: Nearest manhole or cable route access
- **Affected Circuits**: Impacted customers and critical loads
- **Fault Classification**: SLG, line-to-line, or three-phase
- **Estimated Materials**: Likely repair requirements
- **Safety Information**: Isolation points and procedures

### D365 Field Service Integration
*Automatic technician assignment based on location, qualifications, and availability*

---

# Historical Pattern Analysis

## Learning from Every Fault

### Continuous Improvement
- Each fault feeds ML model training
- Location accuracy improves over time
- Recurring fault patterns identified
- Vulnerable cable sections flagged

### Investigation Capabilities
- Copilot queries: "Show similar faults in past 5 years"
- Protection sequence replay for root cause
- Correlation with weather, loading, age factors

---

# Next Steps

## Implementation Roadmap

1. **Discovery Workshop** (Week 1-2)
   - Inventory existing TWFL and protection relay infrastructure
   - Identify pilot circuits and control room integration points

2. **Proof of Value** (Week 3-8)
   - Deploy Fabric workspace with historical fault data
   - Demonstrate ML-enhanced location on recorded faults

3. **Production Deployment** (Month 3-6)
   - Integrate with control room displays
   - Connect to D365 Field Service for dispatch automation

### Contact
Microsoft Energy & Utilities Team | [Schedule Discovery Session]

---

<!-- _class: lead -->

# Questions?

**Grid Surveillance & Intelligent Fault Location**

SP PowerGrid Singapore | Microsoft Fabric Real-Time Intelligence

