# Cloud-Monitoring-Auto-Healing-System
A real-time cloud monitoring and auto-healing system built with Python, Flask, Docker, MySQL, and AWS EC2 for monitoring server health and automating service recovery.


<div align="center">
  <img src="https://github.com/atharva1727/Cloud-Monitoring-Auto-Healing-System/blob/main/banner-hero.png" alt="Cloud Monitoring & Auto-Healing System" width="100%">
</div>

<div align="center">

![Python](https://img.shields.io/badge/Python-3.11-blue?logo=python&logoColor=white)
![Flask](https://img.shields.io/badge/Flask-3.0-black?logo=flask&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-Container-2496ED?logo=docker&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-EC2-FF9900?logo=amazonaws&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL-Database-4479A1?logo=mysql&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-green)
![Status](https://img.shields.io/badge/Status-Active-success)

</div>

---

## 📖 Overview

The **Cloud Monitoring & Auto-Healing System** is a production-style monitoring service that tracks CPU, memory, and disk utilization in real time, detects unhealthy services, and **automatically triggers recovery actions** — with zero manual intervention.

It was built to answer a simple operational problem: *by the time a human notices a server is struggling, damage is often already done.* This system closes that gap by continuously watching infrastructure health, reacting to problems the moment they're detected, and keeping a full audit trail of everything it observed and fixed.

It exposes a clean REST API, persists historical metrics to MySQL for trend analysis, and ships fully containerized for one-command deployment on AWS EC2.

---

## ✨ Features

| Feature | Description |
|---|---|
| 📊 **Real-time metrics** | Live CPU, memory, and disk usage collected via `psutil`, refreshed on every request |
| 🩺 **Automated health checks** | Configurable thresholds classify system state as `HEALTHY`, `WARNING`, or `CRITICAL` |
| 🔁 **Self-healing** | Automatically detects and restarts unhealthy or crashed services — no human required |
| 🚨 **Proactive alerting** | Every anomaly is logged the moment it's detected and exposed via a queryable API |
| 🗄️ **Historical tracking** | All metrics and alerts are persisted to MySQL, enabling trend analysis over time |
| 🐳 **Fully containerized** | `docker-compose up` spins up the API and database together — no manual setup |
| ☁️ **Cloud-ready** | Designed to deploy directly to AWS EC2 with minimal configuration |
| 🔐 **Secure by design** | Credentials and connection details are environment-based — nothing hardcoded |
| 📈 **Scalable architecture** | Modular services (monitor, auto-heal, persistence) can scale or be swapped independently |

---

## 🏗️ System Architecture

<div align="center">
  <img src="https://github.com/atharva1727/Cloud-Monitoring-Auto-Healing-System/blob/main/architecture-diagram.png" alt="System Architecture Diagram" width="100%">
</div>

**How it works, end to end:**
1. The **Dashboard / client** calls the Flask REST API to request metrics or trigger a check.
2. The **Monitor Engine** collects live CPU, memory, and disk stats using `psutil`.
3. The **Auto-Healing Engine** evaluates those stats (and monitored services) against defined thresholds.
4. If something is unhealthy, it's **automatically restarted**, and the event is written to the **Proactive Alerts** log.
5. Every metric snapshot and alert is persisted to **MySQL** for historical reporting.
6. The entire stack runs inside **Docker containers**, deployed on **AWS EC2**.

---

## 🔁 Auto-Healing Workflow

<div align="center">
  <img src="assets/auto-healing-workflow.png" alt="Auto-Healing Workflow Diagram" width="100%">
</div>

Each check cycle: **collect → evaluate → decide → act.** If a service or resource crosses its threshold, the engine restarts it automatically and logs an alert. If everything is within limits, no action is taken — keeping the system quiet unless something actually needs attention.

---

## 🖥️ Dashboard Preview

<div align="center">
  <img src="assets/dashboard-mockup.png" alt="Dashboard Preview" width="100%">
</div>

A conceptual preview of what the monitoring dashboard surfaces: live resource cards, a rolling usage trend, and a running feed of recent alerts and auto-healing actions.

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Backend / API | Python, Flask |
| System Metrics | psutil |
| Database | MySQL |
| Containerization | Docker, Docker Compose |
| Deployment | AWS EC2 |
| WSGI Server | Gunicorn |

---

## 📂 Project Structure

```
cloud-monitoring-autohealing/
├── app/
│   ├── main.py            # Flask REST API — routes and request handling
│   ├── monitor.py         # Metric collection (CPU / Memory / Disk)
│   ├── auto_heal.py       # Health checks + auto-recovery logic
│   └── db.py               # MySQL persistence layer
├── assets/                 # Banners, diagrams, and screenshots used in this README
├── Dockerfile               # Container image definition
├── docker-compose.yml       # Full stack: API + MySQL
├── requirements.txt         # Python dependencies
├── LICENSE
└── README.md
```

---

## 🚀 Getting Started

### Prerequisites
- Docker & Docker Compose installed
- (Optional) Python 3.11+ if you want to run locally without Docker
- (Optional) AWS EC2 instance for cloud deployment

### Option 1 — Run with Docker Compose (recommended)

```bash
git clone https://github.com/atharva1727/cloud-monitoring-autohealing.git
cd cloud-monitoring-autohealing
docker-compose up --build
```

The API will be live at **http://localhost:5000**. MySQL spins up automatically alongside it.

### Option 2 — Run locally (without Docker)

```bash
python -m venv venv
source venv/bin/activate      # Windows: venv\Scripts\activate
pip install -r requirements.txt

export DB_HOST=localhost
export DB_USER=root
export DB_PASSWORD=yourpassword
export DB_NAME=cloud_monitoring

python app/main.py
```

### Option 3 — Deploy to AWS EC2

```bash
# On your EC2 instance
sudo yum install -y docker git
sudo service docker start
git clone https://github.com/atharva1727/cloud-monitoring-autohealing.git
cd cloud-monitoring-autohealing
sudo docker-compose up --build -d
```

---

## 📡 API Reference

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/api/health` | Liveness probe for the monitoring service itself |
| `GET` | `/api/metrics` | Returns current CPU / memory / disk usage and status |
| `POST` | `/api/check` | Runs a full health check and triggers auto-healing if an issue is found |
| `GET` | `/api/alerts?limit=20` | Returns recent alert history, most recent first |

**Example request:**

```bash
curl http://localhost:5000/api/metrics
```

**Example response:**

```json
{
  "timestamp": "2026-07-16T10:32:00Z",
  "cpu_percent": 42.1,
  "memory_percent": 67.8,
  "memory_used_gb": 5.4,
  "memory_total_gb": 8.0,
  "disk_percent": 55.2,
  "disk_used_gb": 110.4,
  "disk_total_gb": 200.0,
  "status": "HEALTHY"
}
```

**Triggering a health check:**

```bash
curl -X POST http://localhost:5000/api/check
```

```json
{
  "metrics": { "cpu_percent": 91.2, "status": "CRITICAL", "...": "..." },
  "issues_found": true,
  "issues": [
    { "type": "RESOURCE", "severity": "CRITICAL", "detail": "CPU=91.2% MEM=70.1% DISK=55.0%" },
    { "type": "SERVICE_DOWN", "service": "nginx", "auto_healed": true }
  ]
}
```

---

## ⚙️ Configuration

Thresholds and connection settings can be tuned via environment variables and constants:

| Variable | Default | Purpose |
|---|---|---|
| `DB_HOST` | `localhost` | MySQL host |
| `DB_USER` | `root` | MySQL user |
| `DB_PASSWORD` | — | MySQL password |
| `DB_NAME` | `cloud_monitoring` | Database name |
| `CPU_THRESHOLD` | `85%` | CPU usage that triggers a warning/critical state |
| `MEMORY_THRESHOLD` | `90%` | Memory usage that triggers a warning/critical state |
| `DISK_THRESHOLD` | `90%` | Disk usage that triggers a warning/critical state |

---

## 🗺️ Roadmap

- [ ] Slack / Email notifications for critical alerts
- [ ] Grafana dashboard integration for richer visualization
- [ ] Fully configurable thresholds via `.env`
- [ ] Kubernetes deployment manifests
- [ ] Multi-host/multi-instance monitoring support

---

## 🤝 Contributing

Contributions, issues, and feature requests are welcome. Feel free to check the [issues page](../../issues) or open a pull request.

1. Fork the repo
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---



<div align="center">

**Built by [Atharva Shevate](https://github.com/atharva1727)**

[Portfolio](https://atharva1727.github.io/Atharva--Portfolio) · [LinkedIn](https://linkedin.com/in/atharva-shevate-082b602a7) · [GitHub](https://github.com/atharva1727)

</div>
