
---

# 📘 **Chapter 9: Dockerfile Basics**

## 1️⃣ **`FROM`** — The Starting Point

The **`FROM`** instruction is the first line in any **Dockerfile** (except in **multi-stage builds**, which we'll cover later). It sets the **base image** for your image.

* **Base Image** = Foundation of your image.
* Can be an **official** image (like `nginx`, `node`, `ubuntu`) or a **custom** image you created earlier.

**Example:**

```Dockerfile
FROM ubuntu:20.04
```

* **`ubuntu:20.04`** is the official Ubuntu image.
* This **image is downloaded** if you don’t have it locally.

### Why `FROM` is Important:

* It defines your **starting environment**. For example, if you want to run a Python app, you’d use `FROM python:3.9`.
* Everything you do in the Dockerfile will build on top of this base image.

### Example with a Node app:

```Dockerfile
FROM node:14
```

This gives you the **Node.js environment** (all the necessary binaries, dependencies, etc.) to run your Node application.

---

## 2️⃣ **`RUN`** — Install Dependencies or Modify the Image

The **`RUN`** instruction is used to execute commands in your image during the **build phase**. It’s most commonly used to install **packages**, **update the system**, or **configure** the image.

Every `RUN` creates a **new layer** in the image.

**Example:**

```Dockerfile
RUN apt-get update && apt-get install -y curl
```

What happens here:

* **`apt-get update`**: Updates the package list inside the container.
* **`apt-get install -y curl`**: Installs the `curl` package.

Multiple commands can be combined in a single `RUN` to minimize the number of layers (as each `RUN` creates a separate layer).

### Why `RUN` is Important:

* You use `RUN` to **install dependencies** or **configure** the image.
* **Best practice**: Combine multiple `RUN` instructions into **one**, reducing image size.

Example of combining:

```Dockerfile
RUN apt-get update && apt-get install -y curl vim git
```

---

## 3️⃣ **`COPY` vs `ADD`** — Copying Files

The **`COPY`** and **`ADD`** instructions are used to **copy files** from your host machine into the image.

* **`COPY`**: Simpler, just copies files.
* **`ADD`**: More advanced, supports remote URLs and auto-extracting compressed files (like `.tar.gz`).

### `COPY`:

```Dockerfile
COPY ./localfile.txt /app/file.txt
```

* **`./localfile.txt`**: Path on the host machine.
* **`/app/file.txt`**: Path inside the image.

### `ADD` (More Powerful, but Use Sparingly):

```Dockerfile
ADD https://example.com/file.tar.gz /app/
```

* **`ADD`** can download files from **URLs** and auto-unpack compressed files.
* **Use `COPY`** when you don’t need the extra features of `ADD`, since it's simpler and more predictable.

### Why `COPY` vs `ADD`:

* **`COPY`** is preferred in most cases for **simplicity** and **clarity**.
* **`ADD`** should be used only when you specifically need to extract compressed files or fetch remote files.

---

## 4️⃣ **`CMD` vs `ENTRYPOINT`** — Default Commands

Both **`CMD`** and **`ENTRYPOINT`** define the default **command** to run when a container is started, but they behave differently.

### `CMD` — Default Arguments (Can Be Overridden)

* The `CMD` instruction is used to define the default **command** that runs inside the container.
* If you provide a command when you run the container (`docker run <image> <command>`), it **overrides** the `CMD`.

**Example:**

```Dockerfile
CMD ["python3", "app.py"]
```

* By default, it runs `python3 app.py` when the container starts.
* However, if you run the container with a different command, it will override this:

  ```bash
  docker run myapp ls
  ```

---

### `ENTRYPOINT` — Sets the Executable (Cannot Be Easily Overridden)

* **`ENTRYPOINT`** defines the **executable** (the main program) to run when the container starts.
* The command provided when you run the container is passed as **arguments** to the `ENTRYPOINT` executable.

**Example:**

```Dockerfile
ENTRYPOINT ["python3"]
CMD ["app.py"]
```

* **`ENTRYPOINT`** makes sure that `python3` will **always** be the executable when the container starts.
* The **default command** (via `CMD`) can still be overridden when running the container:

  ```bash
  docker run myapp script.py
  ```

The result is:

* **`ENTRYPOINT`** defines the executable (in this case, `python3`).
* **`CMD`** provides the default argument (`app.py`), which can be overridden.

### Why `CMD` vs `ENTRYPOINT`:

* **`ENTRYPOINT`** is used for **the core executable**.
* **`CMD`** is used for **default arguments**, but can be easily overridden.
* **If you want a fixed command**, use `ENTRYPOINT`. If you want a command that can be overridden, use `CMD`.

---

## 5️⃣ **`ENV`** — Set Environment Variables

The **`ENV`** instruction is used to **set environment variables** inside the image. These variables can be used during the image build process or at runtime in the container.

**Example:**

```Dockerfile
ENV NODE_ENV=production
```

This sets an environment variable `NODE_ENV` to `production` for the container.

### Why `ENV` is Important:

* It allows you to configure the container at runtime.
* You can also use **environment variables** to make the container more **portable**.

---

## 6️⃣ **`WORKDIR`** — Set the Working Directory

The **`WORKDIR`** instruction sets the **working directory** for any subsequent **`RUN`**, **`CMD`**, **`ENTRYPOINT`**, **`COPY`**, and **`ADD`** instructions.

* This is the directory that will be used **within** the container, making it easy to navigate without needing to use absolute paths.

**Example:**

```Dockerfile
WORKDIR /app
COPY . .
RUN npm install
```

In this case:

* `WORKDIR /app` sets the current working directory to `/app`.
* All files will be copied to `/app`, and the `RUN` command will be executed within `/app`.

### Why `WORKDIR` is Important:

* It **simplifies paths** and makes your Dockerfile **cleaner**.
* Avoids long absolute paths like `/home/user/app`.

---

## 7️⃣ **`EXPOSE`** — Expose Ports

The **`EXPOSE`** instruction informs Docker that the container will listen on a specific port at runtime. This is mostly for **documentation purposes**, as the port still needs to be **mapped** to the host via `docker run -p`.

**Example:**

```Dockerfile
EXPOSE 80
```

This exposes port `80` inside the container, but you still need to map it to the host with:

```bash
docker run -p 8080:80 myapp
```

Now, traffic on host port `8080` is forwarded to the container’s port `80`.

### Why `EXPOSE` is Important:

* It tells Docker (and users) which ports the container uses.
* It’s an important step when building **networked applications**.

---

### Dockerfile Example

Here’s a complete example that combines all these instructions:

```Dockerfile
FROM node:14

WORKDIR /app
COPY package.json .
RUN npm install

COPY . .

ENV NODE_ENV=production

EXPOSE 3000

CMD ["node", "server.js"]
```

This Dockerfile:

1. Starts with the `node:14` image (for Node.js).
2. Sets the working directory to `/app`.
3. Copies `package.json` and installs dependencies with `npm install`.
4. Copies the rest of the application.
5. Sets `NODE_ENV=production`.
6. Exposes port `3000`.
7. Runs `node server.js` when the container starts.

---

## Chapter 9 Summary

| Instruction  | Description                                                           |
| ------------ | --------------------------------------------------------------------- |
| `FROM`       | Specifies the base image.                                             |
| `RUN`        | Executes commands to modify the image (e.g., install packages).       |
| `COPY`       | Copies files from the host machine into the image.                    |
| `ADD`        | Similar to `COPY` but supports remote URLs and auto-extracting files. |
| `CMD`        | Default command to run when the container starts (can be overridden). |
| `ENTRYPOINT` | Defines the executable (can't be easily overridden).                  |
