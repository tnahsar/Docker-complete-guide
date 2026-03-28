## Chapter 20: **Running Real Apps with Compose**

### 1️⃣ Start the App

```bash
docker-compose up -d
```

* `-d` → run in background
* All services start in correct order

Check running containers:

```bash
docker-compose ps
```

---

### 2️⃣ Stop the App

```bash
docker-compose down
```

* Stops containers
* Removes default network
* Volumes **persist unless explicitly removed**

```bash
docker-compose down -v  # removes volumes too
```

---

### 3️⃣ Restart Policies

Add to service:

```yaml
restart: unless-stopped
```

* Ensures container restarts on crash
* Useful for production-like behavior

Options:

* `no` → default
* `always` → always restart
* `unless-stopped` → restart unless manually stopped
* `on-failure` → restart only if exit code != 0

---

### 4️⃣ Environment Handling

#### Using `.env` file

```bash
# .env
DB_USER=admin
DB_PASSWORD=secret
```

`docker-compose.yml` can reference it:

```yaml
environment:
  - DB_USER=${DB_USER}
  - DB_PASSWORD=${DB_PASSWORD}
```

✅ Easier to manage configs and secrets locally

---

### 5️⃣ Real-World Example: Web + DB

* **Web**: Node.js app listening on port 80
* **DB**: Postgres 15, persistent volume
* Web app connects to DB via service name `db`
* One command starts entire app:

```bash
docker-compose up -d
```

Everything runs in isolated network, logs accessible via:

```bash
docker-compose logs -f
```

---

### 6️⃣ Quick Tips

* Always use **user-defined networks**
* Keep **volumes for persistent storage**
* Use **depends_on** + healthchecks for startup order
* Use `.env` for environment variables
* Avoid secrets in `docker-compose.yml`

---

## Chapter 20 Summary

* Docker Compose = **multi-container orchestration for local/dev environments**
* YAML file defines:

  * Services
  * Networks
  * Volumes
  * Dependencies
* Commands:

  * `docker-compose up` → start app
  * `docker-compose down` → stop app
  * `docker-compose logs` → inspect logs
* Environment variables and volumes are key for real-world usage

---