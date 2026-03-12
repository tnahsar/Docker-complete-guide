## Namespaces isolate processes — but how is this achieved in real life?

This is the **most important question** 👏
Let’s make it concrete.

---

## Real-world process creation (without Docker)

Normally, when Linux creates a process:

```text
fork() → exec()
```

The process:

* sees host processes
* sees host network
* sees host filesystem

---

## What Docker does differently

Docker uses **Linux namespaces** to isolate container processes. When you create a Docker container, it effectively creates a **set of namespaces** that isolate the container's processes from the host and from other containers.

For example:
(e.g., a web server, database, etc.).

When Docker creates a container, it does:

```text
clone() + namespace flags
```

It literally says to the kernel:

> “Create a process, BUT:
>
> * give it a new PID namespace
> * give it a new network namespace
> * give it a new mount namespace
> * give it a new UTS namespace”

---

### Example: PID namespace

Inside container:

```bash
ps
```

You see:

```
PID 1 app
```

On host:

```bash
ps aux | grep app
```

You see:

```
PID 48273 app
```

Same process.
Two different realities.

---

## Network namespace (real-world)

Each container gets:

* its own network stack
* its own IP
* its own routing table

That’s why:

* `localhost` inside container ≠ host
* ports must be published (`-p 8080:80`)

---

## Mount namespace (filesystem isolation)

Container sees:

```
/
├── bin
├── lib
├── app
```

Host sees:

```
/
├── home
├── var
├── etc
```

They are **different mount views** of the same disk.

---

## Simple analogy (best one)

Imagine:

* One big building (host)
* Many rooms (containers)

Each room:

* has tinted windows (namespaces)
* has electricity limiters (cgroups)
* shares the same foundation (kernel)

Rooms don’t see each other
But the building owner (kernel) sees everything.

---

## Why Docker needs root (historically)

Creating namespaces & cgroups requires:

* kernel-level permissions

That’s why:

* Docker daemon runs as root
* rootless Docker is more complex (advanced topic)

---

## Important:

* In general, Docker encourages the **"one process per container"** philosophy

    * One **Process** Per **Container** = One **Process** Per **Container** Per **Namespace**

For example:
- One **Process** or one **Container** for Web Servers like **Nginx**
- One **Process** or one **Container** for backend application like **Python Flask**
- One **Process** or one **Container** for Database like **PostgreSQL or Mysql**
- One **Process** or one **Container** for Frontend like **React or Angular**
- One **Process** or one **Container** for Background or Scheduled jobs like **cron / crond**
- One **Process** or one **Container** for metrics collector like **Prometheus node_exporter**
- One **Process** or one **Container** for Jenkins agent like **CI/CD job executors**

---

## Why Run Multiple Processes in One Container?

* In general, Docker encourages the **"one process per container"** philosophy, but in some scenarios, running multiple processes in a single container makes sense, such as:

    * Running a web server (e.g., Nginx, Apache) along with a background worker or cron job.
    * Running a database along with a log aggregator or monitoring agent in a single container.
    * Managing containers for legacy applications that expect multiple processes to run together.

However, for better scalability and maintainability, it’s often better to use multiple containers (one per process) with Docker's Compose or Kubernetes, where each container has a specific role.

---