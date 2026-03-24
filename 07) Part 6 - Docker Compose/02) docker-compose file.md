## Chapter 19: **docker-compose.yml Explained Line by Line**

Let’s break down a **real example**.

### Example `docker-compose.yml`

```yaml
version: '3.9'

services:
  web:
    image: myapp:latest
    ports:
      - "8080:80"
    environment:
      - DB_HOST=db
      - DB_USER=admin
    depends_on:
      - db
    networks:
      - appnet

  db:
    image: postgres:15
    volumes:
      - db_data:/var/lib/postgresql/data
    environment:
      - POSTGRES_USER=admin
      - POSTGRES_PASSWORD=secret
    networks:
      - appnet

volumes:
  db_data:

networks:
  appnet:
```

---

### 1️⃣ `version`

```yaml
version: '3.9'
```

* Specifies Compose file format
* Must be compatible with your Docker version

---

### 2️⃣ `services`

* Each **container is a service**
* Service has:

  * **image** (or build context)
  * **ports**
  * **environment variables**
  * **volumes**
  * **networks**
  * **depends_on**

---

### 3️⃣ `ports`

```yaml
ports:
  - "8080:80"
```

* Maps host port → container port
* Same as `-p` in `docker run`

---

### 4️⃣ `environment`

```yaml
environment:
  - DB_HOST=db
  - DB_USER=admin
```

* Sets env vars for container
* Same as `-e` in `docker run`
* Can also use `.env` file for convenience

---

### 5️⃣ `depends_on`

```yaml
depends_on:
  - db
```

* Ensures **db starts before web**
* **Does not guarantee db is ready** — just container started

> For actual readiness, healthchecks are needed (advanced).

---

### 6️⃣ `volumes`

```yaml
volumes:
  db_data:
```

* Persistent storage
* Can be used by multiple services
* Keeps data safe even if container is deleted

---

### 7️⃣ `networks`

```yaml
networks:
  appnet:
```

* Custom network for services to communicate by **service name**
* Example: web → db via `DB_HOST=db`

---