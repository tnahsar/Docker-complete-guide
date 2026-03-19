
---

# 📘 PART 5 — Networking (Explained Simply)

Networking is how **containers talk to each other** and to the host.
This part answers:

> “Why can’t my container reach another container?”
> “Why doesn’t localhost work?”
> “How do ports actually map?”

---

## Chapter 15: **Docker Networking Basics**

Docker has **three built-in network types** by default:

| Network    | Description                       | Use case                             |
| ---------- | --------------------------------- | ------------------------------------ |
| **bridge** | Default network for containers    | Most apps                            |
| **host**   | Container uses host network stack | High-performance, single host        |
| **none**   | No networking                     | Isolated container, security testing |

---

### 1️⃣ Bridge Network (Default)

A **bridge network** is Docker’s **default network driver** for containers. It allows containers on the **same host** to communicate with each other, while isolating them from the host network (unless explicitly exposed).
* When you run a container without specifying a network, Docker connects it to the **default bridge network**.
* Containers on the same bridge network can communicate via **container names** or **IP addresses**.
* It provides **network isolation** so containers don’t interfere with the host network.

* Every container gets its **own private IP**
* Connected to a **virtual bridge on host** (usually `docker0`)
* Host can reach container via **port mapping** (`-p`)

---

#### Quick Visual:

```
[Container A] --+
                |-- [docker0 bridge] -- Host
[Container B] --+
```

* Default bridge = `bridge`
* Automatic IP assignment via Docker’s internal DHCP

### Key Features

| Feature                | Description                                                           |
| ---------------------- | --------------------------------------------------------------------- |
| Type                   | Private internal network for containers on the same host              |
| Container-to-container | Communication allowed using container name or IP                      |
| Host access            | Containers can access the host via special IP `172.17.0.1` (default)  |
| External access        | Must expose ports explicitly using `-p hostPort:containerPort`        |
| Isolation              | Containers on different bridge networks cannot communicate by default |

---

### 2️⃣ Host Network

The **host network driver** makes a container share the host’s network stack.

* In this mode, the container does not get its **own IP address**.
* Instead, it uses the **host machine’s IP and network interfaces** directly.

```bash
docker run --network host myapp
```

✅ Low latency
❌ Cannot run multiple containers using same port

---

### **Example**

Run a container using host network:

```bash
docker run --network host nginx
```

* Nginx will listen on the host’s network interfaces directly.
* If Nginx is configured to listen on port 80, it is immediately available on the host’s port 80.
* **No `-p` mapping is needed**, unlike bridge mode.

---

### **Use Cases**

* Performance-sensitive applications where **network latency must be minimal**.
* Applications that need to access **host network services** directly.
* Useful in monitoring, logging, or other system-level containers.

---

### Key feature

| Feature                | Description                                                                |
| ---------------------- | -------------------------------------------------------------------------- |
| Container IP           | Shares the host’s IP (no separate container IP)                            |
| Port mapping           | No need for `-p hostPort:containerPort`—container uses host ports directly |
| Container-to-container | Containers can communicate via localhost if they share host network        |
| Isolation              | Less isolated, since container uses host network directly                  |
| Performance            | Slightly better network performance (no NAT involved)                      |


### 3️⃣ None Network

```bash
docker run --network none myapp
```

* Container has **no network interface**
* Cannot reach host or other containers
* Useful for testing, security, or isolated workloads

---

### 4️⃣ Check Networks

```bash
docker network ls
```

Built-in defaults:

```
NETWORK ID     NAME      DRIVER
xxxxxx         bridge    bridge
yyyyyy         host      host
zzzzzz         none      null
```

---

### 5️⃣ Inspect Network

```bash
docker network inspect bridge
```

Shows:

* Connected containers
* IPs
* Gateway
* Subnet

---

## Chapter Summary

* Docker networking = bridge, host, none