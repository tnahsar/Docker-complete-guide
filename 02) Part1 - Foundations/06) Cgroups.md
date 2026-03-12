## How are resources assigned using cgroups?

## Do we mention them in Dockerfile?

### Short answer

❌ **NOT in Dockerfile**
✅ **At runtime (docker run / compose / orchestrator)**

---

## Why NOT Dockerfile?

Dockerfile is for:

* building images
* installing software
* defining startup command

It is **NOT** for:

* machine-specific resource limits

Why?

Because:

* the same image runs on different machines
* different environments need different limits

---

## How we actually assign resources

### Example: CPU and memory limits

```bash
docker run \
  --memory=512m \
  --cpus=1 \
  nginx
```

This tells Docker:

> “Create cgroups for this container
> Limit memory to 512MB
> Limit CPU to 1 core”

Docker:

* talks to Linux kernel
* creates cgroup entries
* attaches the container process to them

---

### Another example: Memory hard limit

```bash
docker run -m 256m nginx
```

If the container tries to exceed 256MB:
💥 **OOM Killer kills it**

---

### With Docker Compose

```yaml
services:
  app:
    image: myapp
    deploy:
      resources:
        limits:
          memory: 512M
          cpus: "1.0"
```

(Compose / Swarm / Kubernetes all use cgroups under the hood)

---

### Key idea 🧠

> **cgroups are kernel features**
> Docker just configures them

Docker itself doesn’t enforce limits —
**Linux kernel does.**

---