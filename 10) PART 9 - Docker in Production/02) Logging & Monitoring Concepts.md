
---

# 📊 Chapter 27: Logging & Monitoring Concepts

### (Docker in Production)

---

## 27.1 Core Principle (Must Understand)

> Containers are **ephemeral** — logs and metrics must live **outside** the container.

If a container dies:

* Filesystem → gone
* Logs inside container → gone ❌

---

## 27.2 Docker Logging Philosophy

### Golden Rule

> **Applications should log to stdout and stderr**

Docker captures logs automatically.

---

### Example

```bash
docker logs mycontainer
```

Docker is just **forwarding stdout/stderr**.

📌 Docker is **not** a logging system.

---

## 27.3 Docker Logging Drivers

Docker uses **logging drivers** to ship logs.

### Common Drivers

| Driver    | Use Case                 |
| --------- | ------------------------ |
| json-file | Default, local debugging |
| syslog    | Linux systems            |
| fluentd   | Centralized logging      |
| awslogs   | AWS CloudWatch           |
| gelf      | Graylog                  |

---

### Example: Fluentd

```bash
docker run \
  --log-driver=fluentd \
  myapp
```

📌 Logs go directly to log pipeline.

---

## 27.4 Centralized Logging Architecture (Docker)

```
Container stdout/stderr
        ↓
Docker logging driver
        ↓
Log agent (Fluentd / Filebeat)
        ↓
Central log store (ELK / CloudWatch)
```

---

## 27.5 Common Logging Mistakes

❌ Writing logs to files inside container
❌ SSH into container to read logs
❌ Huge verbose logs
❌ No log rotation

---

## 27.6 Metrics in Docker (Infrastructure Level)

Docker exposes metrics like:

* CPU usage
* Memory usage
* Network IO
* Disk IO

---

### Quick Check

```bash
docker stats
```

📌 This is **node-level container metrics**, not application metrics.

---

## 27.7 Production Metrics Stack for Docker

Typical setup:

* **cAdvisor** → container metrics
* **node-exporter** → host metrics
* **Prometheus** → scrape metrics
* **Grafana** → dashboards

---

## 27.8 Application Metrics (Inside Containers)

Containers don’t magically expose app metrics.

You must:

* Instrument app
* Expose `/metrics`
* Use Prometheus client libraries

📌 Docker only handles runtime, not app observability.

---

## 27.9 Alerts in Docker-Based Systems

Alert on:

* Container restarts
* CPU saturation
* Memory usage
* OOMKilled events
* Disk pressure

📌 Alert on **symptoms**, not noise.

---

## 27.10 Health Checks (Production Must-Have)

### Dockerfile

```dockerfile
HEALTHCHECK CMD curl -f http://localhost:8080/health || exit 1
```

Docker:

* Marks container as healthy/unhealthy
* Doesn’t auto-restart unless configured

---

## 27.11 Logging & Monitoring in Docker Compose

```yaml
services:
  app:
    logging:
      driver: json-file
      options:
        max-size: "10m"
        max-file: "3"
```

📌 Prevents disk exhaustion.

---

## 27.12 Real-World Best Practices Summary

✅ Log to stdout/stderr
✅ Centralize logs
✅ Monitor CPU & memory
✅ Alert on restarts & OOM
✅ Avoid logging inside container filesystem

---

## 🧠 Interview Power Answer

> “In production Docker environments, I rely on stdout-based logging with centralized log aggregation, container-level metrics monitoring, health checks, and alerting on restarts and resource saturation.”

---

## 🔥 One-Line Summary

> Docker runs containers; observability must live outside them.

---