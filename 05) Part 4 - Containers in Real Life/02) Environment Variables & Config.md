 
---

# 📘 PART 4 — Containers in Real Life

## Chapter 13: **Environment Variables & Config**

This chapter answers:

> “How do I configure containers without rebuilding images?”
> “Where do passwords, URLs, flags go?”
> “What should I NEVER put in an image?”

---

## 1️⃣ What Are Environment Variables (In Containers)?

Environment variables are **key–value pairs** available to a process.

Inside a container, they are used for:

* Database URLs
* Ports
* Feature flags
* API keys
* Environment (dev / prod)

Example:

```bash
ENV NODE_ENV=production
```

Inside container:

```bash
echo $NODE_ENV
```

---

## 2️⃣ `ENV` (Dockerfile) vs `--env` (Runtime)

This distinction is critical.

---

### `ENV` in Dockerfile

```Dockerfile
ENV APP_PORT=3000
```

📌 Characteristics:

* Baked into the image
* Present in **every container**
* Visible via `docker inspect`
* Saved in image history

✅ Good for:

* Default values
* Non-sensitive config

❌ Bad for:

* Secrets
* Environment-specific values

---

### `--env` / `-e` in `docker run`

```bash
docker run -e APP_PORT=3000 myapp
```

📌 Characteristics:

* Provided at runtime
* Overrides Dockerfile `ENV`
* Not baked into image
* Still visible via inspect

✅ Good for:

* Environment-specific config
* URLs, ports, flags

---

### Priority Order

```text
docker run -e  >  Dockerfile ENV
```

Runtime always wins.

---

## 3️⃣ `.env` Files (Convenience Feature)

Instead of passing many `-e` flags:

```bash
docker run --env-file .env myapp
```

### `.env` file example:

```
DB_HOST=localhost
DB_USER=admin
DB_PASSWORD=secret
```

Docker loads all variables from file.

---

### Important ⚠️

* `.env` is **NOT encrypted**
* `.env` should **never be committed**
* Always add `.env` to `.gitignore`

---

## 4️⃣ Accessing Env Vars Inside Containers

### Shell

```bash
echo $DB_HOST
```

---

### Application Code

**Node.js**

```js
process.env.DB_HOST
```

**Python**

```python
import os
os.environ.get("DB_HOST")
```

**Java**

```java
System.getenv("DB_HOST")
```

---

## 5️⃣ Common Real-World Pattern

### Image (Dockerfile)

```Dockerfile
ENV PORT=3000
CMD ["node", "server.js"]
```

### Runtime (prod vs dev)

```bash
docker run -e PORT=8080 myapp
```

Same image, different behavior.
✅ This is how Docker is meant to be used.

---

## 6️⃣ Secrets — What NOT to Do (Very Important)

### ❌ DO NOT put secrets in Dockerfile

```Dockerfile
ENV DB_PASSWORD=supersecret
```

Why this is bad:

* Stored forever in image
* Visible in `docker history`
* Shared with anyone who pulls image

---

### ❌ DO NOT hardcode secrets in app code

```js
const password = "supersecret";
```

* Ends up in git
* Ends up in image
* Impossible to rotate safely

---

### ❌ DO NOT commit `.env` files

Even private repos leak.

---

## 7️⃣ So How Do We Handle Secrets (Basic Docker Way)?

### Acceptable (Basic Level)

```bash
docker run -e DB_PASSWORD=secret myapp
```

Pros:

* Not in image
* Easy

Cons:

* Visible in inspect
* Visible in process list

---

### Better (Still Simple)

Use **env-file** but keep it secure:

```bash
docker run --env-file /secure/path/.env myapp
```

Permissions:

```bash
chmod 600 .env
```

---

### Real Production (Preview)

In real systems:

* Docker Secrets
* Kubernetes Secrets
* Vault
* Cloud secret managers

We’ll talk about this later when you reach orchestration.

---

## 8️⃣ Inspecting Env Vars (Security Reality)

```bash
docker inspect myapp
```

Anyone with Docker access can see env vars.

➡️ Docker alone is **not a secrets manager**.

---

## 9️⃣ Best Practices (Memorize This)

✅ Image = code
✅ Env vars = config
❌ Secrets ≠ image
❌ Secrets ≠ git

Rule of thumb:

> **Build once, configure many times**

---

## Chapter 13 Summary

* `ENV` → baked into image
* `-e / --env` → runtime config
* `.env` → convenience, not security
* Never store secrets in images
* Docker is not a secrets manager

---