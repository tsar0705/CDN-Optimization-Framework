# 🚀 CDN-Optimization-Framework

An interactive simulation of a CDN-style network that models congestion, subnetwork organization, adaptive file distribution, and congestion-aware shortest-path retrieval. Built with **NetworkX** for graph modeling and **Plotly** for interactive 3D visualization, with a background thread simulating fluctuating real-time network load.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/tsar0705/CDN-Optimization-Framework/blob/main/CDN_Optimization_Framework.ipynb)
![Python](https://img.shields.io/badge/Python-3.x-3776AB?logo=python&logoColor=white)
![NetworkX](https://img.shields.io/badge/Graphs-NetworkX-11557C)
![Plotly](https://img.shields.io/badge/Visualization-Plotly-3F4F75?logo=plotly&logoColor=white)

---

## 📖 Overview

This project simulates a distributed network environment where nodes act as servers/routers grouped into subnetworks (mimicking edge nodes in a CDN). Files are distributed across subnetworks, and a background thread continuously introduces random congestion. When a "file request" is simulated, the framework finds the most efficient path to retrieve the file — factoring in current congestion levels — using shortest-path and Dijkstra-based fallback logic.

The entire implementation lives in a **single Jupyter/Colab notebook**: `CDN_Optimization_Framework.ipynb`.

## ✨ Features

- 🌐 **Random connected network generation** — builds a weighted, guaranteed-connected graph of `n` nodes
- 🧩 **Automatic subnetwork classification** — groups nodes into subnetworks (max size 5) based on connected components, reconnecting any that end up disconnected
- 🏢 **Main server node** — a dedicated node connected to one node in every subnetwork, acting as a fallback/central hub
- 📦 **Adaptive file distribution** — randomly assigns files across subnetworks, guaranteeing at least one file per subnetwork
- 🔄 **Real-time congestion simulation** — a background thread randomly increases congestion on nodes at randomized intervals; congestion decays back down over time via a second background thread per congested node
- 🧭 **Congestion-aware pathfinding** — for each file request, finds the best-positioned node holding the file and computes the shortest path to it; if that node is too congested, falls back to Dijkstra search within the subnetwork, and ultimately to the main server node if no good alternative exists
- 🖼️ **Interactive 3D visualization** — Plotly-based 3D graph rendering of the full network, individual subnetworks, and the network with the main server highlighted

## 🏗️ How It Works

1. **Graph Generation** — `Graph.generate_connected_graph(n)` builds a random weighted graph, retrying until it's fully connected.
2. **Subnetwork Classification** — `subnetworks1.classify_subnetworks(...)` splits connected components into subnetworks of at most 5 nodes; `connect_disconnected_subnetwork(...)` repairs any subnetwork that ends up internally disconnected.
3. **Main Server Setup** — `subnetworks1.set_main_server(...)` connects a dedicated main server node to one randomly chosen node in every subnetwork.
4. **File Distribution** — `Graph.create_files(...)` generates a file list, and `subnetworks1.assign_files_to_subnetwork(...)` distributes them across subnetworks (and the main server) so every subnetwork holds at least one file.
5. **Congestion Simulation** — `subnetworks1.congestion_setter(...)` initializes congestion levels per node; `random_congestion_updater(...)` runs in a background thread, randomly increasing congestion on nodes at random intervals, while `subnetworks1.reduce_congestion(...)` decays it back down after a delay.
6. **File Retrieval Simulation** — the `main(...)` loop simulates 50 randomized file requests from random starting nodes, calling `subnetworks1.best_path_finder(...)` each time to locate the requested file and compute the most efficient (congestion-aware) route to it, timing each retrieval.

## 📁 Project Structure

| File | Description |
|---|---|
| `CDN_Optimization_Framework.ipynb` | Full implementation — graph generation, subnetwork classification, file distribution, congestion simulation, pathfinding, and 3D visualization, all in one notebook |
| `README.md` | Documentation |

### Core Components (inside the notebook)

| Component | Type | Responsibility |
|---|---|---|
| `Graph` | Class | Builds the connected network graph, generates the file list, cleans up unwanted edges, and renders 3D visualizations |
| `subnetworks1` | Class | Classifies/repairs subnetworks, sets the main server node, assigns files, tracks and updates congestion, and computes congestion-aware retrieval paths |
| `random_congestion_updater()` | Function | Background thread that randomly increases node congestion at randomized intervals |
| `main()` | Function | Orchestrates graph setup, file distribution, congestion simulation, and the file-retrieval simulation loop |

> Note: earlier documentation for this project referred to `main.py`, `subnetworks1.py`, and `Graph.py` as separate files. In the current repository, all of that logic lives inside the single notebook above as classes/functions of the same names — there are no standalone `.py` files.

## 🧰 Setup & Installation

### Prerequisites

- Python 3.x
- Jupyter Notebook, JupyterLab, or Google Colab (the notebook includes an "Open in Colab" badge)

### Installation

```bash
git clone https://github.com/tsar0705/CDN-Optimization-Framework.git
cd CDN-Optimization-Framework
pip install -r requirements.txt
```

The repository's `requirements.txt` lists `networkx`, `plotly`, and `numpy` — the notebook's only third-party imports (`random`, `time`, and `threading` are part of Python's standard library).

## ▶️ Usage

1. Open `CDN_Optimization_Framework.ipynb` in Jupyter or Colab.
2. Run all cells. When prompted, enter the number of nodes for the network:
   ```
   Enter the number of nodes : 50
   ```
3. The simulation will:
   - Build the network and subnetworks
   - Distribute files
   - Start the background congestion thread
   - Run 50 simulated file-retrieval requests, printing the path, congestion updates, and elapsed time for each

To visualize the network, call any of the following (currently commented out in `main()`):

```python
Graph.display_graph_3d_interactive(connected_graph.graph)
subnetworks1.interactive_3d_plot_with_main(connected_graph.graph, subnetworks, number_of_nodes)
subnetworks1.plot_subnetworks_interactive(subnetworks)
```

## 🛠 Customizing the Simulation

| Parameter | Location | Effect |
|---|---|---|
| `edge_probability` | `Graph.generate_connected_graph()` | Controls how densely nodes are connected |
| `max_size` | `subnetworks1.classify_subnetworks()` | Maximum nodes per subnetwork |
| `max_congestion` | `random_congestion_updater()` | Max congestion added per random update |
| `min_delay` / `max_delay` | `random_congestion_updater()` | Time range (seconds) between random congestion updates |
| Congestion threshold (`4`) | `best_path_finder()` / `dijkstra_for_subnet()` | Congestion level at which the pathfinder looks for an alternative node |
| Retrieval count (`range(50)`) | `main()` | Number of simulated file requests per run |

## 📊 Visualizations

- **Full network view** — interactive 3D plot of all nodes and edges
- **Per-subnetwork view** — each subnetwork rendered in a distinct color
- **Main-server view** — subnetworks plotted around the central main server node, which is highlighted in red

## 🤝 Contributing

Contributions are welcome. If you have ideas for improved congestion modeling, smarter pathfinding, or better file distribution strategies:

1. Fork the repo
2. Create a feature branch
3. Commit your changes
4. Open a Pull Request

## 🗺️ Future Improvements

- [ ] Split the notebook into proper `.py` modules (`graph.py`, `subnetworks.py`, `main.py`) for reuse outside notebooks
- [ ] Replace `print`-based debugging with proper logging
- [ ] Add automated tests for pathfinding and congestion logic
- [ ] Benchmark path-cost / retrieval-time improvements against a naive (non-congestion-aware) baseline

## 🙋 About

A hands-on exploration of network optimization, subnetwork-based load balancing, and congestion-aware pathfinding — modeling core ideas behind CDN-style content distribution.
