# Architecture Storyline: Underground Substation Digital Twin & Asset Intelligence

**Source Document:** [use-case-underground-substation-digital-twin-architecture-explanation.md](use-case-underground-substation-digital-twin-architecture-explanation.md)

**Diagram:** [use-case-underground-substation-digital-twin-architecture-diagram.excalidraw](use-case-underground-substation-digital-twin-architecture-diagram.excalidraw)

---

## Architecture Flow Summary

This architecture enables SP PowerGrid to create 3D digital twins of underground substations, integrating real-time equipment health data with spatial visualization and AI-powered asset intelligence. By combining DGA (dissolved gas analysis), thermal monitoring, and partial discharge sensing with transformer-specific ML models, the platform delivers 20-30% improvement in equipment availability and enables predictive maintenance that can extend transformer life by 5-10 years.

---

## Step-by-Step Data Flow

### Step 1: Substation Sensor Data Collection
**DGA Monitors / Thermal Sensors / Partial Discharge / Humidity Sensors / SCADA / Thermal Cameras → IoT Hub**

Dissolved Gas Analysis (DGA) monitors continuously sample transformer oil, detecting fault gases (hydrogen, methane, acetylene) that indicate developing failures. Thermal sensors measure winding hot-spot temperatures, tap changer contacts, and bushing terminals. Partial discharge sensors detect insulation degradation through acoustic and UHF monitoring. Humidity and environmental sensors track conditions that affect equipment aging. SCADA/RTU data provides electrical loading, voltage, and switching status. Thermal cameras deliver visual heat signatures for cabinet and connection monitoring. This comprehensive sensing creates a complete picture of substation health.

### Step 2: IoT Hub → Eventstream
**IoT Hub → Eventstream**

Azure IoT Hub securely aggregates sensor telemetry from underground substations, handling the connectivity challenges of below-grade installations (LoRaWAN, NB-IoT backhaul through utility tunnels). Eventstream processes incoming data, applying quality checks, timestamp alignment, and initial anomaly flagging. This ingestion layer handles the harsh electromagnetic environment of substations while maintaining reliable data delivery.

### Step 3: Eventstream → Eventhouse
**Eventstream → Eventhouse (KQL Database)**

Eventstream routes processed telemetry to Eventhouse for time-series storage and real-time analytics. The KQL database stores DGA trending data, thermal profiles, PD event signatures, and environmental measurements with the precision required for equipment condition assessment. This repository enables both immediate alarm generation and long-term trending analysis that reveals gradual degradation patterns invisible to threshold-based monitoring.

### Step 4a: Eventhouse ↔ OneLake
**Eventhouse ↔ OneLake (Bi-directional)**

Eventhouse synchronizes equipment health data with OneLake for historical analysis and ML model training. Years of DGA measurements, thermal trending, and maintenance records flow to OneLake to build equipment-specific baselines. Reference data including transformer nameplate ratings, cooling configurations, and manufacturer recommendations flow back to Eventhouse. This bidirectional flow supports both real-time operations and long-term asset strategy development.

### Step 4b: Eventhouse → Digital Twin Builder
**Eventhouse → Digital Twin Builder (3D Substation Model)**

Real-time sensor data feeds Digital Twin Builder to create a living 3D model of each underground substation. The digital twin visualizes transformer bays, switchgear lineups, cable terminations, and auxiliary systems in accurate spatial relationships. Equipment health indicators (temperature, DGA status, PD activity) overlay on 3D models, enabling engineers to "walk through" the substation virtually and identify hot spots without confined space entry. This spatial context transforms abstract sensor data into intuitive visual intelligence.

### Step 5: Digital Twin Builder → Fabric Graph
**Digital Twin Builder → Fabric Graph (Asset Hierarchy)**

Fabric Graph models the hierarchical relationships within substations—transformers contain windings, tap changers, and bushings; switchgear contains breakers, CTs, and PTs; cable systems connect across bays. When a bushing shows elevated temperature, graph queries identify the transformer it belongs to, the circuits it feeds, and the customers affected by potential failure. This relationship modeling supports root cause analysis and impact assessment.

### Step 6: Digital Twin / OneLake → ML Models
**Digital Twin / OneLake / Fabric Graph → ML Models (Train & Score)**

ML models train on historical equipment data to predict transformer health and remaining useful life. Key models include:
- **Health Index (HI) Scoring**: Combines DGA, thermal, electrical, and age factors into a 0-100 composite score
- **DGA Fault Classification**: Identifies fault types (arcing, overheating, partial discharge) from gas ratios using Duval Triangle and Rogers Ratio methods enhanced with ML
- **Thermal Anomaly Detection**: Identifies abnormal heating patterns indicating developing failures
- **Remaining Useful Life (RUL)**: Predicts end-of-life timing based on degradation trajectories and loading history
- **Failure Mode Prediction**: Identifies likely failure mechanisms to guide inspection and testing priorities

Models score continuously against real-time equipment data, providing living health assessments.

### Step 7: ML Models → Real-Time Dashboard / Power BI
**ML Models → Real-Time Dashboard (3D Twin Viewer) / Power BI (Asset Analytics)**

Health scores and predictions flow to visualization layers designed for different user needs. Real-Time Dashboard provides 3D digital twin views with color-coded health status overlays—engineers can rotate through substation equipment, clicking on any asset to see detailed health trending. Power BI delivers fleet-level asset analytics showing health distribution across all underground substations, high-risk equipment identification, and maintenance budget allocation optimization. This tiered visualization supports both daily operations and strategic asset management.

### Step 8: Dashboards → Activator
**Real-Time Dashboard / Power BI → Activator (Work Orders)**

Activator monitors equipment health metrics for conditions requiring action. When a transformer health index drops below 70, Activator generates a condition-based maintenance recommendation. When DGA indicates active arcing (high acetylene), Activator escalates to emergency investigation protocol. Integration with enterprise asset management (EAM) systems like SAP PM or IBM Maximo enables automatic work order generation with equipment-specific procedures and required spare parts. This automation transforms health insights into maintenance actions.

### Step 9: Real-Time Dashboard ↔ Copilot
**Real-Time Dashboard ↔ Copilot (Bi-directional)**

Engineers interact with digital twins through natural language queries. Copilot enables questions like "What is the health trend for Transformer T1 at Jurong Underground Substation over the past year?" or "Compare the DGA patterns of all 66kV transformers in underground installations." Engineers can also ask for failure analysis: "What maintenance actions resolved similar DGA patterns in other transformers?" This conversational interface makes complex equipment data accessible without specialized query skills.

### Step 10: Operations Agents → End Users
**Operations Agents (D365 Field Service) → Substation Engineers / Asset Managers / Field Technicians**

Operations Agents proactively deliver equipment intelligence to role-specific users:
- **Substation Engineers**: Technical health analysis, DGA interpretation guidance, and inspection recommendations
- **Asset Managers**: Fleet health dashboards, replacement prioritization, and capital planning inputs
- **Field Technicians**: Mobile-delivered work orders with equipment location in 3D, required PPE for confined space entry, and step-by-step maintenance procedures

Integration with D365 Field Service optimizes technician scheduling based on equipment priorities and qualification requirements.

---

## Business Outcomes Connected to Architecture

| Architecture Component | Business Outcome |
|------------------------|------------------|
| Multi-Sensor Integration (Step 1) | Complete equipment visibility combining DGA, thermal, PD, and environmental monitoring |
| 3D Digital Twin (Step 4b) | Virtual substation walkthrough eliminates 50% of confined space entries for routine inspections |
| Health Index ML Model (Step 6) | Composite scoring enables objective equipment ranking across the transformer fleet |
| RUL Prediction (Step 6) | 5-10 year life extension through optimal timing of refurbishment vs. replacement decisions |
| 3D Visualization Dashboard (Step 7) | 40% faster fault investigation through intuitive spatial context for sensor data |
| Activator Work Orders (Step 8) | 20-30% improvement in equipment availability through proactive, condition-based maintenance |
| D365 Field Service Integration (Step 10) | Optimized technician scheduling considering qualifications, travel time, and equipment priority |

---

## Confirmation

✅ This architecture diagram and storyline were generated using [use-case-underground-substation-digital-twin-architecture-explanation.md](use-case-underground-substation-digital-twin-architecture-explanation.md) as the primary source of truth.

*Document generated: March 2026*
