
---

# 📘 PART 4 — Containers in Real Life

## Chapter 14: **Volumes & Bind Mounts**

This chapter answers the most common beginner shock:

> “Why did my data disappear when the container stopped?”

---

## 1️⃣ Why Containers Lose Data (Root Cause)

Containers are **not machines**.
They are **processes with a temporary filesystem**.

When a container runs:

```
Image layers (read-only)
+ Writable container layer (temporary)
```

Anything written:

* Goes into the **writable layer**
* Is **deleted** when container is removed

This is **by design**, not a bug.

---

## 2️⃣ Docker Storage Options (Clear Comparison)

Docker gives **three storage options**:

| Storage Type   | Persistent | Managed by Docker | Use Case     |
| -------------- | ---------- | ----------------- | ------------ |
| Writable layer | ❌ No       | Yes               | ❌ Never      |
| **Volume**     | ✅ Yes      | ✅ Yes             | ⭐ Production |
| **Bind mount** | ✅ Yes      | ❌ No              | Dev / local  |

---

## 3️⃣ Docker Volumes (Best Practice)

### What Is a Volume?

> A **Docker volume** is a directory stored on the host, **managed by Docker**, outside the container lifecycle.

---

### Create a Volume

```bash
docker volume create app_data
```

List volumes:

```bash
docker volume ls
```

---

### Use a Volume

```bash
docker run -it \
  -v app_data:/data \
  ubuntu bash
```

Inside container:

```bash
echo "important" > /data/info.txt
exit
```

Delete container → data still exists ✅

---

### Why Volumes Are Recommended

✅ Safe
✅ Portable
✅ Easy backup
✅ Best for databases
✅ Survive container removal

---

## 4️⃣ Bind Mounts (Host-Controlled)

### What Is a Bind Mount?

> A **bind mount** maps a specific host directory into a container.
> Changes in the host directory immediately reflect in the container, and vice versa.
> You have full control over the exact path on your host.

Example:

```
Syntax --> docker run -v /host/path:/container/path myimage

```
```bash
docker run -it \
  -v /home/user/app:/app \
  ubuntu bash
```

Changes:

* From container → host
* From host → container

---

### When to Use Bind Mounts

✅ Local development
✅ Live code reload
❌ Databases
❌ Production data

---

### Volume vs Bind Mount (Clear Table)

| Feature           | Volume | Bind Mount |
| ----------------- | ------ | ---------- |
| Managed by Docker | Yes    | No         |
| Host path visible | No     | Yes        |
| Portable          | Yes    | No         |
| Safe for prod     | Yes    | Risky      |
| Dev friendly      | OK     | Best       |

---

## 5️⃣ Anonymous Volumes (Common Trap)

Example:

```bash
docker run -v /data ubuntu
```

Docker:

* Creates volume automatically
* Gives random name

Problems:
❌ Hard to find
❌ Hard to clean
❌ Easy to forget

👉 **Avoid unless necessary**

---

## 6️⃣ Mounting Hides Image Files (Very Important)

If image contains:

```
/app/config.yml
```

And you mount volume:

```bash
-v myvol:/app
```

Result:

* `/app` from image is **hidden**
* Volume contents override it

This surprises many beginners.

---

## 7️⃣ Real-World Examples

### Database (Correct Way)

```bash
docker run -d \
  -v pgdata:/var/lib/postgresql/data \
  postgres
```

---

### Application Logs

```bash
docker run -d \
  -v logs:/app/logs \
  myapp
```

---

### Local Development

```bash
docker run -it \
  -v $(pwd):/app \
  node
```

---

## 8️⃣ Inspect Volumes & Mounts

### Inspect Volume

```bash
docker volume inspect app_data
```

---

### Inspect Container Mounts

```bash
docker inspect mycontainer
```

Look for:

```json
"Mounts": []
```

---

## 9️⃣ Volume Lifecycle (Important)

| Action           | Data    |
| ---------------- | ------- |
| Stop container   | ✅ stays |
| Remove container | ✅ stays |
| Remove image     | ✅ stays |
| Remove volume    | ❌ gone  |

Remove volume:

```bash
docker volume rm app_data
```

Remove unused volumes:

```bash
docker volume prune
```

⚠️ **Prune deletes data permanently**

---

## 🔟 Common Mistakes (Learn from Others)

❌ Forgetting volumes for DBs
❌ Using bind mounts in prod
❌ Mounting over app directory unintentionally
❌ Relying on container filesystem
❌ Deleting volumes accidentally

---

## Golden Rules (Memorize These)

1️⃣ Containers are disposable
2️⃣ Data must live outside containers
3️⃣ Volumes for prod
4️⃣ Bind mounts for dev
5️⃣ Never trust writable layer

---

## Chapter 14 Summary

* Containers lose data by design
* Volumes persist data safely
* Bind mounts are for development
* Mounting overrides image paths
* Always plan data persistence

---