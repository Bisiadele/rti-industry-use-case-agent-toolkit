# Written Architecture Explanation: UPS and Power Distribution Anomaly Detection

## Use Case: UPS and Power Distribution Anomaly Detection

**Role of AI:** Anomaly Detection, Prediction

**Primary Data Sources:** UPS battery telemetry, transfer switch status, PDU load balancing, harmonic distortion measurements, power quality monitors

**Data Type Produced:** Power quality events, battery health metrics, transfer logs, anomaly alerts, predictive maintenance recommendations

**End Users:** Electrical engineers, NOC operators, Reliability engineers, Data center operations

---

## 1. Data Sources

UPS and Power Distribution Anomaly Detection monitors the critical power infrastructure that ensures continuous operation of data center IT equipment. This data must be captured in real-time because power anomalies can cause immediate equipment damage or downtime, and early detection of degradation prevents catastrophic failures.

### Primary data sources include:

- **UPS Systems:** Report comprehensive telemetry including input/output voltage and current, frequency, power factor, load percentage, battery voltage and temperature, charger status, and inverter health. Modern UPS systems provide data at 1-second intervals or faster during anomalies.

- **Battery Management Systems (BMS):** Monitor individual battery string health including cell voltages, internal resistance, temperature, and discharge test results. Battery degradation is a leading cause of UPS failures.

- **Automatic Transfer Switches (ATS):** Report source availability (utility A, utility B, generator), current active source, transfer counts, and transfer times. ATS data reveals utility reliability and switch performance.

- **Static Transfer Switches (STS):** Provide sub-cycle transfer times between redundant UPS outputs, transfer event logs, and load sharing status for critical loads.

- **Power Distribution Units (PDUs):** Report branch circuit loading, voltage, current, power factor, and neutral current. Overloaded circuits and imbalanced loads create risk.

- **Power Quality Meters:** Capture voltage sags, swells, transients, harmonic distortion, and power factor at critical monitoring points throughout the distribution path.

- **Generator Systems:** Report fuel levels, coolant temperature, oil pressure, battery condition, and run-hour counters for backup power readiness assessment.

- **Switchgear and Breakers:** Provide status (open/closed/tripped), fault current data, and trip event logs for medium and low-voltage distribution.

### Business signals represented:

- Battery capacity degradation indicates replacement planning needs
- Power quality events correlate with IT equipment errors and failures
- Transfer switch performance affects failover reliability
- Load imbalances increase single points of failure risk

---

## 2. Analyze & Transform

Data ingestion and processing must handle high-frequency power system data while detecting anomalies in real-time.

### Eventstream

Eventstream captures and processes power system telemetry:

- **Protocol adapters** connect to UPS systems via SNMP, Modbus TCP, and proprietary monitoring protocols through edge gateways
- **High-frequency capture** preserves sub-second power quality events that would be lost with slower sampling
- **Data normalization** standardizes readings from different UPS vendors (Schneider Electric, Vertiv, Eaton) into a unified schema
- **Event detection** identifies power quality events (sags, swells, transients) and generates discrete event records
- **Contextual enrichment** adds equipment metadata, location, and criticality tier to each reading

### Data Factory

Data Factory manages reference data and historical context:

- **Equipment asset database** with manufacturer specifications, installation dates, and warranty status
- **Maintenance records** documenting battery replacements, firmware updates, and service events
- **Design documentation** including single-line diagrams, redundancy configurations, and capacity ratings
- **Historical event data** from building management and ticketing systems for correlation

### Eventhouse

Eventhouse stores power system data with appropriate granularity:

- **High-resolution storage** retains 1-second data for recent events (7 days) enabling detailed post-incident analysis
- **Downsampled retention** maintains 1-minute averages for 90 days and hourly averages for multi-year trending
- **Event tables** store discrete power quality events with full waveform captures for forensic analysis
- **Materialized views** pre-calculate equipment health scores and reliability metrics

### OneLake

OneLake enables integration with broader data center operations:

- **Incident correlation** links power events to IT system alerts in the CMDB
- **Capacity planning** makes load data available for infrastructure planning workloads
- **Compliance reporting** provides power quality data for SLA documentation

---

## 3. Model & Contextualize

Modeling creates a comprehensive understanding of power system topology and health.

### Digital Twin Builder

Digital Twin Builder models the entire power distribution chain:

- **Electrical topology** represents: utility feeds → main switchgear → UPS systems → PDUs → rack circuits → IT equipment
- **Redundancy relationships** map N+1, 2N, and 2N+1 configurations showing how equipment failure affects load availability
- **Load criticality** classifies circuits by business impact (Tier 1 critical, Tier 2 important, Tier 3 standard)
- **Capacity tracking** monitors rated vs. actual load at each distribution point

### Fabric Graph

Graph relationships enable sophisticated power system analysis:

- Trace the complete power path from utility to server for any piece of equipment
- Identify all loads affected by a specific UPS or PDU failure
- Map redundant power paths to verify single-point-of-failure elimination
- Connect equipment failures to downstream IT impact for business context

### Anomaly Detection

Built-in anomaly detection models identify power system issues:

- **Voltage anomalies** detect sags, swells, and transients outside acceptable ranges
- **Load anomalies** flag unexpected changes in power consumption patterns
- **Battery degradation** identifies capacity decline through discharge test analysis and impedance trends
- **Harmonic anomalies** detect increases in total harmonic distortion indicating equipment issues
- **Transfer anomalies** flag ATS/STS transfers outside normal operating windows

### Contextual Enrichment

Real-time calculations provide operational context:

- **UPS efficiency:** Output power ÷ Input power, indicating equipment health
- **Battery runtime:** Estimated backup time based on current load and measured capacity
- **Load balance score:** Variance in loading across redundant paths
- **Reliability index:** Calculated availability based on equipment age, maintenance history, and observed performance

---

## 4. Train & Score

Machine learning models predict failures and optimize power system operations.

### Feature Engineering

Key features extracted from power system data:

- **UPS health features:** Efficiency trend, battery impedance change, inverter temperature
- **Load features:** Peak load, average load, load growth rate, daily patterns
- **Power quality features:** Sag/swell frequency, transient count, harmonic distortion levels
- **Environmental features:** Ambient temperature, humidity (affects battery performance)
- **Temporal features:** Equipment age, time since last maintenance, operating hours
- **Event history:** Past failures, near-misses, maintenance interventions

### Model Types

Multiple models address different prediction and detection objectives:

- **Battery failure prediction:** Survival analysis models predict remaining useful life based on impedance trends, discharge test results, and operating conditions
- **Power quality classification:** Neural network classifiers categorize power quality events and predict their source (utility, internal, load-induced)
- **Load forecasting:** Time-series models predict load growth to support capacity planning
- **Anomaly detection ensemble:** Multiple algorithms (Isolation Forest, LSTM Autoencoder, One-Class SVM) vote on whether current behavior is anomalous

### Real-Time Scoring

Models score continuously for proactive maintenance:

- **Health scores** update hourly for each UPS, battery string, and major distribution component
- **Failure probability** calculated daily for equipment nearing end-of-life or showing degradation
- **Anomaly scores** computed in real-time as telemetry streams arrive
- **Risk prioritization** ranks equipment by failure probability and business impact

---

## 5. Visualize & Act

Insights drive proactive maintenance and rapid incident response through visualization and automation.

### Power BI

Power BI provides strategic views for planning and review:

- **Fleet health dashboard:** Status of all UPS systems with health scores, capacity utilization, and maintenance due dates
- **Reliability reporting:** MTBF, MTTR, and availability metrics for SLA compliance documentation
- **Capacity planning:** Load growth trends and headroom analysis by distribution path
- **Capital planning:** Equipment replacement forecasts based on age and predicted failure risk

### Real-Time Dashboards

Real-Time Dashboards enable 24/7 operational monitoring:

- **Power system overview:** Single-line diagram with live status of all major components
- **Alarm panel:** Prioritized list of active alarms and anomalies with severity indicators
- **UPS detail view:** Individual UPS status including input/output parameters, battery health, and load
- **Event timeline:** Chronological view of power events with drill-down to waveform captures

### Querysets

KQL querysets support incident investigation and analysis:

- Correlate power quality events with IT system alerts to confirm causation
- Analyze battery performance trends across the fleet to identify procurement patterns
- Compare UPS efficiency across different manufacturers and configurations
- Investigate root cause of unexpected load changes

### Activator

Activator automates response to power system events:

- **Critical alerts:** Immediately page on-call engineers for UPS alarms, utility failures, or generator starts
- **Warning notifications:** Email facilities team for degraded conditions requiring attention within 24 hours
- **Automated workflows:** Create maintenance tickets for predicted failures, trigger load shedding for overload conditions
- **Escalation rules:** Automatically escalate unacknowledged critical alarms after defined intervals

---

## 6. Get Assisted & Interact

AI assistance helps operators quickly understand and respond to power system conditions.

### AI-Powered Ontology

The digital twin ontology enables intuitive exploration:

- Query "What is the status of UPS-3A?" for instant health summary
- Ask "Which servers would be affected if PDU-B2 failed?" for impact analysis
- Request "Show me all battery strings due for replacement this quarter" for maintenance planning

### Data Agents

Data Agents automate routine power system management:

- **Daily health reports** summarizing equipment status, anomalies detected, and recommended actions
- **Incident investigation agents** automatically gather relevant data when alarms trigger
- **Maintenance planning agents** generate work orders based on predicted failures and schedules
- **Compliance agents** prepare power quality reports for regulatory and customer requirements

### Copilot

Copilot assists operators and engineers with power system management:

- **Natural language queries:** "Why did UPS-2B transfer to battery last night?"
- **Troubleshooting assistance:** "Help me diagnose the voltage sag alarm on PDU-C1"
- **Report generation:** "Create a power quality report for Building A for December"
- **What-if analysis:** "What would happen to our redundancy if UPS-1A failed during maintenance?"

---

## Business Outcomes

UPS and Power Distribution Anomaly Detection protects data center uptime and reduces operational costs:

| Outcome | Impact |
|---------|--------|
| Prevented downtime | Avoid $750,000-$3,000,000 annual losses from power-related outages |
| Extended equipment life | 15-25% longer UPS and battery service life through optimized operation |
| Reduced emergency repairs | 40-60% fewer emergency service calls through predictive maintenance |
| Improved reliability | Increase power system availability from 99.9% to 99.99% |
| Faster incident response | Reduce mean time to detect (MTTD) from hours to minutes |
| Optimized maintenance | Shift from time-based to condition-based maintenance, reducing costs 20-30% |
| Compliance documentation | Automated power quality records for SLA and regulatory requirements |

---

*Document generated: February 2026*
*Use Case Reference: DC-004*
