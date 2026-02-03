# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2026-02-03
### Added
- Initial release of the **Keycloak by HTTP** Zabbix template.
- Full compatibility with **Keycloak 20+ (Quarkus)** and **Zabbix 7.2**.
- **Master Item** architecture: Single HTTP request to `/metrics` populates all dependent items for efficiency.
- **Health Monitoring:**
    - Global health status (`/health`) check.
    - Process uptime and restart detection.
- **Database (Agroal) Metrics:**
    - Active, Available, and Awaiting connection counts.
    - Max blocking time monitoring.
    - Pool utilization percentage calculation.
- **JVM Performance Metrics:**
    - CPU Load (Process & System).
    - Memory Usage (Heap & Non-Heap/Metaspace).
    - Garbage Collection pause times.
    - Thread states (Blocked, Runnable, Total).
- **HTTP & SLI Monitoring:**
    - Global active request count.
    - Login-specific request rates and error rates.
    - Login response time (latency) tracking.
- **Triggers:**
    - added 9 production-ready triggers (Disaster/High severity) for DB pool exhaustion, high error rates, and system saturation.
