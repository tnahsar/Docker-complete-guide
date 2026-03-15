
---

# 📘 PART 2 — CORE DOCKER USAGE

## Chapter 5: **Your First Container**

We’ll use the classic first container:

```bash
docker run hello-world
```

Even though it looks simple, a lot happens under the hood.

---

### 1️⃣ Step-by-Step: What Happens When You Run `docker run hello-world`

#### **Step 1: Docker CLI talks to Docker Daemon**

* You type: `docker run hello-world`
* The **Docker CLI** sends a request to the **Docker Daemon (`dockerd`)** using the Docker API.
* Docker Daemon is responsible for:

  * Downloading images
  * Creating containers
  * Running processes
  * Managing networks and storage

Think of CLI → Daemon as **you placing an order → the kitchen prepares it**.

---

#### **Step 2: Check if image exists locally**

* Docker first checks your **local image cache** to see if `hello-world` exists.
* If it exists locally → skip download
* If it **does not exist** → Docker pulls it from **Docker Hub** (the default registry).

Command behind the scenes:

```bash
docker pull hello-world
```

* Docker pulls the image **layer by layer**, caching layers for future use.
* Each layer is **read-only**, stored on disk, and only downloaded if it doesn’t already exist locally.

---

#### **Step 3: Create a writable container layer**

* Docker creates a **new container** from the image.
* A **writable layer** is added on top of the image layers.
* This layer is where any changes during container execution would go (though `hello-world` doesn’t modify anything).

---

#### **Step 4: Set up namespaces & cgroups**

Docker isolates the container using Linux features:

* **Namespaces**:

  * PID namespace → container sees its own process as PID 1
  * Network namespace → container has its own virtual network stack
  * Mount namespace → container sees a virtual filesystem
* **cgroups**:

  * Controls resources (CPU, memory) assigned to the container

Even for a tiny container like `hello-world`, Docker sets up this isolation.

---

#### **Step 5: Start the main process**

* The container starts **the main process** specified in the image’s `CMD` or `ENTRYPOINT`.
* For `hello-world`, this process simply prints:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

* After the process finishes, the container **stops automatically** (because containers exit when the main process exits).

---

#### **Step 6: Clean up / container status**

* Container remains in your local Docker metadata in **stopped state**.
* You can see it with:

```bash
docker ps -a
```

* If you want, you can remove it:

```bash
docker rm <container_id>
```

---

### 2️⃣ Key Takeaways from `hello-world`

| Concept              | What You Learned                            |
| -------------------- | ------------------------------------------- |
| Docker CLI           | Sends commands to Docker Daemon             |
| Docker Daemon        | Manages images, containers, and resources   |
| Image layers         | Read-only layers pulled from Docker Hub     |
| Writable layer       | Container-specific changes go here          |
| Namespaces & cgroups | Isolate container processes and resources   |
| Main process         | Container stops automatically when it exits |
| Ephemeral containers | Containers are temporary by default         |

---

### 3️⃣ Visualizing `docker run hello-world`

```
Docker CLI  --> Docker Daemon
                  |
                  v
       Pulls hello-world image
                  |
                  v
         Adds writable layer
                  |
                  v
       Sets up namespaces & cgroups
                  |
                  v
         Starts main process (prints message)
                  |
                  v
       Process exits -> container stops
```

---

### 4️⃣ Why This Matters

Even this tiny example shows **everything about Docker**:

1. Docker works with **images** and **containers**.
2. Containers are **ephemeral** and process-based.
3. Docker uses Linux isolation features to **secure and separate containers**.
4. Every container has its **own filesystem, processes, and network**, even if it’s tiny like `hello-world`.

---

### 5️⃣ Next Steps

Now that you’ve run a container successfully, the next chapter (**Chapter 6: Running Real Containers**) will go deeper into:

* `docker run` **flags** like `-it`, `-d`, `--name`, `--rm`
* Running containers **in foreground vs background**
* Real examples of containers that stay alive and do work

---