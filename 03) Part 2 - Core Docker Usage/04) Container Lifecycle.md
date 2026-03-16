
---

# 📘 PART 2 — CORE DOCKER USAGE

## Chapter 7: **Container Lifecycle**

A **container lifecycle** describes the **states a container can be in** and the **commands you can use to transition between these states**. Unlike virtual machines, containers are **ephemeral**, meaning they are lightweight and temporary by default.

---

### 1️⃣ **Container States**

A container can be in several states:

| State                | Description                                                    |
| -------------------- | -------------------------------------------------------------- |
| **Created**          | Container is created but **not started** yet.                  |
| **Running**          | Container is currently **running** its main process.           |
| **Paused**           | Container is temporarily **suspended** (processes are frozen). |
| **Stopped / Exited** | Container finished execution or was stopped manually.          |
| **Dead**             | Container is in an unrecoverable error state (rare).           |

---

### 2️⃣ **Basic Lifecycle Commands**

#### **1. `docker run`** — Create and start a container

```bash
docker run -d --name mynginx -p 8080:80 nginx
```

* **Creates** a container and starts it in one step.
* Container moves from **Created → Running** immediately.

---

#### **2. `docker start`** — Start an existing container

```bash
docker start mynginx
```

* Starts a **stopped container**.
* Moves container from **Created/Stopped → Running**.
* Does **not** create a new container; it uses the existing one.

---

#### **3. `docker stop`** — Gracefully stop a running container

```bash
docker stop mynginx
```

* Sends a **SIGTERM** signal to the main process inside the container.
* Docker waits a few seconds (default: 10s) for the process to terminate gracefully.
* Moves container from **Running → Exited**.

---

#### **4. `docker restart`** — Stop and start container

```bash
docker restart mynginx
```

* Equivalent to `docker stop` followed by `docker start`.
* Useful if you want to **restart a container after configuration changes**.

---

#### **5. `docker kill`** — Force stop container

```bash
docker kill mynginx
```

* Sends **SIGKILL** to immediately terminate the container’s main process.
* Moves container from **Running → Exited** instantly.
* Use when the container **doesn’t respond to stop**.

---

#### **6. `docker rm`** — Remove a container

```bash
docker rm mynginx
```

* Removes a container from Docker’s metadata.
* Can only remove containers that are **stopped**.
* Use `-f` to force removal of a running container:

```bash
docker rm -f mynginx
```

---

### 3️⃣ **Pausing and Unpausing Containers**

Sometimes you might want to **temporarily suspend a container** without stopping it completely. This is where **pause/unpause** come in.

#### **Pause a container**

```bash
docker pause mynginx
```

* Freezes all processes inside the container.
* Useful for saving system resources temporarily.
* Container stays in **Paused** state.

#### **Unpause a container**

```bash
docker unpause mynginx
```

* Resumes the container from the paused state.
* Moves container from **Paused → Running**.

---

### 4️⃣ **Container Exit Codes**

When a container’s main process exits, it returns an **exit code**. This code is useful for automation and debugging.

* **0** → Success (everything ran fine)
* **Non-zero** → Error occurred
* Example:

```bash
docker run --rm ubuntu bash -c "exit 5"
echo $?
```

* The `$?` on the host shows the exit code returned by the container’s process (in this case, `5`).

#### Common practice:

* CI/CD pipelines and orchestration systems (like Kubernetes) use **exit codes** to determine whether a container ran successfully.

---

### 5️⃣ **Visualizing Container Lifecycle**

```
docker run
   |
   v
Created -----> Running -----> Exited/Stopped
                 |  ^           ^
                 |  |           |
                 |  +--- pause --+
                 +--- kill / stop
```

* `docker start` moves **Stopped → Running**
* `docker restart` moves **Running → Stopped → Running**
* `docker rm` removes **Exited/Stopped** containers permanently
* `docker pause` freezes the container without stopping it

---

### 6️⃣ **Best Practices for Container Lifecycle Management**

1. **Short-lived containers**:
   Use `--rm` for containers that only run a task and then exit.

2. **Long-running containers**:
   Use `-d` to run services (like Nginx) in detached mode.

3. **Graceful shutdown**:
   Prefer `docker stop` over `docker kill` unless necessary.

4. **Pause for resource management**:
   Use `docker pause` when temporarily freeing CPU/memory without stopping the container.

5. **Always check exit codes**:
   Use exit codes for debugging automation scripts or CI/CD workflows.

---

### 7️⃣ **Example Workflow**

1. Run a container:

```bash
docker run -d --name myapp nginx
```

2. Pause it temporarily:

```bash
docker pause myapp
```

3. Resume it:

```bash
docker unpause myapp
```

4. Stop it gracefully:

```bash
docker stop myapp
```

5. Remove it:

```bash
docker rm myapp
```

---

### ✅ Summary

* Containers go through **Created → Running → Exited** states.
* Key lifecycle commands: `run`, `start`, `stop`, `restart`, `kill`, `pause`, `unpause`, `rm`.
* Containers return **exit codes** to indicate success or failure.
* Proper lifecycle management ensures **clean and efficient container operations**.

---