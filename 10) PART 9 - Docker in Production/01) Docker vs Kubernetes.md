
---

# ⚙️ Chapter 25: Resource Management

### (CPU, Memory, OOM Killer — Explained Simply)

---

## 25.1 Why Resource Management Matters

By default:

* Containers can use **all CPU**
* Containers can use **all memory**

👉 One bad container can **crash the whole host**.

📌 **Containers are isolated, not limited — unless you configure limits.**

---

## 25.2 CPU Management in Docker

### CPU is **compressible**

* If CPU is busy → container slows down
* System doesn’t crash

---

### 25.2.1 CPU Shares (Relative Weight)

```bash
docker run --cpu-shares=512 app
```

* Default = 1024
* Only matters when CPU is under contention

📌 Rarely used in production today.

---

### 25.2.2 CPU Quota & Period (Hard Limit)

```bash
docker run --cpus="1.5" app
```

Meaning:

* Container can use **1.5 CPU cores**

Internally:

* `--cpu-quota`
* `--cpu-period`

📌 Most commonly used CPU control.

---

### 25.2.3 Pinning CPUs (Advanced)

```bash
docker run --cpuset-cpus="0,1" app
```

* Container runs only on CPU 0 & 1
* Used for performance-sensitive workloads

---

## 25.3 Memory Management (CRITICAL)

### Memory is **NOT compressible**

If memory is exhausted → **OOM happens**

---

### 25.3.1 Setting Memory Limits

```bash
docker run -m 512m app
```

Limits container to 512MB RAM.

---

### 25.3.2 Memory + Swap

```bash
docker run -m 512m --memory-swap=512m app
```

* No swap allowed
* Strict memory control

📌 Prevents system-wide memory pressure.

---

## 25.4 OOM Killer (Very Important)

### What is OOM?

Out Of Memory.

When:

* Container exceeds memory limit
* Host runs out of memory

---

### What Happens?

* Linux OOM killer **kills processes**
* Usually kills the container process

You’ll see:

```text
OOMKilled: true
Exit Code: 137
```

📌 Common production issue.

---

## 25.5 Preventing OOM Issues

### Best Practices

✅ Set memory limits
✅ Monitor memory usage
✅ Fix memory leaks
✅ Avoid unlimited JVM heap
✅ Don’t overcommit host memory

---

### JVM Example (Common Mistake)

❌ JVM sees host memory:

```text
Uses 8GB → OOM
```

✅ Fix:

```bash
-Xmx256m
```

---

## 25.6 CPU Throttling vs OOM (Interview Favorite)

| Resource | What Happens |
| -------- | ------------ |
| CPU      | Throttled    |
| Memory   | Killed (OOM) |

📌 Important conceptual difference.

---

## 25.7 Resource Limits in docker-compose

```yaml
services:
  app:
    image: myapp
    deploy:
      resources:
        limits:
          cpus: "1.0"
          memory: 512M
```

📌 `deploy` is Swarm-style; runtime behavior differs.

---

## 25.8 Monitoring Resource Usage

```bash
docker stats
```

Shows:

* CPU %
* Memory usage
* Network IO

📌 Quick troubleshooting tool.

---

## 25.9 Real-World Production Recommendations

| Resource   | Recommendation        |
| ---------- | --------------------- |
| CPU        | Set reasonable limits |
| Memory     | Always set limit      |
| Swap       | Disable or restrict   |
| Monitoring | Alert on usage        |
| JVM        | Explicit heap size    |

---

## 🧠 Interview Power Answer

> “I always set CPU and memory limits for containers. CPU is throttled under pressure, but memory exhaustion triggers OOM kills, so memory limits and monitoring are critical in production.”

---

## 🔥 One-Line Summary

> CPU slows you down; memory kills you.

---