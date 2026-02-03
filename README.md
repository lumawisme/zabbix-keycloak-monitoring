# Zabbix Template – Keycloak by HTTP

A comprehensive, efficient Zabbix HTTP Agent template for monitoring Keycloak Identity and Access Management.

This template utilizes Keycloak's built-in observability endpoints (/health and /metrics) to provide deep visibility into system health, database connection pools (Agroal), JVM performance, and critical Login Service Level Indicators (SLIs).

---

## 🚀 Features

- Zero-Dependency: Uses Zabbix's native HTTP Agent. No external scripts or Zabbix Senders required.

- Efficient Data Collection: Utilizes a Master Item approach. Zabbix fetches the metrics payload once per minute, and all dependent items extract data from that single request, minimizing load on the Keycloak server.

- Production Ready: Includes pre-tuned triggers for database exhaustion, login latency, and system saturation.

---

## 🛠 Tested With
- Keycloak 20+ (Quarkus) running on RHEL 8.9 (Ootpa)
- Zabbix 7.2.1

---

## 📊 Monitored Metrics

### 🔍 Health & Availability
- Global Status: Application health check (/health).
- Uptime: Process uptime tracking with automatic restart detection.
- Service Reliability: Monitors the availability of the metrics endpoint itself.

### 🗄️ Database (Agroal)
- Connection Pool: Active, Available, and Awaiting connections.
- Performance: Maximum blocking time (latency in acquiring a connection).
- Utilization: Calculated percentage of pool usage.

### ☕ JVM Performance
- Memory: Heap and Non-Heap (Metaspace) usage in Bytes.
- CPU: Process-specific CPU load and System-wide CPU load.
- Garbage Collection: Total GC pause time.
- Threads: Runnable, Blocked, and Total thread counts.

### 🌐 HTTP & Login SLIs
Metrics specifically tuned to track user experience based on Keycloak's observability guide.
- Traffic: Global Active HTTP requests.
- Login Rates: Requests per second (RPS) to login flows.
- Errors: Login error rate and calculated Error Ratio (%).
- Latency: Maximum response time for login requests.

### 🧠 Cache (Infinispan)
- Efficiency: Cache Hit Ratio %.

---

## 🚨 Triggers & Alerts

This template comes with High Priority (Disaster) alerts to ensure immediate visibility into critical failures.

| Trigger Name | Threshold | Severity | Logic |
| :--- | :--- | :--- | :--- |
| **Keycloak Health Check is DOWN** | Status ≠ "UP" | **Disaster** | Immediate alert if `/health` fails. |
| **Database Threads are Waiting** | Count > 0 | **Disaster** | Threads are queued; indicates DB pool exhaustion. |
| **High Database Blocking Time** | > 1000ms | **Disaster** | It is taking >1s to acquire a DB connection. |
| **DB Pool almost full** | > 90% | **Disaster** | Connection pool utilization is critical. |
| **High Login Error Rate** | > 5% | **Disaster** | Over 5% of login attempts are failing (500 errors). |
| **Login Response Time High** | > 250ms | **Disaster** | Login slowness detected. |
| **High JVM CPU Load** | > 90% | **Disaster** | CPU is saturated. |
| **JVM Blocked Threads** | > 2 | **Disaster** | Threads stuck waiting on locks. |
| **Process has restarted** | Uptime < 10m | **Disaster** | Detects if the Keycloak service crashed/restarted. |

---

## 🧩 Keycloak Configuration requirements

Keycloak does not expose metrics by default. You must enable them via CLI flags or Environment Variables.

### CLI Flags

When starting Keycloak:

```bash
bin/kc.sh start-dev \
  --health-enabled=true \
  --metrics-enabled=true 
```
Note: The start-dev command is optimized for local testing. For production, ensure these flags are applied to your start command or configuration file.


### 🔍 Verification
Before installing the template, ensure you can access these URLs from your Zabbix Proxy/Server:
- http://your-keycloak-ip:9000/health
- http://your-keycloak-ip:9000/metrics

---

## ⚙️ Zabbix Configuration

### 1. Import Template:

- Go to Data collection → Templates.
- Click Import.
- Select template_keycloak_http.json and confirm.

### 2. Add to Host:

- Create or Edit a Host in Zabbix.
- Add the Template App Keycloak by HTTP.
- Ensure the Host Interface is set (Agent or SNMP interface is not strictly required, but Zabbix needs the IP/DNS to resolve {HOST.CONN}).

### 3. Configure Macros (Optional)

This template uses the standard {HOST.CONN} macro to find your server. If your Keycloak uses a custom port or path, you can override these macros on the Host level:

| Macro                     | Default Value                     | Description                          |
| ------------------------- | --------------------------------- | ------------------------------------ |
| `{$KEYCLOAK.HEALTH.URL}`  | `http://{HOST.CONN}:9000/health`  | Keycloak health endpoint             |
| `{$KEYCLOAK.METRICS.URL}` | `http://{HOST.CONN}:9000/metrics` | Keycloak Prometheus metrics endpoint |

---

### 📚 References & Technical Details

This template leverages Zabbix Prometheus preprocessing to efficiently parse metrics. Instead of making multiple HTTP requests, the template makes a single request to the metrics endpoint and uses dependent items to extract values.

* **Zabbix Prometheus Integration:** [Zabbix Documentation: Prometheus Checks](https://www.zabbix.com/documentation/current/en/manual/config/items/itemtypes/prometheus)
* **Keycloak Observability Guide:** [Keycloak Documentation: Enabling Metrics](https://www.keycloak.org/guides#observability)
* **Keycloak SLIs:** [Keycloak Service Level Indicators](https://www.keycloak.org/observability/keycloak-service-level-indicators)
---

## 🤝 Contributing
- Contributions are welcome!
- Bug reports
- New metric suggestions
- Threshold tuning

---

## 📄 License
- MIT License
  
---

## ⭐ Credits
Created and maintained by Lu Maw.
Inspired by Keycloak observability SLIs and production requirements.
