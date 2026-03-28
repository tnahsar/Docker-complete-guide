
---

# 🔐 Chapter 22: Docker Security Basics (Must-Know for Production)

> **Mindset:**
> Docker security is about **reducing blast radius**, **minimizing attack surface**, and **protecting secrets**.

---

## 22.1 Why Docker Security Matters

Containers are:

* Lightweight
* Fast
* Shared-kernel based

👉 If compromised, **damage can spread fast**.

📌 **Security principle:**

> Assume containers will be attacked — limit what happens next.

---

## 22.2 Running Containers as Non-Root (VERY IMPORTANT)

### ❌ Default Problem

Most base images run as `root`.

If attacker gets shell:

* They are root inside container
* Can exploit kernel vulnerabilities
* Can escape container (rare but possible)

---

### ✅ Best Practice: Use Non-Root User

```dockerfile
FROM node:18-alpine

RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

WORKDIR /app
COPY . .
CMD ["node", "server.js"]
```

📌 Even if hacked → **limited privileges**

---

### 🔥 Interview Answer

> “I always run containers as non-root to reduce the blast radius of a compromise.”

---

## 22.3 Minimal Base Images (Attack Surface Reduction)

### Why?

More packages = more vulnerabilities.

| Image      | Packages | Risk     |
| ---------- | -------- | -------- |
| Ubuntu     | Many     | High     |
| Alpine     | Few      | Medium   |
| Distroless | Minimal  | Very Low |

📌 Fewer binaries → fewer CVEs.

---

## 22.4 Secrets Handling (What NOT to Do)

### ❌ NEVER DO THIS

```dockerfile
ENV DB_PASSWORD=secret123
```

or

```dockerfile
COPY .env .
```

👉 Secrets become:

* Part of image layers
* Visible via `docker inspect`
* Leaked in registries

---

### ✅ Correct Ways to Handle Secrets

#### Option 1: Runtime environment variables

```bash
docker run -e DB_PASSWORD=secret app
```

#### Option 2: Docker secrets (Swarm)

#### Option 3: Kubernetes secrets

#### Option 4: External secret managers

* AWS Secrets Manager
* Vault
* SSM Parameter Store

📌 **Secrets must be injected at runtime, not build time.**

---

## 22.5 File Permissions Inside Container

### Best Practice

* Application files → read-only
* Writable dirs → only where needed

```dockerfile
RUN chmod -R 555 /app
```

---

## 22.6 Read-Only Filesystem (Advanced)

```bash
docker run --read-only myapp
```

Allows writing only to:

* `/tmp`
* mounted volumes

📌 Prevents attackers from modifying binaries.

---

## 22.7 Image Scanning (Conceptual Understanding)

Image scanning finds:

* Known vulnerabilities (CVEs)
* Outdated packages

Popular tools:

* Trivy
* Docker Scout
* Clair
* Snyk

📌 Usually integrated in CI pipeline.

---

## 22.8 Don’t Use `latest` Tag (Security + Stability)

### ❌ Problem

```bash
docker pull nginx:latest
```

* Version changes silently
* Breaks production
* Hard to rollback

---

### ✅ Best Practice

```bash
docker pull nginx:1.25.2
```

📌 **Immutable deployments**

---

## 22.9 Drop Linux Capabilities (Advanced but Powerful)

Containers run with extra privileges by default.

```bash
docker run --cap-drop=ALL --cap-add=NET_BIND_SERVICE myapp
```

📌 Principle of least privilege.

---

## 22.10 Docker Security Checklist (Interview Gold)

✅ Run as non-root
✅ Minimal base images
✅ No secrets in image
✅ Scan images
✅ Pin image versions
✅ Reduce privileges

---

## 🧠 Interview Power Answer (Memorize)

> “I secure Docker images by using minimal base images, running containers as non-root, injecting secrets at runtime, scanning images for vulnerabilities, and following least-privilege principles.”

---