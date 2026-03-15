
---

## Chapter 6: **Running Real Containers**

This chapter, will show you the full power of `docker run` and how to effectively work with containers in **foreground** and **background** modes.

### 1️⃣ **The `docker run` Command — Flag by Flag**

The `docker run` command is the most common used to **create and run a container** from an image. It has several **flags** that control how the container behaves. Let's go through the most important flags, one by one.

---

### Key Flags in `docker run`:

#### **`-it`** — Interactive and TTY

* **`-i`** stands for **interactive**: Keeps the container's **stdin open**, so you can interact with it.
* **`-t`** stands for **tty**: Allocates a **pseudo-TTY**, so the container has a terminal.

**What it does**: Makes the container **interactive**, meaning you can type into the container’s terminal and see the output in real-time. This is especially useful when running shell-based containers like `bash` or `sh`.

#### Example:

```bash
docker run -it ubuntu bash
```

What happens:

1. Docker starts a new container based on the `ubuntu` image.
2. It opens a terminal inside the container (`bash`).
3. You can now run commands in the container (e.g., `ls`, `apt-get update`).

---

#### **`-d`** — Detach Mode (Run in the Background)

* **`-d`** stands for **detached**: It runs the container in the **background**, rather than keeping it attached to the terminal.

**What it does**: Runs the container as a **background process** (daemon). You won’t see the container’s output in your terminal, but the container will keep running.

#### Example:

```bash
docker run -d nginx
```

What happens:

1. Docker starts a new container based on the `nginx` image.
2. The container runs in the background, serving HTTP traffic.

To check if the container is running:

```bash
docker ps
```

To view logs of a running container:

```bash
docker logs <container_id>
```

---

#### **`--name`** — Assign a Name to the Container

* **`--name`** allows you to **assign a custom name** to your container, which makes it easier to reference later.

**What it does**: By default, Docker assigns a random name to each container (e.g., `laughing_morse`), but with `--name`, you can assign a name that’s more meaningful.

#### Example:

```bash
docker run -d --name mynginx nginx
```

What happens:

1. The container is given the name `mynginx`, making it easier to reference in future commands (e.g., `docker stop mynginx`).

---

#### **`--rm`** — Automatically Remove the Container After Exit

* **`--rm`**: Automatically removes the container **once it stops**. This is useful for containers that run short-lived processes.

**What it does**: After the container stops (e.g., the main process exits), Docker will **remove** the container, so you don’t have to clean it up manually.

#### Example:

```bash
docker run --rm ubuntu echo "Hello, Docker!"
```

What happens:

1. Docker runs the `ubuntu` container and executes `echo "Hello, Docker!"`.
2. Once the command completes, the container is automatically removed.

---

#### **`-p`** — Port Mapping (Exposing Ports)

* **`-p`** allows you to **map** a port on your host machine to a port inside the container. This is essential for exposing a service running inside the container to the outside world (e.g., a web server).

**What it does**: Maps a port on your host machine to a port inside the container.

#### Example:

```bash
docker run -d -p 8080:80 nginx
```

What happens:

1. The `nginx` container is started.
2. Port **8080** on the host is mapped to port **80** inside the container.
3. You can access the `nginx` web server on `http://localhost:8080` (on the host machine).

---

#### **`-v`** — Volume Mapping (Mounting Host Directories)

* **`-v`** allows you to **mount a directory or file** from your host machine into the container. This is useful for persistent data, configuration files, or sharing files between the host and container.

**What it does**: Creates a volume or bind mount, so data can be shared between the host and the container.

#### Example:

```bash
docker run -d -v /host/path:/container/path nginx
```

What happens:

1. A directory from the host (`/host/path`) is mounted to a directory in the container (`/container/path`).
2. Any changes to `/container/path` will persist on the host directory (`/host/path`), even after the container is stopped and removed.

---

### 2️⃣ **Foreground vs. Background: Understanding Container Modes**

#### **Foreground Mode**:

* When you run a container **without** the `-d` flag, the container runs in **foreground mode** (attached to the terminal).
* The terminal is "attached" to the container’s output, and you can interact with it.

Example:

```bash
docker run -it ubuntu bash
```

* You’ll see the shell prompt inside the container, and you can interact with it.

* The container will **continue running** in the foreground until you exit the terminal (e.g., by typing `exit`).

#### **Background Mode (Detached Mode)**:

* When you use the `-d` flag, the container runs in **background mode** (detached).
* The container runs in the background, and the terminal is not attached to the container.

Example:

```bash
docker run -d nginx
```

* The container starts in the background and **continues running** even after you exit the terminal.
* You can check its status, view logs, or attach to it later.

#### Switching Between Foreground and Background

* You can start a container in **foreground** mode (`docker run -it ...`), and then **detach** it by pressing **Ctrl + P, Ctrl + Q**.
* To attach back to a running container, you can use:

```bash
docker attach <container_id>
```

---

### 3️⃣ **Real-World Example: Running a Web Application (Nginx)**

Let's combine some of these flags to run a web server (e.g., Nginx) in the background:

```bash
docker run -d --name mynginx -p 8080:80 nginx
```

This command:

1. Runs the **Nginx** image in the background (`-d`).
2. Assigns the name `mynginx` to the container (`--name`).
3. Maps port 8080 on the host to port 80 inside the container (`-p`).
4. The Nginx container will serve HTTP content on `http://localhost:8080`.

You can now:

* **Check container status**:

  ```bash
  docker ps
  ```

* **View logs**:

  ```bash
  docker logs mynginx
  ```

* **Stop the container**:

  ```bash
  docker stop mynginx
  ```

* **Remove the container**:

  ```bash
  docker rm mynginx
  ```

---

### 5️⃣ **Summary of Flags**

| Flag     | Description                                                                      |
| -------- | -------------------------------------------------------------------------------- |
| `-it`    | Runs container in **interactive** mode with a **TTY** (useful for shell access). |
| `-d`     | Runs container in **detached** (background) mode.                                |
| `--name` | Assigns a **name** to the container.                                             |
| `--rm`   | Automatically **removes** the container after it exits.                          |
| `-p`     | **Maps** a port on the host to a port in the container.                          |
| `-v`     | **Mounts** a host directory or file into the container.                          |

---