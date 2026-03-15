
---

# 📘 PART 2: Core Docker Usage

## Chapter 4: **Installing Docker (Linux / Mac / Windows)**

Before we start creating and running containers, we need to get Docker installed on our system. This chapter will cover the installation process for different operating systems and clarify what Docker Desktop and Docker Engine are.

---

### 1. **What Actually Gets Installed?**

When you install Docker, you’re installing several components that make up the **Docker Engine** and **Docker Desktop** (for Mac and Windows).

Here’s the breakdown of **what gets installed**:

---

#### **Docker Engine**:

The Docker Engine is the core component of Docker that actually runs containers. It consists of:

1. **Docker Daemon (`dockerd`)**:
   This is the heart of Docker. It runs in the background and manages Docker containers, images, networks, and volumes. It listens for Docker API requests and processes them. It also interacts with the underlying host OS to create and manage containers.

2. **Docker CLI (`docker`)**:
   The Command Line Interface (CLI) is the tool you use to interact with the Docker daemon. You run commands like `docker run`, `docker build`, and `docker ps` in the terminal.

3. **Docker Images**:
   These are pre-built, read-only templates that contain everything needed to run an application, including the OS, application code, libraries, and environment variables.

4. **Docker Containers**:
   These are the running instances of Docker images. A container is a lightweight, executable package that encapsulates everything an application needs to run.

---

#### **Docker Desktop (for Mac and Windows)**:

Docker Desktop is the GUI and user-friendly version of Docker for Mac and Windows. It includes the Docker Engine as well as additional features that are useful for developers who may not be familiar with command-line tools.

1. **Docker Daemon**:
   The same Docker Daemon (`dockerd`) is included in Docker Desktop. It’s responsible for running containers, just like on Linux.

2. **Docker CLI**:
   Docker Desktop includes the same command-line interface (`docker`) that you use to interact with Docker.

3. **Docker GUI**:
   Docker Desktop provides a graphical user interface (GUI) where you can view your containers, images, and networks, and perform basic management tasks without using the command line.

4. **Virtual Machine**:
   Since macOS and Windows don't run Linux natively, Docker Desktop uses a lightweight virtual machine (VM) to run Docker Engine. This VM provides a Linux environment for the Docker Engine to execute containers.

---

### 2. **Installation on Linux**

On Linux, Docker Engine is installed natively, so no VM is required. The steps will vary based on the Linux distribution (Ubuntu, CentOS, Fedora, etc.). Below are the steps for **Ubuntu**.

#### **Install Docker on Ubuntu:**

1. **Update system**:

   ```bash
   sudo apt-get update
   ```

2. **Install required packages**:

   ```bash
   sudo apt-get install \
       apt-transport-https \
       ca-certificates \
       curl \
       software-properties-common
   ```

3. **Add Docker’s official GPG key**:

   ```bash
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
   ```

4. **Add Docker repository**:

   ```bash
   sudo add-apt-repository \
       "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
       $(lsb_release -cs) \
       stable"
   ```

5. **Update package database**:

   ```bash
   sudo apt-get update
   ```

6. **Install Docker Engine**:

   ```bash
   sudo apt-get install docker-ce
   ```

7. **Check Docker version**:

   ```bash
   docker --version
   ```

8. **Verify installation**:

   ```bash
   sudo systemctl status docker
   ```

You should now have Docker installed and running!

---

### 3. **Installation on macOS (using Docker Desktop)**

For macOS, the easiest way to install Docker is via **Docker Desktop**. This includes the Docker Engine and Docker CLI, and it runs a Linux-based VM under the hood.

1. **Download Docker Desktop**:
   Go to [Docker's official website](https://www.docker.com/products/docker-desktop) and download the installer for macOS.

2. **Install Docker Desktop**:
   Double-click the `.dmg` file and follow the on-screen instructions to install Docker Desktop.

3. **Launch Docker Desktop**:
   After installation, you can find Docker Desktop in your Applications folder. Launch it.

4. **Verify installation**:
   Open the terminal and run:

   ```bash
   docker --version
   ```

5. **Start Docker**:
   When Docker Desktop starts, it will start the Docker Daemon and run the necessary virtual machine in the background. You'll see the Docker whale icon in your menu bar.

---

### 4. **Installation on Windows (using Docker Desktop)**

Similar to macOS, Docker on Windows is installed using **Docker Desktop**, which includes the Docker Engine and CLI, and runs in a VM.

1. **Download Docker Desktop**:
   Go to [Docker's official website](https://www.docker.com/products/docker-desktop) and download the installer for Windows.

2. **Install Docker Desktop**:
   Run the installer and follow the on-screen instructions. It will install Docker and enable **Windows Subsystem for Linux 2 (WSL 2)**, which is required to run Docker on Windows.

3. **Enable WSL 2** (if not already enabled):
   Follow the instructions to install WSL 2 and enable it in Windows, as Docker Desktop relies on it for the virtualized environment.

4. **Launch Docker Desktop**:
   After installation, Docker will automatically launch. You can also find it in your Start Menu.

5. **Verify installation**:
   Open the Command Prompt or PowerShell and run:

   ```bash
   docker --version
   ```

6. **Start Docker**:
   Docker will start the Docker Daemon in a Linux virtual machine via WSL 2.

---

### 5. **Docker Desktop vs Docker Engine**

* **Docker Desktop** is a **GUI-based application** for macOS and Windows that provides a complete Docker environment. It includes:

  * Docker Daemon (`dockerd`)
  * Docker CLI (`docker`)
  * Docker GUI (for managing containers, images, etc.)
  * Virtual Machine (on Mac/Windows) running Linux to support Docker Engine

* **Docker Engine** is the **core component** of Docker that runs on Linux (natively) or inside a VM (on Mac/Windows). It includes:

  * Docker Daemon (`dockerd`)
  * Docker CLI (`docker`)
  * It's installed natively on Linux and doesn't require a VM.

---

## Chapter 4 Summary:

* **Docker Engine** is the core component that runs on Linux (without VM).
* **Docker Desktop** is for **Mac** and **Windows**, and it includes Docker Engine, Docker CLI, and a GUI for managing containers.
* Installation steps vary based on the operating system, but Docker Desktop is the easiest way to get started on Mac/Windows.
* On **Linux**, Docker Engine is installed natively without needing a VM.

---