---
marp: true
theme: default
paginate: true
backgroundColor: #ffffff
style: |
  section {
    font-family: 'Segoe UI', sans-serif;
  }
  h1 {
    color: #0078D4;
  }
  h2 {
    color: #1e1e1e;
  }
  table {
    font-size: 0.8em;
  }
  .columns {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 1rem;
  }
---

# Water Usage Optimization for Cooling
## Real-Time Intelligence Use Case

![bg right:40% 80%](https://img.icons8.com/fluency/240/water.png)

**Industry:** Data Center Infrastructure & Energy

**Use Case ID:** DC-008

**Role of AI:** Optimization & Detection

---

# The Challenge: Data Center Water Consumption

Data centers use **millions of gallons** of water annually for cooling systems.

<div class="columns">
<div>

### 💧 Why It Matters
- Cooling towers evaporate water to remove heat
- Typical WUE: 1.8-2.0 liters per kWh
- Water costs rising globally
- ESG reporting requirements

</div>
<div>

### 📡 Real-Time Data Sources
| Source | What It Measures |
|--------|-----------------|
| Flow Meters | Makeup, blowdown rates |
| Quality Sensors | Conductivity, pH, TDS |
| Cooling Towers | Fan speed, basin levels |
| Weather | Wet bulb, humidity |
| BMS | Chiller load, temps |

</div>
</div>

> **Problem:** Without real-time visibility, leaks go undetected for weeks and blowdown wastes 15-30% more water than necessary.

---

# Real-Time Intelligence Architecture

```
📡 SENSORS      →    ⚡ EVENTSTREAM    →    🗃️ KQL DATABASE    →    📊 OUTPUTS
─────────────────────────────────────────────────────────────────────────────────
Flow Meters          • Parse JSON           • fact_water_telemetry    Dashboard
Water Quality        • Enrich with dims     • fact_cooling_telemetry  Activator
Cooling PLCs    →    • Calculate WUE   →    • dim_cooling_towers  →   Teams
Weather API          • Route by type        • 30-day retention        Power Automate
BMS System           • Anomaly detect       • 7-day hot cache         BMS Control
```

### ⏱️ End-to-End Latency: ~1 Second

| Stage | Latency | Key Function |
|-------|---------|--------------|
| Sensor → Event Hub | 100ms | Ingest telemetry |
| Eventstream | 500ms | Transform, enrich, route |
| KQL Database | 200ms | Store, index, query |
| Activator | 100ms | Trigger alerts & actions |

---

# Business Outcomes

<div class="columns">
<div>

### 💰 Financial Impact
| Metric | Improvement |
|--------|-------------|
| Water reduction | **15-30%** |
| Annual savings | **$150K-$400K** |
| Chemical costs | **↓ 10-20%** |
| Leak detection | **Hours vs. weeks** |

</div>
<div>

### 🌱 Sustainability Impact
| Metric | Target |
|--------|--------|
| WUE | 1.8 → **1.2 L/kWh** |
| Compliance | Automated permits |
| ESG reporting | Real-time data |
| Carbon footprint | Reduced pumping |

</div>
</div>

### 🎯 Key Activator Triggers
- **Leak Alert:** Makeup > evaporation + blowdown for 30+ minutes
- **Quality Alert:** Conductivity approaching treatment limits
- **Efficiency Alert:** WUE degrading below threshold

---

<!--
_class: lead
_backgroundColor: #0078D4
_color: white
-->

# Get Started with Real-Time Intelligence

### 📚 Resources
- [Microsoft Fabric Real-Time Intelligence](https://learn.microsoft.com/fabric/real-time-intelligence/)
- [Eventstream Documentation](https://learn.microsoft.com/fabric/real-time-intelligence/eventstream/)
- [KQL Database Quickstart](https://learn.microsoft.com/fabric/real-time-intelligence/create-database)

### 🚀 Next Steps
1. Connect your water sensors to Event Hub
2. Create Eventstream with transformations
3. Build KQL queries for WUE calculation
4. Configure Activator for leak detection

**Questions?** Ask Copilot: *"How do I calculate WUE in real-time?"*
