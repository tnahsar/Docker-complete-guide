
---

# 📘 PART 1 — FOUNDATIONS

## Chapter 2: **What Docker Really Is (No Marketing, Just Truth)**

---

## The Big Picture: Docker Engine, Images, and Containers

In the previous chapter, we learned why Docker was created:
It solves the problem of running apps consistently on any machine.

But **how** does Docker actually work?
What **components** make up Docker?
Let’s break it down.

---

## 1. **Docker Engine**: The Heart of Docker

The **Docker Engine** is the software that runs containers. Think of it like the **core engine** of Docker.

It does everything:

* Builds images
* Runs containers
* Manages container lifecycle (start/stop)
* Handles networking and volumes

### Docker Engine has two main parts:

#### 1.1. **Docker Daemon (`dockerd`)**

The **Docker Daemon** is like the **manager** of everything Docker. It runs in the background, listens to Docker API requests, and makes decisions about which containers to run.

* It’s always running when Docker is active.
* It communicates with Docker CLI (Command-Line Interface).
* It interacts with containers and images.

#### 1.2. **Docker CLI (`docker`)**

The **Docker CLI** is the tool you use to **talk to** the Docker Daemon.
It’s the interface where you enter commands like:

```bash
docker run
docker build
docker ps
```

When you run a command like `docker run`, it **sends a request** to the Docker Daemon to start a container.

---

## 2. **Images**: The Blueprint of Containers

An **image** is the **blueprint** or **template** used to create containers. It contains everything your application needs to run:

* The app itself (binary files, code)
* Libraries and dependencies
* Environment settings
* Configurations
* File system layout

### Key things about Docker images:

* **Read-Only**: Images are immutable (unchangeable). Once created, you cannot modify them directly.
* **Layered**: Images are made of **layers**. Each layer represents a change (like adding a file or installing a package).
* **Build from a Dockerfile**: Images are typically built using a file called a **Dockerfile**. This file defines the instructions to build the image.

#### What happens when you build an image?

1. **Docker reads the Dockerfile**.
2. **Each instruction** in the Dockerfile (like `RUN`, `COPY`, `ADD`, etc.) creates a new **layer**.
3. The final result is a **stack of layers** that forms the image.

You can think of it like a **stack of pancakes**:

* Each pancake (layer) is a step.
* The final pancake stack is your image.

---

## 3. **Containers**: Running Instances of Images

A **container** is like a **running instance** of an image.

When you run `docker run <image>`, Docker takes the image and starts a container from it.

### Key things about containers:

* **Writable Layer**: Containers have their own **writable layer** on top of the image. This is where any changes (like new files or modified files) happen.
* **Short-lived**: Containers are meant to be ephemeral (temporary). You can start and stop them at will.
* **Isolated**: Each container is isolated from the others, but they share the same kernel (the core of the operating system).
* **Fast**: Starting a container is very fast because it shares the underlying OS, unlike VMs which need to boot their own operating systems.

#### Example:

1. You have an image called `ubuntu:20.04`.
2. You run the command:

   ```bash
   docker run -it ubuntu:20.04
   ```
3. Docker creates a container from the `ubuntu:20.04` image and gives it a writable layer.
4. You interact with the container, install some packages, create files, etc.
5. When the container is stopped, all changes are saved in the **container’s writable layer**.

---

## 4. **How Containers and Images Work Together**

Here's the big picture of how it works:

* **Images** are the **read-only blueprints**.
* **Containers** are **instances** of those blueprints, with a **writable layer** on top.
* When you start a container, Docker makes a copy of the image and adds a writable layer on top of it.
* You can start many containers from the same image. They all share the same base (the image), but they each have their own writable layer for changes.

---

## 5. **Docker’s Filesystem: Layers and Caching**

Docker images use a special filesystem called the **Union Filesystem**. This is what allows Docker images to be made of **layers** and share content efficiently.

Here’s how it works:

* **Each layer is read-only**.
* **Containers have a writable layer** on top of the image layers.
* If two containers use the same image, they **share layers**. Only the writable layer is unique to each container.

### Example of a layered image:

```
Image (read-only)
├── Layer 1: Base OS
├── Layer 2: Install Dependencies
└── Layer 3: App Code
     ↓
Container (writable layer)
```

* **Layer 1**: Base OS
* **Layer 2**: Dependencies (e.g., Python, Node.js)
* **Layer 3**: Application Code (e.g., `app.py`)

When you run a container, Docker takes this stack and adds a **writable layer** on top where changes (new files, edits) are made.

---

## 6. **Why Layers Matter**

* **Efficiency**: If many containers use the same base image (e.g., Ubuntu), they **share layers**, reducing storage.
* **Caching**: Docker caches each layer, so if you don’t change something in your Dockerfile, Docker **doesn’t rebuild** that layer. This speeds up builds.

---

## Docker Networking, Volumes, and Other Features (Just Mentioned for Now)

* **Networking**: Containers can communicate with each other via custom networks, but by default, they are isolated.
* **Volumes**: Volumes are used to persist data outside of the container’s writable layer (e.g., databases).
* **Docker Compose**: A tool for managing multi-container applications (more on this later).

---

## Chapter 2 Summary:

* **Docker Engine** consists of the **Daemon** (background process) and the **CLI** (command line interface).
* **Images** are read-only blueprints that contain everything needed to run an app.
* **Containers** are the running instances of images, with a writable layer on top.
* Images are **layered** and **efficient** (reuse layers, speed up builds).
* Containers are **fast**, **isolated**, and **ephemeral**.

---