
# Example of Psuedo TTY working

To see exactly how a Pseudo TTY works, you can observe it in action on your own Linux or Mac machine right now.
Here is a step-by-step example using two separate terminal windows to demonstrate how data moves between the Master window and the Slave device file.
------------------------------
## Step 1: Find Your Active PTY Slave File
When you open a terminal window, your graphical terminal application automatically acts as the PTY Master. It spawns a shell (like bash or zsh) and binds it to a PTY Slave file inside /dev/pts/.
Open a fresh terminal window (let's call this Terminal A) and run the tty command:

tty

The Output will look like this:

/dev/pts/1

This output tells you that Terminal A is currently reading input and writing output through the specific kernel slave device file /dev/pts/1.
------------------------------
## Step 2: Interact with the Slave File from a Different Window
Now, open an entirely separate terminal window (let's call this Terminal B).
Because /dev/pts/1 is handled by the Linux kernel as a standard character device file, you can interact with it using basic file commands. From Terminal B, write a string of text directly into Terminal A's slave file:

echo "Hello from Terminal B" > /dev/pts/1

What happens:
Look back at Terminal A. The text "Hello from Terminal B" instantly prints out onto the screen.
The Linux kernel intercepted your echo command to that character device, realized it was the slave side of a PTY, and flashed the data directly onto the master window interface.
------------------------------
## Step 3: See how Docker uses this behind the scenes
When you run a container without allocating a PTY, and then with a PTY, you can see the stark difference in how Linux processes the interaction.
## Scenario 1: Running WITHOUT a PTY (-i only)
Run an interactive container but omit the -t flag:

docker run -i ubuntu /bin/bash


* The Result: The terminal looks like it froze. There is no command prompt (no root@container:/#).
* Why: The container is running, but because it has no PTY slave, bash does not realize it is interacting with a human. It treats your input as a raw, non-interactive script file. If you blindly type ls and press Enter, it will still output the file list, but the session is stripped of all layout formatting, color, and tab-completion.

## Scenario 2: Running WITH a PTY (-it)
Exit that container and run it properly with the TTY flag:

docker run -it ubuntu /bin/bash


* The Result: You instantly see a colorful, formatted prompt: root@a1b2c3d4e5f6:/#.
* Why: Docker went to the Linux kernel, created a new PTY pair, hooked the Master side up to your host machine's terminal stream, and attached the Slave side (/dev/pts/[X]) to the container's /bin/bash process. The shell instantly switches to fully interactive mode.

If you would like to go deeper, would you like to see how Control Characters like Ctrl+C or Ctrl+Z are transmitted through a PTY to kill or pause a process, or how to see a list of all active PTY connections currently running on your host system?