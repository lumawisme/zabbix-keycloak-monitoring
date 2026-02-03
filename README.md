# Zabbix Template – Keycloak by HTTP

A comprehensive **Zabbix HTTP Agent template** for monitoring **Keycloak Identity and Access Management** using the built-in `/health` and `/metrics` endpoints.

This template provides deep visibility into Keycloak health, database performance, JVM behavior, HTTP traffic, and login service-level indicators (SLIs).

**Tested with Zabbix 7.2+** and modern Keycloak distributions (Quarkus).

---

## 📊 Monitored Components

### 🔍 Health & Availability
* Keycloak health status (`/health`)
* Metrics endpoint availability
* Process uptime
* Restart detection

### 🗄️ Database (Agroal)
* Active connections
* Available connections
* Threads waiting for DB connections
* Maximum DB blocking time
* Connection pool utilization (%)

### ☕ JVM Performance
* JVM CPU load
* Heap memory usage
* Non-heap (Metaspace) memory usage
* Garbage Collection pause time
* Thread states (runnable, blocked, total)

### 🌐 HTTP & Login SLIs
* Active HTTP requests
* Login request rate
* Login error rate
* Login error ratio (%)
* Maximum login response time

### 🧠 Cache (Infinispan)
* Cache hit ratio (%)

---

## 🚨 Built-in Triggers

The template includes production-ready triggers, including:

* **Critical:** Keycloak health check is DOWN
* **Critical:** Database connection pool almost full (>90%)
* **Critical:** Database threads waiting for connections
* **Warning:** High database blocking time
* **Warning:** High JVM CPU usage
* **Warning:** JVM blocked threads detected
* **SLI Violation:** High login error ratio (>5%)
* **SLI Violation:** Slow login response time (>250ms)

All triggers use stability windows to minimize alert noise.

---

## 🧩 Requirements

### 1. Keycloak Configuration
Keycloak must be configured to expose its observability endpoints.
**Note:** This template is designed for the **Quarkus** distribution of Keycloak.

You must start Keycloak with the following flags or environment variables.

**Option A: CLI Flags**
```bash
bin/kc.sh start \
  --health-enabled=true \
  --metrics-enabled=true 
```

**Option B: Environment Variables (Docker/K8s)**
```bash
KC_HEALTH_ENABLED=true
KC_METRICS_ENABLED=true
```
### 2. Verify Endpoints
By default, Keycloak exposes these on the management port 9000.
- Metrics: http://<your-ip>:9000/metrics
- Health: http://<your-ip>:9000/health

---

## 📥 Installation

Download the **template_keycloak_http.json** file from this repository.

Log in to your Zabbix Interface.

Go to **Data collection → Templates**.

Click **Import** (top right).

Select the **JSON file** and click **Import**.

---

## ⚙️ Configuration

Create or edit a Host in Zabbix for your Keycloak server.

Link the template "Template App Keycloak by HTTP".

Macros: This template uses the standard {HOST.CONN} macro to find your server. If your Keycloak uses a custom port or path, you can override these macros on the Host level:

| Macro                     | Default Value                     | Description                          |
| ------------------------- | --------------------------------- | ------------------------------------ |
| `{$KEYCLOAK.HEALTH.URL}`  | `http://{HOST.CONN}:9000/health`  | Keycloak health endpoint             |
| `{$KEYCLOAK.METRICS.URL}` | `http://{HOST.CONN}:9000/metrics` | Keycloak Prometheus metrics endpoint |

---

## 📈 Data Collection Model

This template is designed for efficiency and scalability:

Master Item: Uses a single HTTP Agent item to fetch /metrics once per minute.

Dependent Items: All metrics are parsed from that single payload using PROMETHEUS_PATTERN.

Calculated Items: Used for ratios (e.g., Error Rate %) and pool utilization.

This design minimizes the HTTP load on both Zabbix and Keycloak.

---

## 🛠 Tested With
- Keycloak 20+ (Quarkus)
- Zabbix 7.2
- PostgreSQL / MySQL (Agroal connection pool)
  
---

## 🤝 Contributing
- Contributions are welcome!
- New metrics or triggers
- Threshold tuning
- Dashboard ideas
- Compatibility improvements
- Feel free to open an issue or submit a pull request.

---

## 📄 License
- MIT License
---
## ⭐ Credits
Created and maintained by Lu Maw.
Inspired by Keycloak observability SLIs and real-world production usage.
