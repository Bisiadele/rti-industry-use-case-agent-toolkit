# Architecture Storyline: Underground Cable Health Monitoring & Predictive Maintenance

**Source Document:** [use-case-underground-cable-health-monitoring-architecture-explanation.md](use-case-underground-cable-health-monitoring-architecture-explanation.md)

**Diagram:** [use-case-underground-cable-health-monitoring-architecture-diagram.excalidraw](use-case-underground-cable-health-monitoring-architecture-diagram.excalidraw)

---

## Architecture Flow Summary

This architecture enables SP PowerGrid to transform underground cable infrastructure from reactive to predictive maintenance, achieving 60-80% reduction in unplanned outages and extending cable asset life by 15-25%. By continuously monitoring Distributed Temperature Sensing (DTS), Partial Discharge (PD), and environmental sensors, the platform delivers real-time cable health scoring and Remaining Useful Life (RUL) predictions to asset managers and field engineers.

---

## Step-by-Step Data Flow

### Step 1: Sensor Data Collection
**DTS Fiber Optic / PD Sensors / Environmental Sensors / LoRaWAN Gateways → IoT Hub**

Distributed Temperature Sensing (DTS) fiber optic systems measure temperature every 1 meter along underground cable routes, sampling every 15-60 seconds. Partial Discharge (PD) sensors detect insulation degradation through high-frequency electrical pulse monitoring. Environmental sensors measure soil moisture, ground movement, and ambient conditions. LoRaWAN gateways aggregate data from sensors in manholes, cable tunnels, and joint bays. This multi-modal sensing provides complete visibility into cable health factors that precede failures by weeks or months.

### Step 2: IoT Hub → Eventstream
**IoT Hub → Eventstream**

Azure IoT Hub provides secure device connectivity with per-device authentication for 10,000+ sensors across Singapore's underground grid. Eventstream ingests the continuous telemetry streams (1-minute intervals), applying initial data quality validation and routing. This step ensures reliable, scalable ingestion of high-frequency sensor data while maintaining device-level security and telemetry integrity.

### Step 3: Eventstream → Eventhouse
**Eventstream → Eventhouse (KQL Database)**

Eventstream routes processed telemetry to Eventhouse for time-series storage and real-time analytics. The KQL database stores temperature profiles, PD patterns, and environmental conditions with millisecond precision, enabling hot spot detection and trend analysis. This high-performance time-series storage supports both real-time queries for immediate alerts and historical analysis for pattern recognition across 10+ years of cable operating data.

### Step 4a: Eventhouse ↔ OneLake
**Eventhouse ↔ OneLake (Bi-directional)**

Eventhouse synchronizes processed cable health data bidirectionally with OneLake, Fabric's unified data lake. Historical temperature profiles, PD event patterns, and maintenance records flow to OneLake for long-term storage and ML model training. Reference data including cable specifications, installation dates, and manufacturer ratings flow back from OneLake to enrich real-time analysis. This data lakehouse pattern enables both operational analytics and strategic asset planning.

### Step 4b: Eventhouse → Digital Twin Builder
**Eventhouse → Digital Twin Builder (Cable Network Model)**

Real-time sensor data feeds Digital Twin Builder to create a living 3D model of Singapore's underground cable network. The digital twin maps sensor locations to specific cable segments, joints, and accessories, providing spatial context for anomaly detection. Engineers can visualize thermal profiles along cable routes and identify hot spots relative to physical infrastructure like road crossings or utility intersections that may cause thermal derating.

### Step 5: Digital Twin Builder → Fabric Graph
**Digital Twin Builder → Fabric Graph (Asset Relationships)**

Fabric Graph models the complex relationships between cables, joints, substations, and connected infrastructure. When a cable segment shows degradation, graph queries identify all upstream substations and downstream customers potentially affected. This relationship modeling supports impact analysis and prioritization—understanding that a failing 66kV transmission cable affects more customers than a 22kV distribution feeder guides maintenance scheduling decisions.

### Step 6: Digital Twin / OneLake → ML Models
**Digital Twin / OneLake / Fabric Graph → ML Models (Train & Score)**

ML models train on historical cable health data, failure patterns, and maintenance outcomes stored in OneLake. The models include:
- **Thermal Anomaly Detection**: Identifies abnormal temperature patterns indicating overloading or external interference
- **PD Classification**: Categorizes partial discharge signatures (void discharge, surface discharge, corona) by severity
- **Remaining Useful Life (RUL)**: Predicts cable segment end-of-life based on degradation trajectories
- **Health Index Scoring**: Combines multiple factors into a 0-100 composite score for each cable segment

Models score against real-time data from Eventhouse, generating predictions that drive maintenance decisions.

### Step 7: ML Models → Real-Time Dashboard / Power BI
**ML Models → Real-Time Dashboard / Power BI**

Health scores, RUL predictions, and anomaly alerts flow to visualization layers. Real-Time Dashboard provides live operational views for control room operators monitoring the grid 24/7. Power BI delivers executive dashboards showing fleet health distribution, high-risk segment identification, and maintenance budget optimization. Asset managers can drill from portfolio-level health metrics down to individual cable joint thermal profiles, supporting both strategic planning and operational decisions.

### Step 8: Dashboards → Activator
**Real-Time Dashboard / Power BI → Activator**

Activator monitors dashboard metrics for threshold breaches and condition triggers. When a cable segment's health index drops below 60, or RUL falls below 18 months, Activator automatically initiates maintenance workflows. Critical alerts (imminent failure prediction) escalate to emergency response teams, while degradation trends generate planned maintenance work orders. This automation ensures no high-risk conditions go unaddressed while avoiding maintenance alert fatigue.

### Step 9: Real-Time Dashboard ↔ Copilot
**Real-Time Dashboard ↔ Copilot (Bi-directional)**

Engineers interact with cable health data through natural language queries. Copilot enables questions like "Which 66kV cable segments have shown increasing PD activity in the last 90 days?" or "What maintenance actions have historically resolved similar thermal anomalies?" This conversational interface democratizes access to complex analytical insights, allowing field engineers and asset planners to query data without KQL expertise.

### Step 10: Operations Agents → End Users
**Operations Agents → Asset Managers / Cable Engineers / Field Technicians**

Operations Agents proactively deliver insights to role-specific users:
- **Asset Managers**: Weekly fleet health reports, budget impact projections, and investment prioritization recommendations
- **Cable Engineers**: Technical analysis of degradation patterns, root cause hypotheses, and specification review
- **Field Technicians**: Mobile-optimized work orders with fault location coordinates, required equipment, and safety procedures

This intelligent delivery ensures each persona receives actionable insights formatted for their decision-making context.

---

## Business Outcomes Connected to Architecture

| Architecture Component | Business Outcome |
|------------------------|------------------|
| DTS + PD Sensors (Step 1) | Continuous monitoring replaces periodic inspections, catching 95% of failures before they occur |
| Eventhouse Time-Series (Step 3) | Millisecond-precision analytics enable detection of transient events that indicate developing faults |
| Digital Twin Builder (Step 4b) | Spatial visualization reduces fault location time by 50%, from hours of searching to precise coordinates |
| ML RUL Prediction (Step 6) | Predictive maintenance extends cable asset life by 15-25%, deferring $50M+ in replacement capital |
| Activator Automation (Step 8) | Zero missed critical alerts while reducing maintenance notification volume by 70% through intelligent filtering |
| Operations Agents (Step 10) | Field technician productivity increases 30% through mobile-delivered, context-aware work instructions |

---

## Confirmation

✅ This architecture diagram and storyline were generated using [use-case-underground-cable-health-monitoring-architecture-explanation.md](use-case-underground-cable-health-monitoring-architecture-explanation.md) as the primary source of truth.

*Document generated: March 2026*
