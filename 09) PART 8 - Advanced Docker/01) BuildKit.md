
---

# ⚙️ Chapter 23: BuildKit (Modern Docker Builds)

---

## 23.1 What Is BuildKit?

**BuildKit** is Docker’s **modern build engine** that replaces the legacy `docker build` behavior.

Think of it as:

> “Docker build, but **faster, smarter, and more secure**.”

---

## 23.2 Why BuildKit Was Introduced

### Problems with old Docker build

* Builds were slow
* Everything ran **sequentially**
* Poor cache usage
* No secure way to pass secrets
* Hard to reuse cache across builds

---

### BuildKit solves:

✅ Parallel builds
✅ Better caching
✅ Secure secrets handling
✅ Cache reuse (local & remote)
✅ Smaller images

---

## 23.3 Enabling BuildKit

### Option 1: One-time

```bash
DOCKER_BUILDKIT=1 docker build .
```

### Option 2: Permanently (recommended)

```bash
export DOCKER_BUILDKIT=1
```

Docker Desktop:

* Enabled by default

---

## 23.4 Parallel Build Execution (Key Feature)

### Old behavior:

```dockerfile
RUN npm install
RUN npm run build
```

👉 Executed strictly in order.

---

### BuildKit behavior:

* Independent layers run **in parallel**
* Faster build times

📌 Especially powerful in **multi-stage builds**.

---

## 23.5 Smart Cache Usage (Much Better Than Old Build)

BuildKit:

* Understands dependency graph
* Reuses cache more aggressively
* Avoids unnecessary rebuilds

### Example

```dockerfile
COPY package.json .
RUN npm install
COPY . .
```

Only source code change?
👉 `npm install` layer reused 😎

---

## 23.6 Cache Mounts (🔥 Game Changer)

### Problem

Every build downloads dependencies again.

---

### Solution: Cache mount

```dockerfile
RUN --mount=type=cache,target=/root/.npm \
    npm install
```

📌 Dependencies cached **outside image layers**
📌 Massive CI speed-up

Common cache mounts:

* npm
* pip
* maven
* go build cache

---

## 23.7 Secrets in Build (SECURE WAY)

### ❌ Old unsafe way

```dockerfile
ENV NPM_TOKEN=xxxx
```

Secrets become part of image ❌

---

### ✅ BuildKit secure secret

```dockerfile
RUN --mount=type=secret,id=npm_token \
    npm install
```

Build command:

```bash
docker build \
  --secret id=npm_token,src=.npmrc .
```

📌 Secret:

* Not stored in image
* Not visible in layers
* Not leaked to registry

---

## 23.8 SSH Forwarding (Advanced but Real)

Used for:

* Private Git repos
* Private submodules

```dockerfile
RUN --mount=type=ssh git clone git@github.com:org/private-repo.git
```

Build command:

```bash
docker build --ssh default .
```

📌 No SSH keys baked into image.

---

## 23.9 Build Output Control

BuildKit gives **cleaner logs**:

* Progress UI
* Clear steps
* Easier debugging

Example:

```bash
docker build --progress=plain .
```

---

## 23.10 Remote Cache (CI Optimization)

BuildKit can:

* Push cache to registry
* Pull cache in next build

📌 Huge win for:

* CI/CD pipelines
* Large monorepos

---

## 23.11 When Should You Use BuildKit?

✅ Always (modern Docker)
✅ CI/CD pipelines
✅ Large images
✅ Multi-stage builds
✅ Secure builds

📌 Legacy builds are slowly being phased out.

---

## 🧠 Interview Power Answer

> “BuildKit is Docker’s modern build engine that enables faster parallel builds, advanced caching, secure secret handling, and efficient CI/CD optimizations.”

---