# Processes

## ps (Processes)

Processes are the programs currently running on your machine. The Linux kernel manages them, and each process is assigned a unique number called the process ID (PID).

    $ ps

    PID        TTY     STAT   TIME          CMD
    41230    pts/4    Ss        00:00:00     bash
    51224    pts/4    R+        00:00:00     ps

>This provides a quick snapshot of the    processes associated with your current terminal session.

The output is:

- **PID**: The unique process ID.
- **TTY**: The controlling terminal for this process.
- **STAT**: The current status for this process.
- **TIME**: The total CPU time the process has used.
- **CMD**: The command that started the process.

### Exploring ps with BSD-Style Options

The ps command is highly versatile, with many options that fall into different syntax styles (BSD, System V, GNU). The BSD style, which doesn't use a dash for options, is quite common. A popular combination is ps aux:

    $ ps aux

    Here's what these options mean:
    a: Displays all processes for all users.
    u: Provides a detailed, user-oriented format.
    x: Includes processes not attached to any terminal. These often include system daemons that start at boot and show a ? in the TTY column.

### Real-Time Monitoring with top

While `ps` gives you a snapshot, the `top` command provides a real-time, dynamic view of the processes on your system.

## Controlling Terminal

### What is a TTY

In modern Linux systems, a TTY refers to the terminal that provides the standard input and output for a process.

There are two main types of terminals you will encounter: **terminal devices** and **pseudo-terminal devices**.

### The Role of Controlling Terminal

Most processes are tied to their luncher terminal, so if the you closed the luncher terminal, then the processes also within this terminal will be closed.

### Process without a Controlling Terminal

Some processes, known as daemons, are designed to run in the background and manage system services. These processes often start when the system boots and stop only when it shuts down.

To prevent them from being accidentally terminated, daemons are not attached to a controlling terminal.

## Process Details

### What is a Linux Process

A process is a program in execution. More precisely, it is an instance of a running program to which the system has allocated resources like memory, CPU time, and I/O. For example, if you open three terminal windows, run the `cat` command in two of them without any arguments (it will wait for standard input, keeping the process active), and then use the third window to run `ps aux | grep cat`, you will see two distinct `cat` processes. Each is a separate instance of the same program, with its own unique process ID and resource allocation.

### The Kernel's Role in Process Management

The kernel is responsible for all process management. The kernel maintains detailed information for each process, including:

- The status of the process
- The resources the process is using and receives
- The process owner
- Signal handling (more on that later)
- And basically everything else

## Process Creation

### The Fork and Exec Model

The primary mechanism for process creation in Linux involves an existing process cloning itself using the `fork` system call. The `fork` call creates a nearly identical child process. This new child process receives its own unique Process ID (PID), while the original process becomes its parent, identified by a Parent Process ID (PPID).

After forking, the child process can either continue running the same program as its parent or, more commonly, use the `execve` system call to load and run a new program. The `execve` call effectively replaces the process's memory space with that of the new program, allowing a different task to begin. This two-step "fork-exec" model is a cornerstone of how you create a process in Linux.

### The Init Process

If every process is a child of another, there must be an original ancestor. This is the `init` process. When the system boots, the kernel creates `init` as the very first user-space process, assigning it a PID of 1. The `init` process is the ultimate parent of all other processes and runs with root privileges to manage the system. It cannot be terminated until the system shuts down and is responsible for spawning many of the services that keep the system running.

## Process Termination

Once a process is created, how does it end? The termination of a process is a critical part of the process lifecycle, ensuring system resources are managed effectively.

A process typically terminates by calling the `_exit` system call. This action signals the kernel that the process is finished and its resources, like memory and file descriptors, can be reclaimed. Upon exiting, the process provides a termination status to the kernel, which is an integer value. By convention, a status of 0 indicates successful execution, while a non-zero value signals an error.

However, calling `_exit` doesn't immediately erase the process. The parent process must acknowledge its child's termination by using the `wait` system call. This call allows the parent to retrieve the child's termination status. This two-step mechanism is essential for proper process cleanup. Another way to `linux kill child process` is by using signals.

### Orphan Processes

What happens if a parent process terminates before its child? The child process becomes an "orphan." Since its original parent can no longer call `wait`, the kernel intervenes. The orphan process is immediately adopted by a special system process, typically `init` (process ID 1), which is considered the ancestor of all processes. The `init` process then assumes the role of the parent, periodically calling `wait` to collect the termination status of any of its adopted children, allowing them to terminate cleanly.

### Zombie Processes

When a child process  terminates, but its parent has not yet called `wait`, it becomes a **_zombie_** process. The process of the parent calling wait to clean up a zombie is called **_reaping_**.

### Zombie vs Orphan Process

- An orphan process is an active, running process whose parent has died. It is adopted by `init` and continues to execute until it finishes.
- A zombie process is a dead process that has completed its execution but still has an entry in the process table. It is waiting for its parent process to read its exit status.

## Signals

In Linux, a signal is a software interrupt sent to a process to notify it that an important event has occurred.

### The Purpose of Signals

Signals serve as a primary method of inter-process communication (**IPC**). They have many uses:

- **User Interaction**: A user can type special terminal characters, like Ctrl-C (SIGINT) or Ctrl-Z (SIGTSTP), to interrupt or suspend foreground processes.
- **Kernel Notifications**: The kernel can send signals to a process to notify it of hardware or software issues, such as an illegal memory access (SIGSEGV).
- **Process Management**: System administrators and other processes use signals to manage the lifecycle of other processes, such as requesting termination or a configuration reload.

### The Signal Lifecycle

When an event generates a signal, it is first delivered to a target process. The signal remains in a "pending" state until the kernel runs the process. When the process is scheduled, the signal is delivered. However, processes have signal masks, which can be configured to block the delivery of specific signals.

When a signal is delivered, the process can take one of several actions:

- **Ignore the signal**: The process simply discards the signal and continues execution.
- **Catch the signal**: The process executes a custom function called a signal handler to respond to the event.
- **Perform the default action**: If not caught or ignored, the default action is taken. For many signals, this means terminating the process.
- **Block the signal**: If the signal is in the process's signal mask, it remains pending until it is unblocked.

### Common Linux Process Signals

Each signal is defined by an integer, but they are almost always referred to by their symbolic names (the **os sig code**), which start with `SIG`.

**The most common linux process signals**:

- **SIGHUP (1)**: Hangup. Often used to tell a daemon to reload its configuration.
- **SIGINT (2)**: Interrupt. Sent by Ctrl-C. It's a request to terminate the process.
- **SIGKILL (9)**: Kill. This is an immediate, forceful termination. The process cannot catch, ignore, or block this signal.
- **SIGSEGV (11)**: Segmentation Fault. Indicates the process made an invalid memory reference.
- **SIGTERM (15)**: Termination. This is the standard, polite way to ask a process to terminate. It is the default signal sent by the kill command. A process can catch this signal to perform cleanup before exiting. This is often referred to as signal 15 linux.
- **SIGSTOP**: Stop. Pauses the process. Like SIGKILL, it cannot be caught or ignored.

## kill (Terminate)

You can manage processes by sending them signals. The primary command for this is kill, which, despite its name, can send various signals, not just ones that terminate a process.

### Default Termination with kill sigterm

The `kill` command without any flag (signal value) is sent by by default with `TERM` signal value.

    $ kill 12445

    The `kill sigterm` signal (also known as `SIGTERM` or signal 15) requests that the process shut down cleanly.

### Understanding Other Common Signals

- **SIGHUP**: The `kill sighup` signal (Hangup, signal 1) is traditionally sent to a process when its controlling terminal is closed. It can be used to tell daemon processes to reload their configuration files.
- **SIGINT**: The Interrupt signal (signal 2) is sent when you press `Ctrl-C`. It requests the process to interrupt its current operation.
- **SIGSTOP**: This signal (signal 19) pauses a process without terminating it. The process can be resumed later with the `SIGCONT` signal.

### Check Process Existence with kill -0

A special use case is linux kill -0. This command doesn't actually send a signal but instead checks if a process with the specified PID exists and if you have permission to signal it.

    $ kill -0 12445

    If the command executes successfully (exit code 0), the process exists. If it fails, the process does not exist or you lack permissions.

## niceness

When running multiple applications, CPU doesn't process them simultaneously, in fact CPU is extremely fast switching between them.

### How the CPU Manages Processes

Each process is allocated a small amount of CPU time called a "time slice". After its time slice, a process is paused, and the CPU moves to the next one. By default, the Linux kernel schedules processes in a round-robin fashion, ensuring every process gets a fair share of CPU time until it completes. The kernel's scheduler is highly efficient at managing these rapid switches.

### What is Niceness in Linux

The niceness of a process is represented by a number ranging from -20 (highest priority) to 19 (lowest priority).

A high niceness value (e.g., 19) means the process is very "nice" and has a low priority, yielding CPU time to others.
A low or negative niceness value (e.g., -20) means the process is not "nice" and demands more CPU time, giving it a higher priority.

### Adjusting Process Priority

You can view the current niceness level of running processes using the `top` command. Look for the NI column, which displays the niceness value.

To control the niceness linux value, you can use the `nice` and `renice` commands.

Use the `nice` command to start a new process with a specific niceness level. For example, the following command starts `apt upgrade` with a niceness of 5.

    $ nice -n 5 apt upgrade

    specify the niceness level of the process

To change the priority of an already running process, use the `renice` command. The following command changes the niceness of a process with PID 3245 to 10.

    $ renice 10 -p 3245

    modify the niceness level of the process

## Process State

### Decoding Common Process State Codes

**R (Running or Runnable)**: A process in this state is either actively executing on a CPU core or is in the run queue, ready to be executed as soon as a CPU core becomes available.

**S (Interruptible Sleep)**: This is one of the most common _process states in Linux_. The process is waiting for an event to complete, such as user input from the terminal or a network packet to arrive. It is "interruptible," meaning it can be woken up by signals.

**D (Uninterruptible Sleep)**: This process is also sleeping, but it's in a state where it cannot be interrupted by a signal. This is typically used for short periods during I/O operations where interrupting the process could lead to a corrupted state. If a process remains in this state for a long time, it may indicate a problem with hardware or a driver.

**Z (Zombie)**: A zombie process has finished execution, but it still has an entry in the process table. It is waiting for its parent process to read its exit status. A few zombies are normal, but a large number may indicate a bug in the parent application.

**T (Stopped)**: A process enters this state when it has been suspended by a job control signal (like pressing `Ctrl+Z`) or because it is being traced by a debugger. It can be resumed with the `SIGCONT` signal.

## /proc filesystem

The information of the running processes is dynamically stored in a special virtual filesystem known as `/proc`.

### Exploring the /proc Directory

The `/proc` filesystem is not a real filesystem on your hard drive; it's created in memory by the kernel. It provides a window into the kernel's internal data structures and the state of the system.

### A Dashboard of System Data

Think of the `/proc` filesystem as the raw data source for many system monitoring tools. Utilities like `top`, `ps`, and `htop` read from `/proc` to present information in a user-friendly format.

## Job Control

Running a Command in the Background
To start a process directly in the background, simply append an ampersand (`&`) to your command. This immediately returns your shell prompt, allowing you to continue working while the command executes.

    $ sleep 1000 &
    $ sleep 1001 &
    $ sleep 1002 &

    # This allows you to run commands in the background

### Listing Background Jobs

You can view all the jobs running in the background by using the `jobs` command.

    $ jobs

    [1]    Running     sleep 1000 &
    [2]-   Running     sleep 1001 &
    [3]+   Running     sleep 1002 &

### Managing Active Processes

Use `Ctrl-Z` to stop the running process then use `bg` command to send them to the background.

### Bringing a Job to the Foreground

Use `fg` command with the job id to bring the job to the foreground.

    $ fg %1

    bring job with `id` 1 to the foreground

### Terminating Background Jobs

Use `kill` command with the job **id** prefixed with (`%`).

    $ kill %1

    # Terminate the job that its id is 1.
