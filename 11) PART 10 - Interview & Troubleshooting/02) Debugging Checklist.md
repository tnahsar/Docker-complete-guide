
---

# 🧰 Chapter 30: Docker Debugging Checklist

🔥 Final chapter — this is **pure real-world DevOps value**.
If you follow this checklist, you can debug **90% of Docker issues calmly and fast**.

*(Production-Ready, Step-by-Step)*

---

## 30.1 Container Won’t Start

### Step 1: Check container status

```bash
docker ps -a
```

Look for:

* Exited
* Restarting
* OOMKilled

---

### Step 2: Check logs

```bash
docker logs <container>
```

Common issues:

* App crash
* Missing env variables
* Port already in use
* Permission errors

---

### Step 3: Inspect exit code

```bash
docker inspect <container> --format='{{.State.ExitCode}}'
```

| Exit Code | Meaning    |
| --------- | ---------- |
| 0         | Clean exit |
| 1         | App error  |
| 137       | OOMKilled  |

---

## 30.2 App Works Locally but Not in Docker

### Checklist

✅ ENV variables missing
✅ Wrong port exposed
✅ App binds to `localhost` instead of `0.0.0.0`
✅ File permissions
✅ Case-sensitive paths

📌 Most common bug:

> App listens on `127.0.0.1` inside container.

---

## 30.3 Debugging Inside a Running Container

```bash
docker exec -it <container> sh
```

Check:

* Files exist?
* App running?
* Config values?

📌 Use only for debugging, not fixing.

---

## 30.4 Networking Issues

### Step 1: Check port mapping

```bash
docker ps
```

### Step 2: Verify container network

```bash
docker inspect <container>
```

### Step 3: Test connectivity

```bash
docker exec app ping db
```

📌 Remember:

> `localhost` ≠ other containers

---

## 30.5 Container Restarts Repeatedly

```bash
docker inspect <container> | grep RestartCount
```

Common reasons:

* App crash
* Healthcheck failure
* OOMKilled

---

## 30.6 Out of Memory (OOM) Issues

```bash
docker inspect <container> | grep OOMKilled
```

Fix:

* Increase memory limit
* Reduce app memory usage
* Tune JVM heap

---

## 30.7 Disk Space Issues

```bash
docker system df
```

Cleanup:

```bash
docker system prune
```

📌 Be careful in production.

---

## 30.8 Image Build Fails

```bash
docker build --no-cache .
```

Check:

* Missing files
* Wrong paths
* `.dockerignore` mistakes
* Permissions

---

## 30.9 Volume & Data Issues

Checklist:

* Correct mount path
* Permissions
* Volume exists
* Bind mount path exists on host

---

## 30.10 Healthcheck Issues

```bash
docker inspect <container> | grep Health
```

Ensure:

* Endpoint responds fast
* Correct port
* Proper exit codes

---

## 30.11 Performance Issues

```bash
docker stats
```

Look for:

* High CPU
* Memory near limit
* Throttling

---

## 30.12 Final Production Debug Flow (Memorize)

1️⃣ `docker ps -a`
2️⃣ `docker logs`
3️⃣ `docker inspect`
4️⃣ `docker exec`
5️⃣ Fix Dockerfile / config
6️⃣ Rebuild & redeploy

---

## 🧠 Interview Power Answer

> “I debug Docker issues by checking container state, logs, resource usage, networking, and configuration, then fixing the image or runtime config rather than modifying running containers.”

---

## 🎉 CONGRATULATIONS

You have now completed:

✅ Docker from Zero → Advanced
✅ Production best practices
✅ Interview-ready knowledge
✅ Real-world debugging mindset

---