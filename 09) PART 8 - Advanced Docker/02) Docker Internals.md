
---

# 🧠 Chapter 24: Docker Internals

### (containerd, runc, OCI — Explained Simply)

---

## 24.1 Big Picture: What Really Happens When You Run a Container

When you run:

```bash
docker run nginx
```

You are **NOT** directly running a container.

Instead:

```
Docker CLI
   ↓
Docker Daemon (dockerd)
   ↓
containerd
   ↓
runc
   ↓
Linux Kernel
```

📌 Docker is a **manager**, not the runtime itself.

---

## 24.2 Docker CLI vs Docker Daemon (Quick Recap)

### Docker CLI

* Just a client
* Sends REST API calls
* Does **not** run containers

### Docker Daemon (`dockerd`)

* Main brain of Docker
* Builds images
* Manages networks, volumes
* Talks to container runtime

---

## 24.3 containerd (The Container Manager)

### What is containerd?

> A **high-level container runtime** that manages container lifecycle.

Originally part of Docker, now:

* Open source
* CNCF graduated project
* Used by Docker **and Kubernetes**

---

### containerd Responsibilities

containerd handles:

* Image pull & push
* Image storage
* Container lifecycle:

  * create
  * start
  * stop
  * delete

📌 containerd **does NOT create containers directly**.

It delegates that to **runc**.

---

## 24.4 runc (The Actual Container Creator)

### What is runc?

> A **low-level runtime** that actually creates containers using Linux features.

runc:

* Uses namespaces
* Uses cgroups
* Uses seccomp
* Talks directly to Linux kernel

---

### Important Truth

📌 **runc = where the container is born**

When runc runs:

* Creates namespaces (PID, NET, MNT, UTS)
* Applies cgroup limits
* Starts the process

---

## 24.5 OCI (Open Container Initiative)

### Why OCI Exists

Before OCI:

* Docker had its own format
* Vendor lock-in risk

OCI was created to:

* Standardize container format
* Standardize runtime behavior

---

### OCI Defines Two Standards

#### 1️⃣ OCI Image Spec

* How images are built
* Layers
* Metadata

👉 Docker images follow this

---

#### 2️⃣ OCI Runtime Spec

* How containers are created
* Process lifecycle
* Filesystem layout

👉 runc follows this

---

📌 **This is why Kubernetes can run Docker images without Docker.**

---

## 24.6 Docker vs Kubernetes (Internals Perspective)

| Component         | Docker     | Kubernetes |
| ----------------- | ---------- | ---------- |
| CLI               | docker     | kubectl    |
| Runtime manager   | dockerd    | kubelet    |
| Container runtime | containerd | containerd |
| Low-level runtime | runc       | runc       |

📌 Docker is **not required** to run containers in Kubernetes.

---

## 24.7 containerd Without Docker (Real World)

Kubernetes setup:

```text
kubelet → containerd → runc → kernel
```

Docker was removed from Kubernetes in v1.24.

📌 Important interview fact.

---

## 24.8 Namespaces & cgroups (Ultra Quick Recap)

### Namespaces → Isolation

* PID → process IDs
* NET → network
* MNT → filesystem
* UTS → hostname

### cgroups → Limits

* CPU
* Memory
* IO

---

## 24.9 Why This Matters for You

Understanding internals helps with:

* Debugging weird container issues
* Security discussions
* Kubernetes knowledge
* Senior DevOps interviews

---

## 🧠 Interview Power Answer (Memorize)

> “Docker uses containerd to manage container lifecycle and runc to create containers using OCI standards. OCI ensures container portability across runtimes, which is why Kubernetes can run Docker images without Docker.”

---

## 🔥 One-Line Summary

> Docker is a **tooling layer**, containerd is the **manager**, runc is the **executor**, and OCI is the **rulebook**.

---