# Architecture Storyline: DERMS Real-Time Grid Integration

**Source Document:** [use-case-derms-real-time-grid-integration-architecture-explanation.md](use-case-derms-real-time-grid-integration-architecture-explanation.md)

**Diagram:** [use-case-derms-real-time-grid-integration-architecture-diagram.excalidraw](use-case-derms-real-time-grid-integration-architecture-diagram.excalidraw)

---

## Architecture Flow Summary

This architecture enables SP PowerGrid to orchestrate 10,000+ distributed energy resources (DERs) including rooftop solar, battery storage, and EV chargers as a unified virtual power plant (VPP). By combining real-time grid telemetry with AI-powered optimization, the platform achieves 15-30% reduction in peak demand, 8-12% improvement in renewable utilization, and creates the foundation for Singapore's smart grid transformation.

---

## Step-by-Step Data Flow

### Step 1: DER Data Collection
**Solar Inverters / BESS / EV Chargers / Smart Meters / Weather Stations → IoT Hub**

Solar inverter telemetry streams real-time generation, irradiance, and panel health data via IEEE 2030.5 (SEP2) protocol. Battery Energy Storage Systems (BESS) report state-of-charge (SoC), available capacity, and charge/discharge rates. EV chargers communicate session status and flexible capacity through OCPP (Open Charge Point Protocol). Smart meters provide 15-minute interval consumption data while weather stations deliver hyperlocal irradiance forecasts. This comprehensive data collection provides the situational awareness required for real-time grid balancing.

### Step 2: IoT Hub → Eventstream
**IoT Hub → Eventstream**

Azure IoT Hub aggregates telemetry from diverse DER protocols into a unified ingestion stream. Eventstream applies protocol normalization, converting heterogeneous data formats into standardized time-series events. This step handles the "protocol zoo" challenge—transforming Modbus, SunSpec, OCPP, and proprietary formats into consistent data structures that downstream analytics can process uniformly.

### Step 3: Eventstream → Eventhouse
**Eventstream → Eventhouse (KQL Database)**

Eventstream routes normalized DER telemetry to Eventhouse for sub-second analytics. The KQL database stores generation forecasts, consumption patterns, and grid frequency measurements, enabling real-time calculation of net load and available flexibility. This high-performance time-series engine supports the 4-second response requirements for frequency regulation services, processing millions of events per minute during peak solar generation hours.

### Step 4a: Eventhouse ↔ OneLake
**Eventhouse ↔ OneLake (Bi-directional)**

Eventhouse synchronizes operational data with OneLake for historical analysis and ML training. Solar generation profiles, demand patterns, and weather correlations flow to OneLake for long-term trend analysis. Reference data including DER technical specifications, grid constraints, and market rules flow back to Eventhouse. This bidirectional flow enables both operational optimization and strategic capacity planning.

### Step 4b: Eventhouse → Digital Twin Builder
**Eventhouse → Digital Twin Builder (VPP Model)**

Real-time DER status feeds Digital Twin Builder to create a living model of the virtual power plant. The digital twin visualizes aggregate capacity, geographic distribution, and real-time flexibility across Singapore's grid regions. Network operators can see available battery capacity in Jurong industrial zone or flexible EV charging load in residential estates, enabling spatially-aware dispatch decisions that respect distribution network constraints.

### Step 5: Digital Twin Builder → Fabric Graph
**Digital Twin Builder → Fabric Graph (Grid Topology)**

Fabric Graph models the electrical relationships between DERs, transformers, feeders, and substations. When dispatching stored energy, graph queries identify the optimal BESS units considering both available capacity and electrical proximity to load centers. This topology awareness prevents solutions that would violate transformer ratings or cause voltage issues on specific feeders, ensuring all VPP dispatches are electrically feasible.

### Step 6: Digital Twin / OneLake → ML Models
**Digital Twin / OneLake / Fabric Graph → ML Models (Train & Score)**

ML models train on historical generation, consumption, and weather data to predict grid conditions and optimize DER dispatch. Key models include:
- **Solar Forecast**: Predicts generation 15 minutes to 24 hours ahead using satellite imagery and historical patterns
- **Load Forecast**: Anticipates consumption curves by customer segment and grid region
- **Volt-VAR Optimization**: Determines optimal reactive power settings to maintain voltage within standards
- **VPP Dispatch Optimization**: Maximizes economic value while respecting technical constraints
- **Curtailment Minimization**: Identifies storage and demand response opportunities to absorb excess solar

Models score continuously against real-time grid conditions, generating dispatch recommendations every 4-15 seconds.

### Step 7: ML Models → Real-Time Dashboard / Power BI
**ML Models → Real-Time Dashboard / Power BI**

Optimization outputs and grid status flow to visualization layers. Real-Time Dashboard provides live views for network operations center staff monitoring system balance and DER response. Power BI delivers analytics for grid planners assessing hosting capacity, DER growth trends, and network investment needs. This visibility supports both real-time operations and long-term grid evolution planning.

### Step 8: Dashboards → Activator
**Real-Time Dashboard / Power BI → Activator (DER Commands)**

Activator translates optimization decisions into DER control commands. When peak demand approaches network limits, Activator dispatches battery discharge commands to registered BESS units. When solar generation exceeds local consumption, Activator reduces EV charging rates or initiates battery charging to absorb surplus. This closed-loop automation enables autonomous VPP operation while maintaining human oversight of system-wide decisions.

### Step 9: Real-Time Dashboard ↔ Copilot
**Real-Time Dashboard ↔ Copilot (Bi-directional)**

Grid operators interact with VPP data through natural language queries. Copilot enables questions like "What's our current aggregate battery capacity in the western region?" or "How did yesterday's cloud cover affect our solar forecast accuracy?" This conversational interface accelerates operator decision-making and supports training of new staff on complex grid balancing concepts.

### Step 10: Operations Agents → End Users
**Operations Agents → Grid Operators / DER Aggregators / Market Participants**

Operations Agents deliver role-specific insights and automation:
- **Grid Operators**: Real-time system status, constraint violations, and recommended interventions
- **DER Aggregators**: Portfolio performance, device availability, and revenue opportunity notifications
- **Market Participants**: Settlement data, flexibility service performance, and market price signals

This intelligent delivery ensures each stakeholder has the information needed for their operational and commercial decisions.

---

## Business Outcomes Connected to Architecture

| Architecture Component | Business Outcome |
|------------------------|------------------|
| Multi-Protocol Ingestion (Step 1) | Unified management of 10,000+ heterogeneous DERs through standardized data platform |
| Eventhouse Real-Time Analytics (Step 3) | 4-second response capability enables participation in frequency regulation markets |
| Digital Twin VPP Model (Step 4b) | Geographic visualization enables spatially-aware dispatch that respects network constraints |
| Fabric Graph Topology (Step 5) | Electrical feasibility validation prevents dispatch commands that would violate network ratings |
| VPP Optimization ML (Step 6) | 15-30% peak demand reduction through intelligent battery dispatch and demand response |
| Activator Closed-Loop (Step 8) | Autonomous DER dispatch with 99.9% command success rate, enabling hands-off grid balancing |
| Solar Forecast ML (Step 6) | 8-12% improvement in renewable utilization through accurate curtailment avoidance |

---

## Confirmation

✅ This architecture diagram and storyline were generated using [use-case-derms-real-time-grid-integration-architecture-explanation.md](use-case-derms-real-time-grid-integration-architecture-explanation.md) as the primary source of truth.

*Document generated: March 2026*
