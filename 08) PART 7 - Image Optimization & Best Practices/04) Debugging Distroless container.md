
# Debugging Distroless contianers

Kubernetes Ephemeral Debug Containers troubleshoot distroless containers by launching a separate, temporary troubleshooting container inside the exact same Pod as your app.
Because containers inside the same Pod share namespaces, this debug container can directly inspect your distroless app's files, memory, and network processes without altering the production container.
------------------------------
## Step-by-Step Execution Workflow
When your application is misbehaving and you need to look inside, the process flows through three phases:
## 1. The Operational command
You execute a specific kubectl debug command from your local terminal. You target the active pod and specify a tool-rich troubleshooting image (like ubuntu or busybox) to act as your tool belt:

kubectl debug -it pod-name --image=busybox --target=app-container-name


* -it: Allocates an interactive Pseudo TTY (PTY) directly to your terminal.
* --target: Tells the Linux kernel to align the new container with the process namespace of your running distroless application.

## 2. The Linux Namespace Shared Link
Behind the scenes, the Kubernetes node handles the request without stopping or restarting your production application. It leverages native Linux features to bridge the two containers:

┌─────────────────────────────────────────────────────────────┐
│                       KUBERNETES POD                        │
│                                                             │
│ ┌──────────────────────────┐    ┌─────────────────────────┐ │
│ │ Production Container     │    │ Debug Container (New)   │ │
│ │ (Distroless: No Shell)   │    │ (BusyBox: Full Shell)   │ │
│ │                          │    │                         │ │
│ │ PID 1: /myapp ◄──────────┼────┼─► Runs: ps, curl, gdb   │ │
│ └──────────────────────────┘    └─────────────────────────┘ │
│              ▲                               ▲              │
│              └───────────────┬───────────────┘              │
│                              ▼                              │
│                 SHARED LINUX NAMESPACES                     │
│               (Network, PID, IPC, UTS)                      │
└─────────────────────────────────────────────────────────────┘


* Process ID (PID) Sharing: The debug container hitches a ride on the application's process namespace. When you type ps aux inside the debug shell, you can directly see the distroless /myapp process running alongside your tools.
* Network Sharing: Both containers share the identical network loopback interface. Testing local network connectivity via curl localhost:8080 from the debug shell targets your application natively.

## 3. The Troubleshooting Session
Your terminal screen shifts into the busybox shell environment. You now have complete access to tools like gdb, curl, nslookup, or tcpdump.
You can run diagnostics, inspect raw logs, or track memory configurations. Once you type exit, the ephemeral container is cleanly destroyed, leaving the distroless production container completely untouched and securely locked down.
------------------------------
## Alternative Method: The Local Docker Approach
If you are running your distroless container locally using raw Docker rather than Kubernetes, you can achieve the exact same troubleshooting capability. You use the --pid and --network flags to manually map a debug container into your target container's namespaces:

# 1. Run your distroless container in the background
docker run -d --name distroless-app gcr.io/distroless/static-debian12 /myapp
# 2. Attach a busybox shell to its exact network and process spaces
docker run -it --rm \
  --pid=container:distroless-app \
  --network=container:distroless-app \
  busybox /bin/sh

Inside this new busybox terminal, running ls /proc/1/root/ lets you peek directly into the virtual filesystem layout of your running distroless container.
Would you like to try executing the local Docker debug command sequence on your machine, or look at how to verify that PID namespace sharing is enabled in your Kubernetes configuration?

