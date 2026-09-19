# Automated Observability Stack: Prometheus, Grafana, and Datadog/Groundcover Integration

This repository provides a production-ready, fully automated deployment of a modern monitoring and observability stack. It uses **Docker Compose** for local service orchestration and **Ansible** for automated provisioning and multi-node host configuration. 

The stack deploys infrastructure metrics collection, unified visualization through **Grafana**, and seamlessly bridges metrics to cloud-native platforms like **Datadog** or **Groundcover**.

---

## 🏗️ Repository Architecture

```text
monitoring-stack/
├── ansible/
│   ├── inventory.ini
│   └── playbook.yml
├── dashboards/
│   └── default-dashboard.json
├── docker-compose.yml
├── grafana-datasources.yml
├── prometheus.yml
└── README.md
```

---

## 🚀 Getting Started

### Prerequisites
* **Docker** & **Docker Compose v2+** installed on the target machine.
* **Ansible 2.10+** (if utilizing remote deployment playbooks).
* Account credentials for **Datadog** or **Groundcover** (API Key & Site URL).

### Method 1: Local Deployment with Docker Compose
To launch the core stack instantly on your local machine:

1. Clone this repository and navigate to the directory:
   ```bash
   git clone <your-repo-url>
   cd monitoring-stack
   ```
2. Configure your Datadog or Groundcover environment variables in a `.env` file or export them directly:
   ```bash
   export DD_API_KEY="your_datadog_api_key_here"
   export DD_SITE="datadoghq.com"
   ```
3. Start the containers in detached mode:
   ```bash
   docker-compose up -d
   ```
4. Access your local dashboards:
   * **Grafana:** `http://localhost:3000` (Default Credentials: `admin` / `admin`)
   * **Prometheus Expression Browser:** `http://localhost:9090`

### Method 2: Remote Enterprise Deployment with Ansible
To provision, configure dependencies, and deploy the entire stack across target server fleets:

1. Open `ansible/inventory.ini` and specify your target host IP addresses and SSH users.
2. Execute the Ansible playbook:
   ```bash
   ansible-playbook -i ansible/inventory.ini ansible/playbook.yml
   ```

---

## 🛠️ Components & Configurations

### 1. Prometheus (`prometheus.yml`)
Scrapes host metric metrics natively at 15-second intervals from `node-exporter` and infrastructure containers.

### 2. Grafana (`grafana-datasources.yml`)
Automatically provisions the local Prometheus instance as a default data source on startup. Manual data source adding is eliminated.

### 3. Datadog / Groundcover Agent
Runs as a lightweight containerized sidecar. It securely collects core engine performance counters, system utilization metrics, and forwards logs/traces to your centralized APM control plane.

### 4. Dashboards (`dashboards/default-dashboard.json`)
Pre-seeded dashboard JSON template that visualizes CPU profiles, memory availability, active network connections, storage IOPS bottlenecks, and container performance health metrics out-of-the-box.

---

## 🔒 Production Security Best Practices
* **Update Default Secrets:** Change the default Grafana admin password (`admin`) in the `docker-compose.yml` environment block or map it via enterprise secrets managers before production launch.
* **Network Hardening:** Restrict ports `9090` (Prometheus) and `9100` (Node Exporter) to internal VPC networks or Docker virtual bridges. Only port `3000` or a reverse proxy route should be public-facing.
* **API Key Safety:** Never commit your actual `DD_API_KEY` to public source control systems. Always populate credentials dynamically using external `.env` runtime injectors, Ansible Vault, or cloud secrets managers.
