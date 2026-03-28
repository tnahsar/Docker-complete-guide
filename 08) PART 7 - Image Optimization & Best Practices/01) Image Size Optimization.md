
---

# 📘 PART 7: Image Optimization & Best Practices

---

## 🔹 Chapter 21: Image Size Optimization (Very Important)

> **Interview mindset:**
> “Smaller images = faster builds, faster pulls, lower attack surface, lower cost”

---

## 21.1 Why Image Size Matters

Large images cause:

* Slow CI/CD pipelines
* Slow container startup
* More bandwidth usage
* Bigger security attack surface

📌 **Rule of thumb:**

> If your image is > 500MB → something is wrong.

---

## 21.2 Choosing the Right Base Image

### Alpine vs Debian vs Distroless

### 🔸 Alpine

* Size: ~5–7 MB
* Uses `musl` libc
* Very small, fast pull

✅ Good for:

* Simple apps
* Static binaries (Go, Rust)

❌ Problems:

* Some binaries don’t work
* Debugging is harder
* Missing common tools

---

### 🔸 Debian / Ubuntu

* Size: 70–120 MB
* Uses `glibc`
* Very compatible

✅ Good for:

* Java, Python, Node apps
* When debugging tools needed

❌ Larger than Alpine

---

### 🔸 Distroless (Advanced)

* Contains **only runtime**
* No shell, no package manager

✅ Best for:

* Production security
* Minimal attack surface

❌ Hard to debug

📌 **Interview answer:**

> “Alpine for size, Debian for compatibility, Distroless for production security.”

---

## 21.3 Multi-Stage Builds (MOST IMPORTANT)

### ❌ Problem (Single-stage build)

```dockerfile
FROM node:18
COPY . .
RUN npm install
RUN npm run build
CMD ["npm", "start"]
```

👉 Final image contains:

* Source code
* Build tools
* Node modules
* Cache files

---

### ✅ Solution: Multi-stage build

```dockerfile
# Build stage
FROM node:18 AS builder
WORKDIR /app
COPY package*.json .
RUN npm install
COPY . .
RUN npm run build

# Runtime stage
FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/dist .
CMD ["node", "server.js"]
```

### What happened?

* Build tools stay behind
* Only runtime artifacts go to final image

📌 **Massive size reduction** (often 70–90%)

---

## 21.4 Cleaning Package Cache (Silent Image Killer)

### ❌ Bad

```dockerfile
RUN apt-get update
RUN apt-get install -y curl
```

Each RUN = new layer
Cache stays forever.

---

### ✅ Good

```dockerfile
RUN apt-get update && \
    apt-get install -y curl && \
    rm -rf /var/lib/apt/lists/*
```

📌 **One layer, no cache leftovers**

---

## 21.5 Reducing Layers (Layer Hygiene)

Each Dockerfile instruction = layer.

### ❌ Bad

```dockerfile
RUN mkdir /app
RUN cd /app
RUN npm install
```

---

### ✅ Good

```dockerfile
WORKDIR /app
RUN npm install
```

📌 Fewer layers → smaller image → better cache reuse

---

## 21.6 `.dockerignore` (Often Ignored, Very Powerful)

Without `.dockerignore`:

* `.git`
* `node_modules`
* logs
* local build artifacts

➡️ All copied into build context ❌

---

### ✅ Example `.dockerignore`

```
.git
node_modules
*.log
.env
dist
```

📌 **Huge build speed improvement**

---

## 21.7 COPY vs ADD (Optimization Angle)

| Instruction | Use                 |
| ----------- | ------------------- |
| COPY        | Preferred           |
| ADD         | Avoid unless needed |

ADD:

* Auto-extracts archives
* Downloads URLs (bad practice)

📌 **Best practice:** Always use `COPY` unless you *need* ADD behavior.

---

## 21.8 Real-World Image Optimization Checklist

✅ Small base image
✅ Multi-stage builds
✅ Clean package cache
✅ Minimal layers
✅ `.dockerignore`
✅ No dev dependencies in final image

---

## 💡 Interview Power Line (Memorize This)

> “I optimize Docker images using multi-stage builds, minimal base images, layer optimization, and by removing unnecessary build artifacts and caches.”

---