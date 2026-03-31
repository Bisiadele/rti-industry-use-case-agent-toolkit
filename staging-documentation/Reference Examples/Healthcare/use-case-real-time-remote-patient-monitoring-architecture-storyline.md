# Architecture Storyline: Real-Time Remote Patient Monitoring

**Source Document:** [use-case-real-time-remote-patient-monitoring-architecture-explanation.md](use-case-real-time-remote-patient-monitoring-architecture-explanation.md)

**Diagram:** [use-case-real-time-remote-patient-monitoring-architecture-diagram.excalidraw](use-case-real-time-remote-patient-monitoring-architecture-diagram.excalidraw)

---

## Architecture Flow Summary

This architecture enables healthcare organizations to continuously monitor patients at home or in care facilities, detecting health deteriorations within minutes rather than hours or days. By combining real-time streaming data from wearable devices with AI-powered anomaly detection and predictive models, care teams receive actionable alerts that enable early intervention—reducing hospital readmissions by 15-25% and generating $2.5M-$5M in annual savings per health system.

---

## Step-by-Step Data Flow

### Step 1: Device Data Collection
**Wearable Devices & Smart Monitors → IoT Hub**

Patients wear connected health devices that continuously capture vital signs: heart rate monitors transmit ECG data every 5-15 seconds, pulse oximeters measure blood oxygen saturation every 30 seconds, and continuous glucose monitors report levels every 5 minutes. Smart blood pressure cuffs and scales capture periodic measurements. These devices transmit data securely through cellular or Wi-Fi connections to Azure IoT Hub, which serves as the central ingestion point for all device telemetry.

### Step 2: IoT Hub to Eventstream
**IoT Hub → Eventstream**

Azure IoT Hub forwards the device telemetry streams to Microsoft Fabric Eventstream for real-time processing. This handoff occurs with sub-second latency, ensuring that time-sensitive vital sign data reaches the analytics pipeline immediately. Eventstream establishes a reliable, ordered stream of events that can be processed, transformed, and routed to multiple downstream consumers simultaneously.

### Step 3: Eventstream to Eventhouse
**Eventstream → Eventhouse (KQL Database)**

Eventstream processes incoming data and writes it to Eventhouse for time-series storage and analysis. During this step, the system normalizes data from different device manufacturers (converting varied units, sampling rates, and payload formats into a unified schema), validates readings to filter out corrupt or out-of-range values, and enriches events with patient identifiers and device metadata. The Eventhouse KQL database efficiently stores billions of vital sign readings with sub-second query performance.

### Step 4a: Eventhouse to OneLake (Bi-directional)
**Eventhouse ↔ OneLake**

The system maintains bi-directional data flow between Eventhouse and OneLake. Real-time and recent vital signs in Eventhouse are periodically persisted to OneLake for long-term storage, compliance archiving, and historical analysis. Conversely, reference data, historical baselines, and ML training datasets stored in OneLake are made available to Eventhouse through shortcuts, enabling real-time queries to incorporate historical context without data duplication.

### Step 4b: Eventhouse to Digital Twin Builder
**Eventhouse → Digital Twin Builder**

Current vital signs from Eventhouse feed into Digital Twin Builder, which maintains a live digital representation of each patient's physiological state. The patient health twin incorporates real-time vital signs, active diagnoses, current medications, and risk factors. Device twins track equipment status including battery levels, connectivity, and calibration. This digital model enables simulation, "what-if" analysis, and provides context for anomaly detection.

### Step 5: Digital Twin Builder to Fabric Graph
**Digital Twin Builder → Fabric Graph**

The Digital Twin Builder populates Fabric Graph with relationship data critical for care coordination. The graph models connections between patients and their assigned devices, patients and their care team members (primary physicians, specialists, nurses, care coordinators), patients and their active medical conditions, and facilities and their patient populations. These relationships enable intelligent routing of alerts and coordinated care responses.

### Step 6: OneLake/Fabric Graph to ML Models
**OneLake + Fabric Graph → ML Models (Train & Score)**

Machine learning models are trained using historical vital signs and outcomes data from OneLake, combined with relationship context from Fabric Graph. The deterioration risk model learns each patient's individual baseline patterns and predicts the probability of clinical deterioration within the next 4-24 hours. Anomaly detection models identify readings that deviate from expected patterns considering time of day, activity level, and recent medication changes. Models are retrained periodically as new outcome data becomes available.

### Step 7: ML Models to Real-Time Dashboard
**ML Models → Real-Time Dashboard**

Trained ML models score each incoming vital sign reading in near real-time, generating risk scores and anomaly flags. These scores flow to Real-Time Dashboards in Microsoft Fabric, where care team members see a live patient monitoring board with color-coded risk levels, a prioritized alert queue, and detailed individual patient views showing all vitals, trends, and care plan status. Dashboards refresh continuously, providing an always-current operational picture.

### Step 8: Dashboards to Activator
**Real-Time Dashboard + Power BI → Activator**

When risk scores exceed configured thresholds or anomalies are detected, Activator triggers automated responses. Critical alerts (heart rate >120, SpO2 <90%, weight gain >3 lbs in 24 hours for heart failure patients) immediately notify appropriate care team members via Teams, email, or SMS. Escalation workflows ensure that unacknowledged alerts reach supervisors within 15 minutes. Patient engagement actions send automated reminders for missed readings or educational content triggered by specific conditions.

### Step 9: Real-Time Dashboard to Copilot (Bi-directional)
**Real-Time Dashboard ↔ Copilot**

Clinicians interact with dashboards using natural language through Copilot integration. A nurse can ask "Which of my patients had the most alerts overnight?" and receive an immediate summary. A physician reviewing an alert can request "Summarize this patient's vital sign trends over the past week." Copilot also assists with documentation, drafting care coordination notes based on the patient's current status and recent interventions.

### Step 10: Data Agents to End Users
**Data Agents → End Users (Physicians, Nurses, Care Coordinators)**

Data Agents automate routine analytical tasks and deliver insights proactively. The Morning Report Agent generates a daily summary of overnight alerts, critical patients, and pending follow-ups—delivered to care coordinators before their shift begins. The Compliance Agent identifies patients with decreasing engagement and recommends outreach strategies. The Outcomes Agent tracks clinical outcomes and correlates them with monitoring interventions, helping continuously improve the program.

---

## Business Outcomes Connected to Architecture

| Architecture Component | Business Outcome |
|------------------------|------------------|
| IoT Hub + Eventstream (Steps 1-2) | Sub-60-second data ingestion enables real-time monitoring rather than delayed batch processing |
| Eventhouse (Step 3) | Efficient time-series storage supports analysis across billions of readings with sub-second query response |
| Digital Twin Builder (Step 4b) | Personalized baselines improve anomaly detection accuracy, reducing false positive alerts by 40% |
| ML Models (Steps 6-7) | Predictive deterioration scoring enables 4-8 hours earlier intervention compared to manual monitoring |
| Activator (Step 8) | Automated alerting reduces response time from hours to minutes, directly impacting patient outcomes |
| Copilot + Data Agents (Steps 9-10) | AI assistance reduces clinician administrative burden by 30-40%, allowing focus on patient care |
| End-to-End Architecture | 15-25% reduction in hospital readmissions, $2.5M-$5M annual savings per health system |

---

## Confirmation

✅ This architecture diagram and storyline were generated using [use-case-real-time-remote-patient-monitoring-architecture-explanation.md](use-case-real-time-remote-patient-monitoring-architecture-explanation.md) as the primary source of truth.

*Document generated: March 2026*
