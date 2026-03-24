
---

## **Chapter 17: Port Mapping (`-p`)**

Port mapping allows the host machine to access services running inside Docker containers, which have their own private IPs and ports.

---

### **1️⃣ Why Port Mapping Exists**

* Each container has a **private IP** and **internal port**.
* The host cannot access these directly without mapping.
* Port mapping connects a **host port** to a **container port**.

**Syntax:**

```bash
docker run -p <host_port>:<container_port> myapp
```

**Example:**

```bash
docker run -p 8080:80 nginx
```

* Host port **8080** → Container port **80**
* Access via `http://localhost:8080` on the host.

---

### **2️⃣ How Port Mapping Works (Internals)**

* Docker uses **iptables / NAT** for port forwarding.
* Incoming traffic to the host port is **redirected to the container IP and port**.
* This is **transparent to the container**.

**Visual Representation:**

```
[Host:8080] ---> [docker0 bridge] ---> [Container:80]
```

---

### **3️⃣ Bind to a Specific Host Interface**

You can restrict access to a specific host interface:

```bash
docker run -p 127.0.0.1:8080:80 nginx
```

* Only accessible from **localhost**.
* Useful for **security** or **development purposes**.

---

### **4️⃣ Map Multiple Ports**

Expose multiple container ports to the host:

```bash
docker run -p 8080:80 -p 8443:443 nginx
```

* Host port **8080** → Container **80**
* Host port **8443** → Container **443**

---

### **5️⃣ Check Port Mappings**

Use `docker ps` to see mapped ports:

```text
CONTAINER ID   IMAGE   COMMAND   PORTS                  NAMES
abc123         nginx   "nginx"   0.0.0.0:8080->80/tcp   web
```

* **PORTS column** shows host → container mappings.

---

### **6️⃣ Quick Rules of Thumb**

* **Port mapping (`-p`)**: exposes container ports to host.
* **Host network**: low latency, high performance, single host only.
* **None network**: full isolation, no network access.
* Docker handles NAT / iptables automatically

---