# 📘 Water Hydraulic Pressures Simulation in Water Net Pipelines

This document outlines how to simulate hydraulic pressure in water pipe networks and build a complete system that overcomes the current limitations of tools like EPANET and WNTR. We'll build everything needed step-by-step, including live dashboards, real-time data pipelines, ML anomaly detection, multilingual metadata, and graph-based querying.

---

## ✅ Project Goals
- Fully simulate water pressure and detect defective pipes.
- Enhance open tools (EPANET, WNTR) with real-time, intelligent capabilities.
- Build modular systems using Docker, PostgreSQL, Python, and Gremlin.
- Visualize data using HTML dashboards and graph UIs.
- Create multilingual, optimized, and extensible water network models.

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
To create a consistent development environment, we package all dependencies into a Docker container.

### 📂 Project Structure
```
water-pressure-lab/
├── app.py                    # Main simulation script (Dash + WNTR)
├── data/Net3.inp             # EPANET model file
├── Dockerfile                # Docker build instructions
├── requirements.txt          # Python dependencies
├── README.md
```

### 🐳 Dockerfile
```dockerfile
FROM python:3.10-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
CMD ["python", "app.py"]
```

### 📦 requirements.txt
```
wntr
dash
plotly
pandas
faker
gremlinpython
psycopg2-binary
scikit-learn
```

### ▶️ Build & Run
```bash
docker build -t water-pressure-lab .
docker run -p 8050:8050 water-pressure-lab
```
Visit `http://localhost:8050` to interact with the live dashboard.

---

## 🗃️ Step 2: Multilingual PostgreSQL + Test Data

To support multilingual labels (English + Arabic) and store simulation data, we’ll create a PostgreSQL schema and a Python script using `Faker` to generate test data.

### 📄 PostgreSQL Schema
```sql
CREATE TABLE water_nodes (
  node_id TEXT PRIMARY KEY,
  type TEXT,
  x DOUBLE PRECISION,
  y DOUBLE PRECISION,
  pressure DOUBLE PRECISION,
  english_label TEXT,
  arabic_label TEXT
);

CREATE TABLE water_links (
  link_id TEXT PRIMARY KEY,
  from_node TEXT,
  to_node TEXT,
  pressure_loss DOUBLE PRECISION,
  flow DOUBLE PRECISION,
  status TEXT,
  english_label TEXT,
  arabic_label TEXT
);
```

### 🧪 Test Data Generator (Python)
```python
from faker import Faker
import random
import psycopg2

fake_en = Faker('en_US')
fake_ar = Faker('ar_EG')
Faker.seed(0)

conn = psycopg2.connect("dbname=water user=postgres password=postgres")
cursor = conn.cursor()

for _ in range(500):
    node_id = fake_en.uuid4()
    x, y = random.uniform(0, 100), random.uniform(0, 100)
    pressure = random.uniform(30, 90)
    cursor.execute("""
        INSERT INTO water_nodes (node_id, type, x, y, pressure, english_label, arabic_label)
        VALUES (%s, %s, %s, %s, %s, %s, %s)
    """, (node_id, 'JUNCTION', x, y, pressure, fake_en.name(), fake_ar.name()))

conn.commit()
cursor.close()
conn.close()
```

This script populates the `water_nodes` table with multilingual labels and random location/pressure values.

---

## 🌐 Step 3: HTML Dashboard for Live Simulation

The following example creates a Dash-based dashboard that loads a `.inp` file, runs a WNTR simulation, and displays pressure changes over time.

### 📄 app.py
```python
import wntr
import pandas as pd
from dash import Dash, html, dcc
import plotly.graph_objs as go

# Load EPANET network
wn = wntr.network.WaterNetworkModel("data/Net3.inp")
sim = wntr.sim.EpanetSimulator(wn)
results = sim.run_sim()

# Pick a sample node for visualization
node_id = wn.junction_name_list[0]
pressure_series = results.node['pressure'][node_id]

# Create Dash app
app = Dash(__name__)
app.layout = html.Div([
    html.H1(f"Pressure Over Time at Node: {node_id}"),
    dcc.Graph(
        figure=go.Figure(
            data=[go.Scatter(y=pressure_series.values, mode='lines')],
            layout=go.Layout(xaxis_title='Time Step', yaxis_title='Pressure (m)')
        )
    )
])

if __name__ == '__main__':
    app.run_server(debug=True)
```

Visit `http://localhost:8050` to view the interactive chart.

You can enhance this dashboard by:
- Switching between multiple nodes via dropdown
- Showing flow or headloss per link
- Color coding pressure levels (normal, low, critical)

---

## 🧠 Step 4: ML Anomaly Detection

We use `scikit-learn` to detect anomalies in pressure readings using Isolation Forest.

### 📄 anomaly_detector.py
```python
import wntr
from sklearn.ensemble import IsolationForest
import matplotlib.pyplot as plt

# Load and simulate network
wn = wntr.network.WaterNetworkModel("data/Net3.inp")
sim = wntr.sim.EpanetSimulator(wn)
results = sim.run_sim()

# Select node pressures
pressure_data = results.node['pressure'].T  # shape: [time, node]
model = IsolationForest(contamination=0.05)
labels = model.fit_predict(pressure_data)

# Plot anomaly detection result
anomaly_times = pressure_data.index[labels == -1]
plt.figure(figsize=(10, 5))
plt.plot(pressure_data.index, pressure_data.mean(axis=1), label='Avg Pressure')
plt.scatter(anomaly_times, pressure_data.loc[anomaly_times].mean(axis=1), color='red', label='Anomalies')
plt.title("Anomaly Detection on Node Pressure")
plt.xlabel("Time Step")
plt.ylabel("Pressure (m)")
plt.legend()
plt.tight_layout()
plt.show()
```

This tool detects outliers in pressure trends across all nodes and flags sudden drops or unexpected spikes as anomalies.

---

Next Steps:
- 🔗 Step 5: Knowledge Graph with Gremlin
- 📣 Step 6: Real-time alerting engine

Let me know when you're ready to proceed! 🚀

