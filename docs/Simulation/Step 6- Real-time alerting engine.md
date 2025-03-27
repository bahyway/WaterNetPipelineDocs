# 📘 Water Hydraulic Pressures Simulation in Water Net Pipelines

This document outlines how to simulate hydraulic pressure in water pipe networks and build a complete system that overcomes the current limitations of tools like EPANET and WNTR. We'll build everything needed step-by-step, including live dashboards, real-time data pipelines, ML anomaly detection, multilingual metadata, graph-based querying, and a real-time alerting engine.

---

## ✅ Project Goals
- Fully simulate water pressure and detect defective pipes.
- Enhance open tools (EPANET, WNTR) with real-time, intelligent capabilities.
- Build modular systems using Docker, PostgreSQL, Python, and Gremlin.
- Visualize data using HTML dashboards and graph UIs.
- Create multilingual, optimized, and extensible water network models.
- Receive real-time alerts for abnormal pressure values or leaks.

---

## 🧰 Technology Stack
| Tool / Library            | Purpose                                                              |
|---------------------------|----------------------------------------------------------------------|
| **EPANET**                | Hydraulic network modeling (.inp files)                             |
| **WNTR**                  | Python simulations, failure analysis, resilience metrics             |
| **PostgreSQL**            | Central relational + graph-compatible storage                        |
| **Gremlin + TinkerPop**   | Query and analyze network topologies as Knowledge Graph              |
| **Dash / Plotly / Streamlit** | Dashboards and simulations in browser                        |
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

---

## 🚀 Step 1: Deploy Locally Using Docker
[...unchanged content...]

---

## 🔗 Step 5: Knowledge Graph with Gremlin
[...unchanged content...]

---

## 📣 Step 6: Real-time Alerting Engine

In this step, we set up a Python script to monitor pressure values and send real-time alerts when anomalies are detected. We use email or Telegram messages to notify administrators.

### ⚠️ Alert Configuration (example thresholds)
- Pressure < 25 m = ⚠️ Leak risk (critical)
- Pressure > 95 m = ⚠️ Overload / blockage

### 🔔 Example Python Script: Telegram Alert
```python
import requests
import wntr

TELEGRAM_TOKEN = 'your-telegram-bot-token'
CHAT_ID = 'your-chat-id'

def send_alert(msg):
    url = f"https://api.telegram.org/bot{TELEGRAM_TOKEN}/sendMessage"
    data = {"chat_id": CHAT_ID, "text": msg}
    requests.post(url, data=data)

wn = wntr.network.WaterNetworkModel("data/Net3.inp")
sim = wntr.sim.EpanetSimulator(wn)
results = sim.run_sim()

# Scan pressure for anomalies
for node in wn.junction_name_list:
    pressure = results.node['pressure'][node].mean()
    if pressure < 25:
        send_alert(f"🔴 ALERT: Low pressure at node {node} = {pressure:.2f} m")
    elif pressure > 95:
        send_alert(f"🔴 ALERT: High pressure at node {node} = {pressure:.2f} m")
```

### 📧 Optional: Email Alert Setup
You can also use the `smtplib` module in Python to send alerts via email using your mail server (SMTP).

### 📅 Schedule the Alert Engine
Use `cron` (Linux) or Task Scheduler (Windows) to run the alert script every 15 minutes.

---

With this final step, the simulation becomes a **real-time monitoring and alerting system**, capable of:
- Continuous pressure scanning
- ML-based anomaly detection
- Instant alert delivery to Telegram or Email

---

✅ All 6 foundational steps are now implemented!
Would you like to add:
- 🌍 GIS Map integration?
- 📊 Streamlit dashboards?
- 📦 Export to Neo4j or RDF?

Let me know how you want to enhance this next 🚀

