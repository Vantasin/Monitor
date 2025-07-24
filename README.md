# 🖥️ Monitor Stack

[![MIT License](https://img.shields.io/github/license/Vantasin/Monitor?style=flat-square)](LICENSE)
[![Docker](https://img.shields.io/badge/Docker-compose-2496ED?logo=docker&logoColor=white&style=flat-square)](https://www.docker.com/)
[![ZFS](https://img.shields.io/badge/ZFS-OpenZFS-blue?style=flat-square)](https://openzfs.org/)

[![Prometheus](https://img.shields.io/badge/Prometheus-metrics-orange?logo=prometheus&style=flat-square)](https://prometheus.io/)
[![Grafana](https://img.shields.io/badge/Grafana-dashboard-yellow?logo=grafana&style=flat-square)](https://grafana.com/)
[![Alertmanager](https://img.shields.io/badge/Alertmanager-alerting-critical?style=flat-square)](https://prometheus.io/docs/alerting/latest/alertmanager/)
[![Node Exporter](https://img.shields.io/badge/Node--Exporter-host%20metrics-lightgrey?style=flat-square)](https://github.com/prometheus/node_exporter)
[![cAdvisor](https://img.shields.io/badge/cAdvisor-container%20metrics-blue?style=flat-square)](https://github.com/google/cadvisor)

A lightweight, self-hosted monitoring solution built with Docker Compose. This stack includes:

* **Prometheus**: metrics collection and alerting system
* **Node Exporter**: host-level hardware and OS metrics
* **Grafana**: dashboards and visualization
* **Alertmanager**: alert routing and notifications
* **cAdvisor**: container resource usage monitoring

---

## 📁 Directory Structure

```bash
tank/
├── docker/
│   └── compose/
│       └── monitor/                             # Git repo lives here
│           ├── docker-compose.yml               # Main Docker Compose config
│           ├── alertmanager.yml                 # Alertmanager config with real secrets (gitignored!)
│           ├── alertmanager-example.yml         # Example Alertmanager config with email settings
│           ├── alert_rules.yml                  # Prometheus alerting rules file
│           ├── prometheus.yml                   # Prometheus configuration (scrape targets, alert rules, etc.)
│           └── README.md                        # Project documentation (This file)
```

---

## 🧰 Prerequisites

* Docker Engine
* Docker Compose V2
* Git
* (Optional) ZFS on Linux for dataset management

> ⚠️ **Note:** These instructions assume your ZFS pool is named `tank`. If your pool has a different name (e.g., `rpool`, `zdata`, etc.), replace `tank` in all paths and commands with your actual pool name.

---

## ⚙️ Setup Instructions

1. **Create the stack directory and clone the repository**

   If using ZFS:
   ```bash
   sudo zfs create -p tank/docker/compose/monitor
   cd /tank/docker/compose/monitor
   sudo git clone https://github.com/Vantasin/Monitor.git .
   ```

   If using standard directories:
   ```bash
   mkdir -p ~/docker/compose/monitor
   cd ~/docker/compose/monitor
   git clone https://github.com/Vantasin/Monitor.git .
   ```

2. **Configure alertmanager variables**

   Copy and modify the `alertmanager.yml` file:

   ```bash
   sudo cp alertmanager-example.yml alertmanager.yml
   sudo nano alertmanager.yml
   sudo chmod 600 alertmanager.yml
   ```

   > **Note:** Be sure to update `"smtp.example.com:587"`, `"your-email@example.com"`, `"your-email@example.com"`, `"your-app-password"` and `"admin@example.com"` with your credentials in order to receive email alerts from your monitoring stack.

   > **(Optional)** Tune Prometheus scrape settings and alert rules in `prometheus.yml` and `alert_rules.yml`

3. **Start monitor**

   ```bash
   docker compose up -d
   ```

---

## 🌐 Accessing monitor Web UI

Once deployed, access the monitors via:

- **Prometheus**:    `http://localhost:9090/`
- **Grafana**:       `http://localhost:3030/` (default credentials: `admin` / `admin`)
- **Alertmanager**:  `http://localhost:9093/`
- **cAdvisor**:      `http://localhost:8088/`
- **Node Exporter**: `http://localhost:9100/`

> **Note:** If using a `remote-host` and/or `Tailscale`, replace `localhost` with the appropriate IP address.

> **Note:**  
> This monitoring stack uses **Node Exporter** to expose system metrics, **Prometheus** to scrape, store, and evaluate those metrics, and **Grafana** to visualize them in dashboards. Prometheus also handles alerting by evaluating rules defined in `alert_rules.yml` and forwarding alerts to **Alertmanager**.

> **Note:**  
> **cAdvisor** collects real-time resource usage metrics from Docker containers, which Prometheus scrapes and Grafana visualizes. It complements Node Exporter by providing container-level insights.

---

## 📊 Grafana Dashboard Setup

To visualize metrics from Prometheus, Node Exporter, and cAdvisor in Grafana, follow these steps:

### 1. Access Grafana
Open your browser and navigate to:

`http://<your-server-ip>:3030`

- Default username: `admin`
- Default password: `admin` (you will be prompted to change it)

> **Note:** Consider adding `Grafana` as proxy host using [Nginx Proxy Manager](https://github.com/Vantasin/Nginx-Proxy-Manager.git) as a reverse proxy for HTTPS certificates via Let's Encrypt.

### 2. Add Prometheus as a Data Source

1. In the left sidebar, go to **⚙️ Settings → Data Sources**
2. Click **"Add data source"**
3. Select **Prometheus**
4. Set the URL to: `http://prometheus:9090`
5. Click **"Save & Test"**

### 3. Import Dashboards

Go to **+ Create → Import**, then enter the following dashboard IDs and follow the prompts:

#### 📦 Docker Container Metrics (cAdvisor)
- **ID:** `893`
- **Description:** Visualizes per-container CPU, memory, disk, and network stats from cAdvisor

#### 🖥️ Node Exporter Full (Host Metrics)
- **ID:** `1860`
- **Description:** Full system metrics dashboard including CPU, RAM, disk, network, load average, and more from Node Exporter

#### 📈 Prometheus Stats
- **ID:** `3662`
- **Description:** Internal Prometheus stats like target status, scrape duration, rule evaluation, etc.

### 4. View and Customize
- Navigate to **Dashboards → Manage** to view your imported dashboards
- You can filter by job (e.g., `node`, `cadvisor`) or target label (e.g., container name or hostname)
- Customize panels or create new dashboards as needed

---

## 🙏 Acknowledgments

- [ChatGPT](https://openai.com/chatgpt) — for assistance in generating setup scripts and templates.
- [OpenZFS](https://openzfs.org/) — for advanced local filesystem features, dataset organization, and snapshotting.
- [Prometheus](https://prometheus.io/) — for time-series metrics collection and powerful alerting capabilities.
- [Grafana](https://grafana.com/) — for beautiful, flexible dashboards and visualizations of Prometheus metrics.
- [Node Exporter](https://github.com/prometheus/node_exporter) — for exposing essential hardware and OS-level metrics from the host system.
- [cAdvisor](https://github.com/google/cadvisor) — for collecting real-time resource usage and performance stats from Docker containers.
- [Alertmanager](https://prometheus.io/docs/alerting/latest/alertmanager/) — for handling, grouping, silencing, and routing alerts generated by Prometheus.
- [Docker](https://www.docker.com/) — for containerizing the entire monitoring stack and enabling easy deployment.
