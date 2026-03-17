
---

# 📘 PART 3 — Images & Dockerfile

## Chapter 11: **Building Images**

This chapter explains:

* What really happens when you run `docker build .`
* Cache hit vs cache miss (internals)
* Why `.dockerignore` is critical

We’ll keep it **simple, sequential, and practical**.

---

## 1️⃣ The `docker build .` Command

Basic command:

```bash
docker build .
```

What this means:

* **`docker build`** → start image build
* **`.`** → build context (current directory)

Docker does **not** read your whole filesystem.
It only sees what’s inside the **build context**.

---

## 2️⃣ What Is Build Context? (Very Important)

> **Build context = files Docker can access during build**

When you run:

```bash
docker build .
```

Docker:

1. Takes **all files in current directory**
2. Sends them to Docker daemon
3. Uses them for `COPY` and `ADD`

⚠️ If your directory is large → build is slow.

---

### Example

Directory:

```
project/
├── Dockerfile
├── app.js
├── node_modules/
├── .git/
```

If you run `docker build .`:

* Docker sends **everything**
* Even `node_modules`, `.git`, etc.

This is why `.dockerignore` exists (we’ll come back to it).

---

## 3️⃣ What Happens Internally (Step by Step)

Let’s say your Dockerfile is:

```Dockerfile
FROM ubuntu:20.04
RUN apt-get update
COPY app.txt /app.txt
CMD ["cat", "/app.txt"]
```

Now run:

```bash
docker build -t myimage .
```

### Internal flow:

---

### Step 1: Docker reads Dockerfile line by line

Docker **never executes the whole file at once**.

It processes **one instruction at a time**.

---

### Step 2: `FROM ubuntu:20.04`

* Docker checks local cache
* If image exists → reuse
* Else → pull from Docker Hub

✅ Creates **base layer**

---

### Step 3: `RUN apt-get update`

Docker:

1. Creates a **temporary container**
2. Runs `apt-get update`
3. Takes a **filesystem snapshot**
4. Saves it as a **new image layer**
5. Deletes the temporary container

This is how **every RUN works**.

---

### Step 4: `COPY app.txt /app.txt`

Docker:

1. Looks inside build context
2. Copies `app.txt`
3. Creates **another layer**

---

### Step 5: `CMD ["cat", "/app.txt"]`

* No filesystem change
* Metadata only
* Creates a **zero-byte layer**

---

### Step 6: Image is finalized

Final image =

```
Layer 1: ubuntu base
Layer 2: apt-get update
Layer 3: copy app.txt
Layer 4: CMD (0B)
```

---

## 4️⃣ Cache Hit vs Cache Miss (CRITICAL)

Docker’s speed depends on cache.

---

### 🔁 Cache Hit

A cache hit happens when:

* Instruction text is unchanged
* Files used by instruction are unchanged

Docker:

* Reuses old layer
* Skips execution

Example output:

```
Using cache
```

---

### ❌ Cache Miss

Cache miss happens when:

* Instruction changes
* Any file used by instruction changes

Docker:

* Rebuilds that layer
* **Invalidates all layers after it**

---

### Example (Very Important)

Dockerfile:

```Dockerfile
FROM node:14
COPY . .
RUN npm install
```

Change **any file** → `COPY . .` changes
➡️ cache miss
➡️ `RUN npm install` reruns every time ❌

---

### Optimized Version

```Dockerfile
FROM node:14
COPY package.json .
RUN npm install
COPY . .
```

Now:

* Code changes → only last `COPY`
* Dependencies unchanged → cache hit ✅

---

## 5️⃣ Why Cache Is Invalidated After a Miss

Docker layers are **linear**:

```
Layer A → Layer B → Layer C
```

If Layer B changes:

* Layer C depends on old Layer B
* Layer C must be rebuilt

➡️ Docker **cannot skip ahead**

---

## 6️⃣ `.dockerignore` (Extremely Important)

`.dockerignore` works like `.gitignore`.

It tells Docker:

> “Do NOT send these files to build context”

---

### Example `.dockerignore`

```
node_modules
.git
.env
*.log
```

Now:

* Smaller build context
* Faster builds
* Better cache usage
* More secure (no secrets)

---

### Without `.dockerignore` ❌

* Large uploads
* Slow builds
* Cache misses
* Accidental secret leaks

---

### With `.dockerignore` ✅

* Fast
* Clean
* Predictable
* Safe

---

## 7️⃣ Common Beginner Mistakes

❌ `COPY . .` at top
❌ No `.dockerignore`
❌ Installing deps after copying full source
❌ Rebuilding heavy layers repeatedly

---

## 8️⃣ Golden Rules for Building Images

Memorize these:

1. **Base image first**
2. **Install dependencies early**
3. **Copy rarely-changing files first**
4. **Copy frequently-changing files last**
5. **Always use `.dockerignore`**
6. **One logical change = one layer**

---

## 9️⃣ Mental Model (Final)

> Docker builds images by
> **executing instructions → snapshotting filesystem → caching layers**

If you understand this sentence, you understand Docker builds.

---

## Chapter 11 Summary

* `docker build .` sends build context
* Docker reads Dockerfile line by line
* Each instruction = one layer
* Cache hit = reuse layer
* Cache miss = rebuild + invalidate later layers
* `.dockerignore` is essential

---
