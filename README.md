# 🌐 Cloud-Native Network Monitoring System

This project demonstrates a distributed, cloud-native monitoring infrastructure deployed in a containerized environment using **Docker**, **Docker Compose**, **Prometheus**, **Grafana**, and **Node Exporter**. The architecture monitors multiple targets concurrently: the local host (via WSL2 Ubuntu) and a remote/isolated node running on a Hyper-V Ubuntu Virtual Machine.

---

## 🚀 Key Features

* 📊 **Multi-Node Distributed Monitoring:** Monitors both local host and isolated virtual machines simultaneously.
* ⏱️ **Real-Time Telemetry:** Scrapes system metrics at a high-precision interval of **5 seconds** (`scrape_interval: 5s`).
* 📉 **Full-Stack Visualization:** Dynamic Grafana dashboards tracking CPU, RAM, Disk utilization, and Network I/O.
* 🚨 **Proactive Alerting:** Integrated automated alerting system based on custom metric thresholds.
* 💬 **Instant Notifications:** Real-time alert forwarding to smart devices using **Telegram Bot API**.
* 📦 **Containerized Orchestration:** Seamless, isolated deployment via Docker Compose.

---

## 🛠️ Built With

* **[Docker & Docker Compose](https://www.docker.com/)** — Containerization and service orchestration.
* **[Prometheus](https://prometheus.io/)** — Time-series database (TSDB) for scraping and storing metrics.
* **[Grafana](https://grafana.com/)** — Data visualization and alert management platform.
* **[Node Exporter](https://github.com/prometheus/node_exporter)** — Hardware and OS metrics exporter.
* **[Ubuntu (WSL2)](https://learn.microsoft.com/en-us/windows/wsl/)** — Local Linux development subsystem.
* **[Hyper-V](https://learn.microsoft.com/en-us/virtualization/hyper-v-on-windows/)** — Isolated secondary node simulation.
* **[Telegram Bot API](https://core.telegram.org/bots/api)** — Instant notification routing channel.

---

## 📐 System Architecture & Data Flow

The monitoring infrastructure operates on a Pull-Based telemetry model, where the centralized Prometheus server continuously scrapes endpoints exposed by Node Exporters across different network spaces.

  1. Distributed Targets (Data Sources):
    
    node-exporter-host (WSL2) ─── (Port: 9100/metrics) ───► Centralized Pipeline
    
    ubuntu-vm (Hyper-V) ─────── (Port: 9100/metrics) ───► Centralized Pipeline
  
  2. Centralized Core Stack:
    
    Prometheus TSDB (:9090): Scrapes all targets concurrently every 5 seconds.
    
    Grafana Dashboard (:3000): Connects to Prometheus to visualize real-time database queries.
  
  3. Alert & Notification Pipeline:
  
    Grafana Alerting Engine: Triggers instantly when the threshold is violated (node_load1 > 1.1).
    
    Telegram Bot API: Receives webhook payload and performs an instant push routing.
    
    System Administrator: Receives real-time Firing and Resolved notifications on smart devices.

## ⚙️ Configuration & Deployment

### 1. Prometheus Target Definition (`prometheus.yml`)
The configuration defines two individual jobs to collect metrics from both the local containerized network and the explicit IP address of the Hyper-V virtual machine.

```yaml
global:
  scrape_interval: 5s # High frequency sampling

scrape_configs:
  - job_name: "node-exporter-host"
    static_configs:
      - targets: ["node-exporter:9100"]

  - job_name: "ubuntu-vm"
    static_configs:
      - targets: ["172.20.236.188:9100"] # Static IP of Hyper-V VM
2. Up and Running
Deploy the entire monitoring stack with a single command via Docker Compose:

Bash
docker-compose up -d
Verify that all target endpoints are in the "UP" state by visiting http://localhost:9090/targets.

⚡ Automated Alerting & Crisis Simulation
To ensure system reliability during critical load anomalies, a dedicated alert pipeline was integrated and stressed.

Alert Definition
Metric Monitored: CPU Load Average (node_load1).

Threshold Condition: > 1.1.

Routing Contact Point: Telegram Contact Point (configured via Telegram Bot Token and Chat ID).

Crisis Simulation (Stress Testing)
The architecture's alerting capability was verified by triggering a synthetic heavy workload on the processor using the stress tool:

Bash
# Simulating severe CPU load for 180 seconds across 8 cores
stress --cpu 8 --timeout 180
Notification Lifecycle Execution
Pending Status: CPU load peaks immediately; Grafana registers the anomaly.

Firing Status: The rule surpasses the duration threshold, changing state to Firing. A critical push notification is sent instantly via the Telegram Bot to the administrator's phone.

Resolved Status: After 180 seconds, the stress execution times out, and hardware usage drops back to normal. The bot successfully transmits an automated Resolved notification signifying system stabilization.

🔍 Troubleshooting Matrix
❌ Docker Daemon Connection Issues: If docker ps fails on your terminal, ensure Docker Desktop is running and initialized.

❌ WSL Command Disconnect: If Docker commands are unrecognized inside the WSL shell, navigate to Docker Desktop Settings -> Resources -> WSL Integration and enable integration for your target distribution (e.g., Ubuntu).

❌ VM Target "DOWN" Status: Hyper-V dynamic IP reassignment might break connection strings. Update the newly assigned IP inside prometheus.yml and reload/restart the Prometheus container service to restore "UP" status.
