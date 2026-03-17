
---

# 📘 PART 4 — Containers in Real Life

This part is about **day-to-day Docker usage**:

* Debugging running containers
* Inspecting what’s really happening
* Interacting with containers safely

---

## Chapter 12: **Logs, Exec, Inspect**

This chapter answers:

> *“My container is running (or crashed). What now?”*

---

## 1️⃣ `docker logs` — See Container Output

### What `docker logs` Does

`docker logs` shows:

* **STDOUT**
* **STDERR**

of the **main process** inside the container.

Docker **does not invent logs** — it just captures what the process prints.

---

### Basic Usage

```bash
docker logs <container_name_or_id>
```

Example:

```bash
docker logs myapp
```

---

### Follow Logs (Like `tail -f`)

```bash
docker logs -f myapp
```

Useful for:

* Watching live traffic
* Debugging crashes
* Monitoring startup behavior

---

### Show Last N Lines

```bash
docker logs --tail 50 myapp
```

---

### Add Timestamps

```bash
docker logs -t myapp
```

---

### Important Reality Check ⚠️

❌ Docker does NOT rotate logs by default
❌ Logs grow forever
❌ Disk can fill up

👉 In production, logs should go to:

* stdout → log collector
* or logging drivers (ELK, Loki, etc.)

---

## 2️⃣ `docker exec` — Enter a Running Container

### What `docker exec` Really Does

`docker exec`:

* Runs **another process**
* Inside an **already running container**

It does **NOT**:

* Restart the container
* Change image
* Affect the main process

---

### Open a Shell Inside Container

```bash
docker exec -it myapp bash
```

If bash isn’t available:

```bash
docker exec -it myapp sh
```

---

### Run a Single Command

```bash
docker exec myapp ls /app
```

---

### Real-World Use Cases

✅ Debug broken apps
✅ Check environment variables
✅ Inspect files
❌ Not for permanent fixes

> Any change you make is lost when container is recreated.

---

## 3️⃣ `docker inspect` — Truth Source of Docker

### What `docker inspect` Shows

`docker inspect` returns:

* JSON metadata
* Network info
* Volumes
* Env vars
* Entrypoint / CMD
* Mount paths
* IP address

---

### Basic Usage

```bash
docker inspect myapp
```

Output is **JSON** (big and ugly).

---

### Filter Important Fields

```bash
docker inspect -f '{{.State.Status}}' myapp
```

Examples:

```bash
docker inspect -f '{{.NetworkSettings.IPAddress}}' myapp
docker inspect -f '{{.Config.Env}}' myapp
docker inspect -f '{{.Mounts}}' myapp
```

---

### When to Use `inspect`

✅ Debug networking
✅ Check mounts
✅ Verify env variables
✅ See restart policies

---

## 4️⃣ Debugging Containers (Real-World Flow)

### Scenario: Container Exits Immediately

1️⃣ Check status:

```bash
docker ps -a
```

2️⃣ Read logs:

```bash
docker logs myapp
```

3️⃣ If needed, run interactively:

```bash
docker run -it myimage sh
```

---

### Scenario: App Is Running But Broken

1️⃣ Exec into container:

```bash
docker exec -it myapp bash
```

2️⃣ Check:

* Files
* Env vars
* Permissions
* Network access

---

### Scenario: Port Not Accessible

1️⃣ Inspect port mapping:

```bash
docker ps
```

2️⃣ Inspect container:

```bash
docker inspect myapp
```

---

## 5️⃣ Commands Summary

| Command          | Purpose                  |
| ---------------- | ------------------------ |
| `docker logs`    | View app output          |
| `docker logs -f` | Follow logs              |
| `docker exec`    | Run command in container |
| `docker inspect` | Full container metadata  |

---

## Chapter 12 Summary

* Docker logs = process output
* Docker exec = temporary debugging
* Docker inspect = single source of truth
* Debug containers methodically
* Never rely on exec for permanent changes

---