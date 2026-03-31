# Architecture Storyline: Grid Surveillance & Intelligent Fault Location

**Source Document:** [use-case-grid-surveillance-fault-location-architecture-explanation.md](use-case-grid-surveillance-fault-location-architecture-explanation.md)

**Diagram:** [use-case-grid-surveillance-fault-location-architecture-diagram.excalidraw](use-case-grid-surveillance-fault-location-architecture-diagram.excalidraw)

---

## Architecture Flow Summary

This architecture enables SP PowerGrid to locate underground cable faults within ±5 meters accuracy and classify fault types in real-time, reducing average restoration time from 4-8 hours to under 2 hours. By combining Traveling Wave Fault Location (TWFL), DTS fiber thermal profiling, and DAS acoustic sensing with AI-powered pattern recognition, the platform transforms fault response from labor-intensive cable tracing to precision-guided repair dispatch.

---

## Step-by-Step Data Flow

### Step 1: Fault Detection Sensor Data Collection
**TWFL Devices / Digital Protective Relays / PMUs / DTS Fiber / DAS Acoustic / SCADA → IoT Hub / Event Hub**

Traveling Wave Fault Location (TWFL) devices capture sub-microsecond voltage transients when faults occur, measuring arrival time differences at multiple points to triangulate fault position. Digital protective relays (IEC 61850) provide fault current waveforms, sequence quantities, and operating times. Phasor Measurement Units (PMUs) and Fault Passage Indicators (FPIs) provide wide-area grid visibility at 30-60 samples per second. DTS fiber optic systems detect thermal anomalies from cable faults. DAS (Distributed Acoustic Sensing) fiber detects fault sounds and vibrations. SCADA/Spectrum Power 7 integration provides network topology and switching status context. This multi-modal fault sensing ensures comprehensive detection regardless of fault type.

### Step 2: IoT Hub / Event Hub → Eventstream
**IoT Hub / Event Hub → Eventstream (Fault Waveforms)**

Azure IoT Hub and Event Hub aggregate high-bandwidth fault data, handling the burst telemetry that occurs during fault events (milliseconds of waveform data captured at MHz sampling rates). Eventstream applies time-stamp alignment critical for TWFL calculations—nanosecond precision required for accurate fault location. The stream processing layer also filters normal operating data from fault events, ensuring storage efficiency while preserving all fault-related measurements.

### Step 3: Eventstream → Eventhouse
**Eventstream → Eventhouse (Time-Series Fault DB)**

Eventstream routes fault waveforms and processed measurements to Eventhouse for high-performance time-series storage. The KQL database maintains a searchable archive of all fault events with associated waveforms, protection relay sequences, and SCADA topology snapshots. This enables both real-time fault analysis and historical pattern matching—comparing current fault signatures against decades of prior events to identify recurring issues or vulnerable cable sections.

### Step 4a: Eventhouse ↔ OneLake
**Eventhouse ↔ OneLake (Historical Faults - Bi-directional)**

Eventhouse synchronizes fault data with OneLake for ML training and long-term archival. Historical fault records including location accuracy feedback (comparing predicted vs. actual locations found during repair) flow to OneLake to continuously improve ML models. Reference data including cable impedance values, network models, and fault classification rules flow back to Eventhouse. This feedback loop enables the system to learn from each fault, improving accuracy over time.

### Step 4b: Eventhouse → Digital Twin Builder
**Eventhouse → Digital Twin Builder (Cable Route Geometry)**

Fault location data feeds Digital Twin Builder to visualize predicted fault positions on cable route maps. The digital twin integrates GIS data (cable paths, manhole locations, joint positions) with calculated fault distances, displaying fault location estimates on actual underground infrastructure. Engineers can see not just "meter 1,247" but the specific section between Manhole 23 and Manhole 24, along a route that runs under Orchard Road. This spatial context transforms abstract distance calculations into actionable repair coordinates.

### Step 5: Digital Twin Builder → Fabric Graph
**Digital Twin Builder → Fabric Graph (Network Topology)**

Fabric Graph models the electrical topology of the underground network—which cables connect which substations, where joints and accessories exist, and how protection zones overlap. When multiple fault indicators fire, graph queries correlate signals to identify the faulted section while filtering spurious indications from healthy circuits. This topology awareness prevents mis-identification errors that occur when parallel cables or back-feed paths confuse impedance-based methods.

### Step 6: Digital Twin / OneLake → ML Models
**Digital Twin / OneLake / Fabric Graph → ML Models (Train & Score)**

ML models train on historical fault data to enhance location accuracy and enable fault classification. Key models include:
- **TWFL Algorithm Enhancement**: Improves traveling wave arrival time detection using ML-based noise filtering
- **Fault Classification**: Identifies fault type (single line-to-ground, line-to-line, three-phase) from current waveforms
- **Severity Scoring**: Assesses fault energy to predict cable damage extent
- **Pattern Matching**: Compares current fault signatures to historical database to identify similar events
- **Route Optimization**: Recommends optimal repair crew approach paths considering traffic and underground access points

Models score in real-time during fault events, providing location and classification within seconds of fault occurrence.

### Step 7: ML Models → Real-Time Dashboard / Power BI
**ML Models → Real-Time Dashboard (Fault Location Map) / Power BI (Fault Analytics)**

Fault locations and classifications flow to visualization layers optimized for different response phases. Real-Time Dashboard provides the live fault location map displayed in the control room during outage events—showing predicted fault position, confidence radius, affected circuits, and impacted customers. Power BI delivers fault analytics for post-event analysis—fault frequency by route section, cable age correlation, and environmental factor analysis. This tiered visualization supports both emergency response and reliability improvement planning.

### Step 8: Dashboards → Activator
**Real-Time Dashboard / Power BI → Activator (Crew Dispatch)**

Activator monitors fault events and automatically initiates response workflows. When a fault is detected and located, Activator generates a dispatch package including fault coordinates, affected circuit information, estimated customer impact, and recommended repair materials. Integration with D365 Field Service or workforce management systems enables automatic crew assignment based on crew locations, qualifications (cable jointing certification), and equipment availability (cable test van, fault location equipment). This automation accelerates the critical path from fault detection to crew arrival on scene.

### Step 9: Real-Time Dashboard ↔ Copilot
**Real-Time Dashboard ↔ Copilot (Fault Analysis - Bi-directional)**

Control room operators and protection engineers interact with fault data through natural language. Copilot enables questions like "What similar faults have occurred on this cable circuit in the past 5 years?" or "Show me the protection relay sequence for the fault at 14:23 today." During prolonged outages, operators can ask "What's the fastest restoration option considering available switching?" This conversational interface accelerates fault investigation during high-pressure restoration events.

### Step 10: Operations Agents → End Users
**Operations Agents (Mobile Field App) → Control Room Ops / Protection Engineers / Cable Jointers / Restoration Teams**

Operations Agents deliver role-specific fault information to field and office personnel:
- **Control Room Operators**: Real-time fault status, restoration progress tracking, customer impact metrics
- **Protection Engineers**: Fault waveforms, relay operating sequences, coordination analysis
- **Cable Jointers**: GPS coordinates for fault location, nearest access point, required materials and equipment
- **Restoration Teams**: Switching sequences, safety isolation points, estimated repair duration

Mobile-optimized delivery ensures field crews have accurate information regardless of cellular connectivity in underground locations.

---

## Business Outcomes Connected to Architecture

| Architecture Component | Business Outcome |
|------------------------|------------------|
| TWFL + DTS + DAS Sensing (Step 1) | Multi-modal fault detection achieves 95%+ fault detection rate with ±5 meter location accuracy |
| Sub-Microsecond Time Alignment (Step 2) | Precise timestamp correlation enables traveling wave calculations required for accuracy |
| Eventhouse Fault Archive (Step 3) | Searchable fault history enables pattern identification and recurring fault investigation |
| Digital Twin Route Mapping (Step 4b) | Fault coordinates displayed on actual cable routes reduce search time from hours to minutes |
| Fabric Graph Topology (Step 5) | Network-aware fault location eliminates false positives from parallel path confusion |
| ML Classification (Step 6) | Automatic fault type identification enables appropriate repair resource mobilization |
| Activator Crew Dispatch (Step 8) | Automated dispatch packages reduce fault-to-crew-arrival time by 30-40% |
| Overall Restoration Impact | Average restoration time reduced from 4-8 hours to under 2 hours, saving $2M-$8M annually |

---

## Confirmation

✅ This architecture diagram and storyline were generated using [use-case-grid-surveillance-fault-location-architecture-explanation.md](use-case-grid-surveillance-fault-location-architecture-explanation.md) as the primary source of truth.

*Document generated: March 2026*
