
---

# 📘 PART 6 — Docker Compose (Real-World Usage)

Docker Compose is all about **running multi-container apps easily**.
Most real-world apps have **more than one container**: e.g., web app + database + cache.

---

## Chapter 18: **Why Docker Compose Exists**

### 1️⃣ The Problem: Single Containers Aren’t Enough

Earlier, we learned how to run one container:

```bash
docker run -d -p 8080:80 myapp
```

* Works for **one service**
* But real apps often need **database, cache, queue, search engine**

Example real-world app:

```
[Web App] → [PostgreSQL DB] → [Redis Cache]
```

Without Compose:

* You’d need **3 separate `docker run` commands**
* Handle **networks, ports, environment variables** manually
* Reproducibility is tough

---

### 2️⃣ Multi-Service Apps

Docker Compose lets you define **all services in a single YAML file**, e.g.:

* **services:** web, database, cache
* **networks:** define communication
* **volumes:** persistent storage
* **environment variables:** configs

✅ One command starts everything:

```bash
docker-compose up
```

---

### 3️⃣ Key Advantages

| Feature              | Manual `docker run` | Docker Compose              |
| -------------------- | ------------------- | --------------------------- |
| Multi-container apps | Hard                | Easy                        |
| Networks             | Manual              | Automatic                   |
| Persistent data      | Manual              | Built-in volume support     |
| Environment handling | Manual              | `.env` or inline            |
| Reproducible         | No                  | Yes, YAML = source of truth |

> Compose is basically **“infrastructure as code” for local Docker apps**.

---