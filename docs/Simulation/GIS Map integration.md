# 📘 Water Hydraulic Pressures Simulation in Water Net Pipelines

This document outlines how to simulate hydraulic pressure in water pipe networks and build a complete system that overcomes the current limitations of tools like EPANET and WNTR. We'll build everything needed step-by-step, including live dashboards, real-time data pipelines, ML anomaly detection, multilingual metadata, graph-based querying, a real-time alerting engine, and GIS map integration.

---

## ✅ Project Goals
- Fully simulate water pressure and detect defective pipes.
- Enhance open tools (EPANET, WNTR) with real-time, intelligent capabilities.
- Build modular systems using Docker, PostgreSQL, Python, and Gremlin.
- Visualize data using HTML dashboards and graph UIs.
- Create multilingual, optimized, and extensible water network models.
- Receive real-time alerts for abnormal pressure values or leaks.
- Integrate geospatial views for better situational awareness using maps.

---

## 🧰 Technology Stack
| Tool / Library            | Purpose                                                              |
|---------------------------|----------------------------------------------------------------------|
| **EPANET**                | Hydraulic network modeling (.inp files)                             |
| **WNTR**                  | Python simulations, failure analysis, resilience metrics             |
| **PostgreSQL + PostGIS**  | Central storage + geospatial query support                           |
| **Gremlin + TinkerPop**   | Query and analyze network topologies as Knowledge Graph              |
| **Dash / Plotly / Streamlit** | Dashboards and simulations in browser                        |
| **Leaflet.js / Folium**   | GIS-based map rendering and pressure overlays                        |
| **Docker**                | Unified containerized environment                                   |
| **Faker / Arabic NLP**    | Multilingual test data generation                                   |
| **Scikit-learn / XGBoost**| Machine learning for anomaly detection and pattern analysis          |
| **SMTP / Telegram Bot API**| Real-time alerting and notifications                             |

---

## 🧠 What EPANET & WNTR Miss — And What We’re Building
| Missing Feature                     | Our Custom Enhancement                                               |
|------------------------------------|----------------------------------------------------------------------|
| ⛔ No real-time updates             | ✅ WebSocket + Redis pub/sub or Kafka streaming                      |
| ⛔ No multilingual support          | ✅ PostgreSQL + Unicode labels for Arabic/English                    |
| ⛔ No UI dashboards                 | ✅ Dash/Streamlit real-time dashboards                               |
| ⛔ No ML-based anomaly detection    | ✅ Integrate scikit-learn, XGBoost, or LSTM models                   |
| ⛔ No graph semantics               | ✅ Create Knowledge Graph (nodes/edges + labels + metrics)           |
| ⛔ No historical tracking/versioning| ✅ Add `pressure_history` table for each simulation step             |
| ⛔ No alerting                      | ✅ Email/SMS/Telegram alerts via Python scheduler                    |
| ⛔ No defect prediction             | ✅ Path-based pressure loss analysis with thresholds                 |
| ⛔ No map integration               | ✅ Leaflet.js + Folium-based geospatial visualizations               |

---

## 🚀 Step 1: Deploy Locally Using Docker
[...unchanged content...]

---

## 🔗 Step 5: Knowledge Graph with Gremlin
[...unchanged content...]

---

## 📣 Step 6: Real-time Alerting Engine
[...unchanged content...]

---

## 🌍 Step 7: GIS Map Integration

To provide a spatial understanding of water pressure behavior, we use Leaflet.js (via Folium) to overlay pressure nodes and pipelines on an interactive map.

### 📄 gis_map.py (Python + Folium)
```python
import folium
import pandas as pd
import psycopg2

# Load node coordinates and pressures from PostgreSQL
conn = psycopg2.connect("dbname=water user=postgres password=postgres")
df = pd.read_sql("SELECT x, y, pressure, english_label FROM water_nodes", conn)

# Create a Folium map
m = folium.Map(location=[df.y.mean(), df.x.mean()], zoom_start=12)

# Add pressure nodes as markers
for _, row in df.iterrows():
    color = 'green' if row['pressure'] >= 30 else 'red'
    folium.CircleMarker(
        location=(row['y'], row['x']),
        radius=5,
        popup=f"{row['english_label']}<br>Pressure: {row['pressure']:.1f} m",
        color=color,
        fill=True
    ).add_to(m)

m.save("pressure_map.html")
```

### 🗺️ Output
This script generates a file called `pressure_map.html` that can be opened in any web browser. Nodes will appear color-coded by pressure level (red for low pressure).

---

With GIS map integration, we now have:
- Spatial insight into pipeline conditions
- Visual confirmation of pressure distribution
- The ability to export map views and integrate into dashboards

---

✅ All 7 foundational steps are now implemented!
Would you like to add:
- 📊 Streamlit dashboards?
- 📦 Export to Neo4j or RDF?
- 🎯 Automated optimization for pump scheduling?

Let me know how you want to enhance this next 🚀

