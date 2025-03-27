# 📘 Water Hydraulic Pressures Simulation in Water Net Pipelines

This document covers tools, setup steps, demo simulations, HTML integration, database schema, test data, and ongoing Q&A for simulating hydraulic pressure in water pipe networks. It supports defect detection and anomaly monitoring using EPANET, Python, PostgreSQL, and Gremlin-based Knowledge Graph visualization.

---

## ✅ Purpose
- Simulate hydraulic pressure in water networks
- Detect defective or leaking pipelines based on pressure variations (high/low)
- Use open-source tools with PostgreSQL and Gremlin
- Build a Knowledge Graph of the water network
- Enable ML/AI-based analysis and real-time visual feedback
- Follow step-by-step simulations to trace pressure between stations and detect defective segments
- Fill in the missing pieces from WNTR/EPANET to create smarter simulations

---

## 🧰 Tools Used

| Tool                    | Description                                                                 |
|-------------------------|-----------------------------------------------------------------------------|
| **EPANET**              | Public domain water distribution system simulator                            |
| **WNTR (Python)**       | Water network modeling library that supports hydraulic failure simulation   |
| **PostgreSQL**          | Central database to store nodes, edges, simulation outputs, metadata         |
| **Apache TinkerPop**    | Graph computing framework used to model pipeline network                    |
| **Gremlin-Python**      | Query and manipulate the water network graph from Python                    |
| **Docker**              | Containerization of entire stack                                            |
| **Dash Cytoscape**      | Python-based graph visualization for pipelines and pressure flows           |

---

## 🔁 Workflow Overview
1. Model network using EPANET and generate `.inp`
2. Run simulation using WNTR (Python)
3. Store node/edge results into PostgreSQL
4. Convert PostgreSQL tables to TinkerPop-compatible graph
5. Visualize with Dash Cytoscape or query with Gremlin
6. Analyze pressure deviations and detect pipe defects (e.g., between stations A → B → C)
7. Extend with custom modules to overcome EPANET/WNTR limitations

---

## 🧠 What EPANET & WNTR Miss — And How You Can Fill the Gaps
While EPANET and WNTR are powerful, here are areas where your own approach can enhance or complete the picture:

| Gap in EPANET/WNTR         | How to Fill It With Your Approach                                         |
|----------------------------|----------------------------------------------------------------------------|
| No real-time sensor data   | Integrate with IoT sensors and stream data to PostgreSQL or Redis         |
| Limited anomaly detection  | Add ML-based modules for anomaly/defect detection (e.g., pressure drop)   |
| No multilingual support    | Extend PostgreSQL with Arabic/English metadata for nodes/edges            |
| Lack of version tracking   | Create history tables to track pressure over time per pipe or junction    |
| No dashboard out-of-box    | Build web dashboards using Dash, Streamlit, or FastAPI                    |
| No graph semantics         | Build Gremlin-based Knowledge Graphs for context-aware queries            |
| No alerting mechanisms     | Add notification system for pressure violations using Python or Node.js   |
| No optimization engine     | Integrate with optimization libraries for pump scheduling, flow control   |

---

## 💻 HTML-Based Demo Simulation Using WNTR & Dash
Here is a simple example that loads a small `.inp` file, simulates pressure using WNTR, and visualizes results in an HTML dashboard using Dash.

```python
import wntr
import pandas as pd
import dash
from dash import dcc, html
import plotly.graph_objs as go

# Load example network
wn = wntr.network.WaterNetworkModel('data/Net3.inp')
sim = wntr.sim.EpanetSimulator(wn)
results = sim.run_sim()

# Extract pressure at a node over time
node = '123'  # replace with valid node in your INP
pressure = results.node['pressure'][node]

# Build HTML dashboard
app = dash.Dash(__name__)
app.layout = html.Div([
    html.H1('Pressure Simulation for Node 123'),
    dcc.Graph(
        figure=go.Figure(
            data=[go.Scatter(y=pressure.values, mode='lines', name='Pressure')],
            layout=go.Layout(title='Pressure vs Time', xaxis_title='Time Step', yaxis_title='Pressure (m)')
        )
    )
])

if __name__ == '__main__':
    app.run_server(debug=True)
```

---

## 🔗 Are There Online Demos?
There is currently no official online demo hosted by EPA or WNTR, but you can:
- Run demos locally using Dash (see example above)
- Use GitHub projects like [WaterNetGen](https://github.com/alshedivat/WaterNetGen) to generate test data
- Deploy your Dash app to Render, Hugging Face Spaces, or Heroku

---

## 🔧 Installation & Setup (Docker)
```
# Dockerfile
FROM python:3.10
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
CMD ["python", "app.py"]
```

```
# requirements.txt
wntr
pandas
dash
plotly
```

---

## 🗃️ PostgreSQL Schema (Best Practices)
```sql
CREATE TABLE water_nodes (
    id SERIAL PRIMARY KEY,
    node_id TEXT UNIQUE,
    type TEXT,
    x DOUBLE PRECISION,
    y DOUBLE PRECISION,
    base_elevation DOUBLE PRECISION,
    demand DOUBLE PRECISION,
    pressure DOUBLE PRECISION,
    arabic_label TEXT
);

CREATE TABLE water_links (
    id SERIAL PRIMARY KEY,
    link_id TEXT UNIQUE,
    from_node TEXT,
    to_node TEXT,
    length DOUBLE PRECISION,
    diameter DOUBLE PRECISION,
    status TEXT,
    flow DOUBLE PRECISION,
    velocity DOUBLE PRECISION,
    pressure_loss DOUBLE PRECISION,
    arabic_label TEXT
);
```

---

## 🧪 Sample Test Data Generator
```python
from faker import Faker
import random, psycopg2
fake = Faker(['en_US', 'ar_EG'])
conn = psycopg2.connect("dbname=water user=postgres password=postgres")
cursor = conn.cursor()

for _ in range(1000):
    node_id = fake.uuid4()
    x, y = random.uniform(0, 100), random.uniform(0, 100)
    pressure = random.uniform(10, 100)
    arabic = fake.name()
    cursor.execute("""
        INSERT INTO water_nodes (node_id, type, x, y, pressure, arabic_label)
        VALUES (%s, 'JUNCTION', %s, %s, %s, %s)
    """, (node_id, x, y, pressure, arabic))
conn.commit()
```

---

## 🌐 Knowledge Graph Integration (WNTR + Gremlin)
```python
import wntr, psycopg2
from gremlin_python.driver.client import Client

wn = wntr.network.WaterNetworkModel('data/example.inp')
sim = wntr.sim.EpanetSimulator(wn)
results = sim.run_sim()

conn = psycopg2.connect("dbname=water user=postgres password=postgres")
cursor = conn.cursor()

for junc in wn.junction_name_list:
    pressure = results.node['pressure'].loc[:, junc].mean()
    cursor.execute("UPDATE water_nodes SET pressure=%s WHERE node_id=%s", (pressure, junc))
conn.commit()

client = Client('ws://localhost:8182/gremlin', 'g')
client.submit("g.addV('node').property('name', 'Junction A')")
```

---

## ❓ Questions & Answers

### Q: Is there any Free Software like Petrel RE for Water Hydraulic Pressure?
**A:** Yes. EPANET is a public domain alternative developed by the US EPA for simulating water distribution and pressure.

### Q: I want to simulate pressure in pipelines and detect defects?
**A:** EPANET + WNTR (Python) is ideal. You can simulate pressure drops or leaks and flag unusual readings.

### Q: Can I connect EPANET results with a Knowledge Graph?
**A:** Yes. Use PostgreSQL to store simulation output, then query with Apache TinkerPop + Gremlin for graph modeling and visualization.

### Q: What is the best way to simulate water pressure?
**A:** Use EPANET for network modeling, WNTR for simulation and failure analysis, PostgreSQL for storage, and Gremlin/Dash Cytoscape for querying and visualization.

### Q: Can I see a demo of pressure simulation using HTML?
**A:** Yes! A Dash app is provided that shows pressure over time at a node using an interactive line chart.

### Q: Can I find defective pipes between multiple pressure stations?
**A:** Yes. By simulating pressure values at each station and comparing expected vs actual readings, you can identify abnormal pressure drops between nodes. These can be flagged using Python logic or Gremlin graph traversal.

### Q: What does EPANET and WNTR miss?
**A:** They lack real-time capabilities, multilingual support, dashboards, graph semantics, ML/AI modules, versioning, alerting, and optimization tools. These can all be added as extensions to build a smarter and more integrated system.

---

_This document will continue to evolve with more datasets, simulations, ML modules, dashboards, and deployment guides._

