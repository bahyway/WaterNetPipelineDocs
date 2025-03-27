# 📘 Water Hydraulic Pressures Simulation in Water Net Pipelines

This document outlines how to simulate hydraulic pressure in water pipe networks and build a complete system that overcomes the current limitations of tools like EPANET and WNTR. We'll build everything needed step-by-step, including live dashboards, real-time data pipelines, ML anomaly detection, multilingual metadata, graph-based querying, a real-time alerting engine, GIS map integration, and Streamlit dashboards.

---

## ✅ Project Goals
- Fully simulate water pressure and detect defective pipes.
- Enhance open tools (EPANET, WNTR) with real-time, intelligent capabilities.
- Build modular systems using Docker, PostgreSQL, Python, and Gremlin.
- Visualize data using HTML dashboards and graph UIs.
- Create multilingual, optimized, and extensible water network models.
- Receive real-time alerts for abnormal pressure values or leaks.
- Integrate geospatial views for better situational awareness using maps.
- Create intuitive Streamlit dashboards for non-technical users.

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
| ⛔ No simplified UI for end users   | ✅ Streamlit-based dashboards for stakeholders                      |

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
[...unchanged content...]

---

## 📊 Step 8: Streamlit Dashboards

Streamlit offers a fast way to build user-friendly dashboards. The following example loads pressure data from PostgreSQL and visualizes it interactively.

### 📄 streamlit_app.py
```python
import streamlit as st
import pandas as pd
import psycopg2
import matplotlib.pyplot as plt

# Connect to PostgreSQL
def load_data():
    conn = psycopg2.connect("dbname=water user=postgres password=postgres")
    df = pd.read_sql("SELECT * FROM water_nodes", conn)
    return df

st.title("💧 Water Pressure Dashboard")
data = load_data()

selected_node = st.selectbox("Select Node:", data['english_label'])
record = data[data['english_label'] == selected_node].iloc[0]

st.metric(label="Pressure (m)", value=f"{record['pressure']:.2f}")
st.map(pd.DataFrame({"lat": [record['y']], "lon": [record['x']]}))

# Optional chart
st.subheader("Pressure Threshold Zones")
fig, ax = plt.subplots()
ax.bar([record['english_label']], [record['pressure']], color='red' if record['pressure'] < 30 else 'green')
ax.axhline(30, color='orange', linestyle='--')
st.pyplot(fig)
```

### ▶️ To Run:
```bash
streamlit run streamlit_app.py
```
Then visit `http://localhost:8501` in your browser.

---

With Streamlit dashboards, we provide:
- A simple UI for monitoring water pressure per node
- Embedded map and metrics
- Threshold visualization with color feedback

---

✅ All 8 foundational steps are now implemented!
Would you like to add:
- 📦 Export to Neo4j or RDF?
- 🎯 Automated optimization for pump scheduling?
- 📁 Save/export reports in PDF or Excel?

Let me know what you’d like to build next! 🚀

