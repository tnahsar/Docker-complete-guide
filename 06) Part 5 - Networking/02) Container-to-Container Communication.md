
## Chapter 16: **Container-to-Container Communication**

### 1️⃣ Why `localhost` doesn’t work

* `localhost` refers to the **container’s own network stack**, not the host or other containers.
* If a container tries to access another container using `localhost`, it will **only reach itself**, not the other container.

**Example:**

```bash
docker run -d --name db postgres
docker run -it --name app busybox
```

Inside `app`:

```bash
ping localhost  # will ping itself, not db
ping db         # works if on same custom network
```

* To communicate with other containers, use **container name** or **IP address**, not `localhost`.

---

### 2️⃣ Docker DNS (Magic)

* Docker has an **internal DNS server** that allows containers to resolve **other container names** on the same user-defined network.
* If containers are on the same custom bridge network, they can communicate using **container names** as hostnames.

**Example:**

```bash
docker network create my-network

docker run -d --name app1 --network my-network nginx
docker run -d --name app2 --network my-network busybox sleep 3600
```

Inside `app2`, you can ping `app1`:

```bash
docker exec app2 ping app1
```

* Docker automatically resolves `app1` to its container IP.
* This works **only on custom networks**, not the default bridge network (default bridge only allows communication via IP, not name).

> Note: Default `bridge` network does **not provide automatic DNS**, use user-defined bridge networks for container-to-container name resolution.

---

### 3️⃣ Custom Networks (Recommended)

Docker allows creating **user-defined networks**, which improve container-to-container communication:

```bash
docker network create my-network
```

**Benefits of custom networks:**

1. **Service discovery**: Containers can use names to communicate.
2. **Isolation**: Only containers on the same network can see each other.
3. **Control over IPs and subnets**: You can assign static IPs if needed.
4. **Multiple containers**: Useful in multi-container applications (like Docker Compose setups).

**Example:**

```bash
docker run -d --name web --network my-network nginx
docker run -d --name api --network my-network myapp
```

* `api` can access `web` using hostname `web`.
* Isolated from containers not on `my-network`.
---

### 4️⃣ Connecting a Container to a Network

```bash
docker network connect mynetwork mycontainer
```

Disconnect:

```bash
docker network disconnect mynetwork mycontainer
```

---


## Chapter Summary

* **Default bridge network**: isolated, container names not resolvable.
* **User-defined bridge network**: allows DNS-based container name resolution.
* Containers need **DNS or IP**, not `localhost` for inter-container communication
* Custom networks = recommended for production

---
