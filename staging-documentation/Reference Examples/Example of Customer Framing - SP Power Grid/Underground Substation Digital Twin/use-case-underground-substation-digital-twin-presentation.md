---
marp: true
theme: default
paginate: true
backgroundColor: #ffffff
header: 'Microsoft Fabric - Real-Time Intelligence'
footer: 'Underground Substation Digital Twin | SP PowerGrid Singapore'
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

# Underground Substation Digital Twin & Asset Intelligence

## SP PowerGrid Singapore | Real-Time Intelligence

**3D Visualization and AI-Powered Transformer Health Management**

---

# Business Challenge

## Underground Substation Operations

- **Confined space constraints**: Physical inspections are costly and risky
- **Hidden degradation**: Transformer issues develop invisibly over years
- **Reactive replacement**: Assets replaced based on age, not condition
- **Manual analysis**: DGA interpretation requires specialist expertise
- **Limited visibility**: No unified view of equipment health across fleet

### The Question
*How can SP PowerGrid see inside underground substations and predict transformer failures?*

---

# Solution Overview

## 3D Digital Twin with AI Asset Intelligence

| Component | Function |
|-----------|----------|
| **DGA Monitors** | Continuous dissolved gas analysis for fault detection |
| **Thermal Sensors** | Winding hot-spot and connection temperature monitoring |
| **Eventstream + Eventhouse** | Real-time health data processing |
| **Digital Twin Builder** | 3D substation models with health overlays |
| **Fabric Graph** | Asset hierarchy and impact relationships |
| **ML Models** | Health Index, DGA classification, RUL prediction |
| **D365 Field Service** | Condition-based work order generation |

---

# Architecture Overview

![width:950px](media/use-case-underground-substation-digital-twin-architecture-diagram.png)

<!-- Note: Export the .excalidraw file to PNG and place in media/ folder -->

---

# 3D Digital Twin Capabilities

## Virtual Substation Experience

### Visualization Features
- **3D Equipment Models**: Transformers, switchgear, cables in spatial context
- **Health Overlays**: Color-coded equipment status (green/yellow/red)
- **Sensor Locations**: See exactly where measurements originate
- **Thermal Profiles**: Visualize temperature gradients across equipment

### Interaction Capabilities
- **Virtual Walkthrough**: Navigate substation without confined space entry
- **Click-to-Detail**: Select any equipment for full health analysis
- **Historical Playback**: Review conditions at any point in time
- **Alert Visualization**: See anomaly locations in spatial context

---

# AI-Powered Health Assessment

## Transformer Intelligence

| Model | Function | Benefit |
|-------|----------|---------|
| **Health Index (HI)** | Composite 0-100 score from multiple factors | Objective fleet ranking |
| **DGA Classification** | Fault type identification from gas ratios | Targeted inspection |
| **Thermal Anomaly** | Detect abnormal heating patterns | Early warning |
| **RUL Prediction** | Remaining useful life estimation | Optimal replacement timing |
| **Failure Mode** | Likely failure mechanism identification | Appropriate response |

### DGA Intelligence
*ML-enhanced Duval Triangle and Rogers Ratio analysis with historical pattern matching*

---

# Business Outcomes

## Quantified Value for SP PowerGrid

| Metric | Impact |
|--------|--------|
| **Equipment Availability** | 20-30% improvement |
| **Transformer Life Extension** | 5-10 years additional service |
| **Confined Space Entries** | 50% reduction through virtual inspection |
| **Fault Investigation Time** | 40% faster with 3D context |
| **Annual Value** | $3M-$10M in availability & life extension |

### Safety & Efficiency
*Reduce human exposure to underground hazards while improving asset visibility*

---

# Integration with Field Service

## D365 Field Service Connection

### Automated Workflows
- Health Index triggers generate work orders automatically
- Emergency DGA alerts escalate to immediate response
- Condition-based scheduling optimizes technician routes

### Field Technician Experience
- 3D equipment location on mobile device
- Confined space entry requirements and PPE
- Step-by-step maintenance procedures
- Historical health context for diagnosis

---

# Next Steps

## Implementation Roadmap

1. **Discovery Workshop** (Week 1-2)
   - Identify pilot underground substations
   - Inventory existing DGA and sensor infrastructure

2. **Proof of Value** (Week 3-8)
   - Deploy 3D digital twin for 2-3 pilot substations
   - Demonstrate Health Index scoring on transformers

3. **Production Deployment** (Month 3-6)
   - Scale to all underground substations
   - Integrate with D365 Field Service

### Contact
Microsoft Energy & Utilities Team | [Schedule Discovery Session]

---

<!-- _class: lead -->

# Questions?

**Underground Substation Digital Twin & Asset Intelligence**

SP PowerGrid Singapore | Microsoft Fabric Real-Time Intelligence

