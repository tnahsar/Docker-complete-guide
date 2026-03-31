
---

# 🎯 Chapter 29: Common Docker Interview Questions

*(With Real, Interview-Ready Answers)*

---

## 29.1 What Is the Difference Between an Image and a Container?

**Answer:**

> A Docker image is a **read-only template** used to create containers, while a container is a **running instance** of that image.

📌 Simple analogy:

* Image → Class
* Container → Object

---

## 29.2 What Happens When You Run `docker run`?

**Answer:**

1. Docker checks for the image locally
2. Pulls it from registry if missing
3. Creates a container
4. Sets up networking, storage, namespaces
5. Starts the process

📌 Shows internal understanding.

---

## 29.3 CMD vs ENTRYPOINT

| CMD               | ENTRYPOINT            |
| ----------------- | --------------------- |
| Default arguments | Main executable       |
| Overridable       | Not easily overridden |
| Optional          | Mandatory             |

**Best Practice:**

```dockerfile
ENTRYPOINT ["python"]
CMD ["app.py"]
```

---

## 29.4 COPY vs ADD

**Answer:**

> COPY is preferred for predictable file copying, while ADD has extra features like auto-extract and URL downloads, which are usually avoided.

📌 Say: “I default to COPY.”

---

## 29.5 How Is a Container Different from a VM?

**Answer:**

> Containers share the host kernel and isolate processes, while VMs run separate operating systems, making containers faster and lighter.

---

## 29.6 How Do Containers Achieve Isolation?

**Answer:**

> Using Linux namespaces for isolation and cgroups for resource control.

---

## 29.7 How Do You Persist Data in Docker?

**Answer:**

> By using volumes or bind mounts, since container filesystems are ephemeral.

---

## 29.8 Volumes vs Bind Mounts

| Volumes           | Bind Mounts  |
| ----------------- | ------------ |
| Docker-managed    | Host-managed |
| Safer             | Flexible     |
| Preferred in prod | Mostly dev   |

---

## 29.9 How Do Containers Communicate?

**Answer:**

> Through Docker networks using internal DNS resolution.

📌 Mention custom bridge networks.

---

## 29.10 What Is Docker Compose Used For?

**Answer:**

> Docker Compose is used to define and run multi-container applications using a YAML file, mainly for development and small setups.

---

## 29.11 How Do You Reduce Docker Image Size?

**Answer:**

* Multi-stage builds
* Minimal base images
* `.dockerignore`
* Cleaning package cache

---

## 29.12 How Do You Secure Docker Containers?

**Answer:**

* Run as non-root
* Avoid secrets in images
* Scan images
* Drop capabilities
* Use minimal images

---

## 29.13 What Is BuildKit?

**Answer:**

> BuildKit is Docker’s modern build engine that enables faster builds, better caching, and secure handling of secrets.

---

## 29.14 How Do You Monitor Docker Containers?

**Answer:**

* docker stats
* cAdvisor
* Prometheus
* Centralized logging

---

## 29.15 Why Should You Avoid `latest` Tag?

**Answer:**

> Because it causes unpredictable deployments and makes rollbacks difficult.

---

## 29.16 What Is OOMKilled?

**Answer:**

> It happens when a container exceeds its memory limit and the Linux OOM killer terminates it.

---

## 29.17 Can Kubernetes Run Without Docker?

**Answer:**

> Yes, Kubernetes uses containerd and runc and does not require Docker.

📌 This answer scores high.

---

## 🧠 Ultimate Interview Closing Statement

> “I use Docker to build secure, optimized images and rely on orchestration platforms like Kubernetes for running containers reliably at scale.”

---