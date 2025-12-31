# Urban-Traffic-Flow
Stochastic traffic simulation engine on real-world OpenStreetMap networks. Features continuous-space modeling, Monte Carlo risk analysis for tail risks, and graph topology correlations (Edge Betweenness) using Python &amp; NetworkX.
# Urban Traffic Flow & Congestion Analysis Engine

![Python](https://img.shields.io/badge/Python-3.10%2B-blue)
![NetworkX](https://img.shields.io/badge/Graph_Theory-NetworkX-green)
![License](https://img.shields.io/badge/License-MIT-purple)
![Status](https://img.shields.io/badge/Status-Complete-success)

> **A stochastic, continuous-space traffic simulation engine built on real-world OpenStreetMap data to model urban congestion dynamics and validate topological predictors.**

---

## 📍 Overview

[cite_start]This project implements a **discrete-event simulation** to model vehicle flow on real-world road networks[cite: 11]. [cite_start]Unlike simple node-hopping algorithms, this engine tracks **continuous vehicle positions** (in meters) to simulate realistic queuing, spillback, and density-dependent velocity reduction[cite: 14, 16].

[cite_start]The core objective was to answer: **Can static graph topology predict dynamic traffic bottlenecks?** By running Monte Carlo simulations across different urban layouts (Buenos Aires Residential vs. Obelisco), the study confirms that **Edge Betweenness Centrality** is a statistically significant predictor of congestion ($R^2 \approx 0.21, p < 10^{-65}$), while local metrics like Degree Centrality offer zero predictive power[cite: 553, 560].

---

## 🎥 Simulation Demo

*Real-time visualization of congestion propagation. Red edges indicate high density ($\rho > 80\%$) and reduced flow.*

![Simulation Demo](assets/demo.gif)
*(Note: Replace `assets/demo.gif` with your uploaded file `download (3).gif`)*

---

## 🚀 Key Features

* [cite_start]**Real-World Network Ingestion:** Utilizes **OSMnx** to ingest directed multigraphs from OpenStreetMap, handling parallel edges and cleaning disconnected components[cite: 12, 64, 75].
* [cite_start]**Physics-Based Mobility:** Implements the **Greenshields Model**, where vehicle speed decays linearly as edge density increases, simulating the transition from free-flow to gridlock[cite: 159, 258].
* [cite_start]**Spillback Mechanics:** Prevents "teleportation" by enforcing capacity constraints; vehicles queue at the end of edges if the downstream link is jammed[cite: 266].
* [cite_start]**Monte Carlo Risk Analysis:** Aggregates data from repeated stochastic runs (N=10) to generate confidence intervals for travel times and congestion metrics[cite: 322].

---

## 📊 Data Science & Analysis

### 1. Heavy-Tail Risk Distribution
Congestion does not distribute normally. [cite_start]Analysis of 8.7M edge-timestep measurements reveals a **heavy-tailed distribution**, where the top **5% of edges** account for the vast majority of network delay[cite: 385].

| Metric | Value |
| :--- | :--- |
| **Median Congestion** | 0.034 (Free Flow) |
| **95th Percentile** | ~0.18 (Significant Delay) |
| **Distribution Type** | Heavy-tailed / Log-normal-like |

![Congestion Distribution](assets/distribution.png)
*(Rename `congestion_distribution.png` to `assets/distribution.png`)*

### 2. Topological Predictors of Congestion
We performed a correlation analysis between static graph metrics and empirical congestion gathered from the simulation.

**Findings:**
* [cite_start]**Edge Betweenness** is the strongest predictor ($r=0.454$, $p<10^{-65}$), proving that "bridge" edges are critical failure points[cite: 503].
* [cite_start]**Degree Centrality** (number of lanes/connections) has **no correlation** ($r=0.02$) with congestion, debunking the idea that "more intersections = more traffic"[cite: 560].

![Scatter Plots](assets/correlations.png)
*(Rename `metric_scatter_plots_obelisco.png` to `assets/correlations.png`)*

![Bar Chart](assets/predictive_power.png)
*(Rename `download (1).png` to `assets/predictive_power.png`)*

### 3. System Equilibrium
[cite_start]The system demonstrates stability, reaching a steady-state congestion level after an initial loading phase (approx. 400 timesteps), validating the closed-loop respawn logic[cite: 406].

![Time Evolution](assets/time_evolution.png)
*(Rename `download (2).png` to `assets/time_evolution.png`)*

---

## 🛠️ Technical Architecture

### Class Structure
The project uses a modular **Object-Oriented Design**:
* [cite_start]`NetworkLoader`: Handles OSM API requests, graph cleaning (SCC extraction), and attribute injection (speed limits, lengths)[cite: 43].
* [cite_start]`TrafficSimulation`: Manages the global state, time-stepping ($\Delta t \approx 0.28s$), and spatial hashing of vehicles[cite: 95].
* [cite_start]`Car`: A dataclass tracking continuous position `pos_m`, route path, and current velocity `speed_ms`[cite: 80].

### Congestion Logic (Greenshields)
Velocity $v$ for a vehicle on edge $e$ is calculated dynamically:

$$v_{eff} = v_{max} \left( 1 - \frac{\rho}{\rho_{jam}} \right)$$

Where $\rho$ is the current density (cars/km/lane). [cite_start]A minimum speed floor ($1.0 m/s$) is enforced to prevent numerical instability during gridlock[cite: 264].

---

## 💻 Installation & Usage

1. **Clone the repository**
   ```bash
   git clone [https://github.com/Abdulrahmansoliman/Urban-Traffic-Flow.git](https://github.com/Abdulrahmansoliman/Urban-Traffic-Flow.git)
   cd Urban-Traffic-Flow
