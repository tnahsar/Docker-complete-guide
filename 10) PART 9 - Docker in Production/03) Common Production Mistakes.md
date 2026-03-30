
---

# 🚨 Chapter 28: Common Production Mistakes (Very Important)

---

## 28.1 Using Mutable Containers ❌

### The Mistake

* SSH into running container
* Install packages
* Change config live

```bash
apt install vim
```

---

### Why This Is Bad

* Changes are lost on restart
* No audit trail
* Configuration drift
* Impossible to reproduce

---

### Correct Approach ✅

* Rebuild image
* Redeploy container

📌 **Containers must be immutable**.

---

## 28.2 Baking Secrets Into Images ❌

### The Mistake

```dockerfile
ENV DB_PASSWORD=secret123
```

---

### Why This Is Dangerous

* Secrets stored in image layers
* Visible via `docker inspect`
* Leaked in registries
* Permanent exposure

---

### Correct Approach ✅

* Inject secrets at runtime
* Use secret managers

📌 **Secrets never belong in images**.

---

## 28.3 Not Setting Resource Limits ❌

### The Mistake

```bash
docker run myapp
```

No limits → unlimited CPU & memory.

---

### Result

* One container eats all memory
* Host crashes
* Multiple services go down

---

### Correct Approach ✅

```bash
docker run -m 512m --cpus=1 myapp
```

📌 Memory limits are **mandatory**.

---

## 28.4 Using `latest` Tag in Production ❌

### The Mistake

```bash
docker pull nginx:latest
```

---

### Why This Breaks Production

* Image changes silently
* No rollback
* Non-repeatable builds

---

### Correct Approach ✅

```bash
nginx:1.25.2
```

📌 Pin versions always.

---

## 28.5 Writing Logs Inside Container ❌

### The Mistake

```text
/var/log/app.log
```

---

### Why This Is Bad

* Logs disappear on restart
* Hard to aggregate
* Disk fills up

---

### Correct Approach ✅

* Log to stdout/stderr
* Centralize logs

---

## 28.6 Not Using `.dockerignore` ❌

### The Mistake

* `.git`
* `node_modules`
* logs copied into image

---

### Impact

* Huge images
* Slow builds
* Leaked secrets

---

### Correct Approach ✅

Use `.dockerignore`.

---

## 28.7 Running as Root ❌

### The Mistake

Default user = root.

---

### Risk

* Privilege escalation
* Container escape risk

---

### Correct Approach ✅

* Use non-root user

---

## 28.8 No Health Checks ❌

### The Mistake

* Container is “running”
* App is dead

---

### Impact

* Traffic routed to broken app
* Silent failures

---

### Correct Approach ✅

```dockerfile
HEALTHCHECK CMD curl -f http://localhost/health || exit 1
```

---

## 28.9 Overloading Containers ❌

### The Mistake

* App
* Cron
* Log rotation
* Monitoring

All in one container.

---

### Correct Approach ✅

* One process per container
* Sidecars if needed

---

## 28.10 No Image Scanning ❌

### The Mistake

* Vulnerable libraries shipped
* Security risks

---

### Correct Approach ✅

* Scan images in CI
* Fix critical CVEs

---

## 28.11 Summary Table (Interview Gold)

| Mistake            | Fix                 |
| ------------------ | ------------------- |
| Mutable containers | Immutable builds    |
| Secrets in image   | Runtime injection   |
| No limits          | CPU & memory limits |
| latest tag         | Pinned versions     |
| File-based logs    | stdout logging      |
| Root user          | Non-root            |
| No healthcheck     | Add healthchecks    |

---

## 🧠 Interview Power Answer

> “Most Docker production issues come from mutable containers, missing resource limits, improper secrets handling, and lack of observability. I focus on immutability, security, and monitoring.”

---

## 🔥 One-Line Summary

> Docker outages are rarely Docker’s fault — they’re configuration mistakes.

---