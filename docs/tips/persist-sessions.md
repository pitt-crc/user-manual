---
tags:
  - CRC
  - tmux
  - nohup
  - Login Nodes
  - User Manual
---

# Persisting Sessions with tmux and nohup

Login nodes enforce a per-user resource limit of **1 CPU core and 8 GB of memory** (combined across all your processes).
These tools are intended for lightweight tasks such as monitoring jobs, editing files, or launching submissions — **not** for running compute-intensive work.
For any resource-heavy work, [submit a batch job](../slurm/batch-jobs.md) or [request an interactive compute node](../slurm/interactive-jobs.md).

When you need a lightweight process to survive a disconnection or prevent timeout due to keyboard inactivity, use **tmux** or **nohup**.

---

## tmux

`tmux` is a terminal multiplexer that keeps a session alive on the server even after you disconnect. You can return to the session from any new SSH connection.

### Starting a session

```commandline
tmux new -s mysession
```

This creates a new named session called `mysession`. Naming sessions makes them easier to find later.

### Detaching from a session

Press `Ctrl+b`, then `d` to detach. Your session continues running in the background.

### Listing active sessions

```commandline
tmux ls
```

### Reattaching to a session

```commandline
tmux attach -t mysession
```

If there is only one session, you can simply run `tmux attach`.

### Killing a session

From inside the session:

```commandline
exit
```

Or from outside:

```commandline
tmux kill-session -t mysession
```

### Basic key bindings

All tmux key bindings begin with the prefix `Ctrl+b`.

| Shortcut           | Action                        |
|--------------------|-------------------------------|
| `Ctrl+b` then `d`  | Detach from session           |
| `Ctrl+b` then `c`  | Create a new window           |
| `Ctrl+b` then `n`  | Next window                   |
| `Ctrl+b` then `p`  | Previous window               |
| `Ctrl+b` then `%`  | Split pane vertically         |
| `Ctrl+b` then `"`  | Split pane horizontally       |
| `Ctrl+b` then `[`  | Enter scroll mode (q to exit) |

!!! note
    tmux sessions live on the specific login node you connected to. If you reconnect and land on a different login node, use `ssh login0.crc.pitt.edu` or `ssh login1.crc.pitt.edu` to reach the node where your session is running, then reattach.

---

## nohup

`nohup` (no hangup) runs a command so that it is not terminated when your SSH session ends. It is simpler than tmux and best suited for single commands that you want to run unattended.

### Basic usage

```commandline
nohup my_command &
```

The `&` sends the process to the background. Output that would normally go to the terminal is written to `nohup.out` in your current directory by default.

To redirect output to a specific file:

```commandline
nohup my_command > my_output.log 2>&1 &
```

The shell prints the background process ID (PID) when you run this. Take note of it.

### Checking whether the process is still running

```commandline
ps -p <PID>
```

Or search by name:

```commandline
pgrep -a my_command
```

### Stopping the process

```commandline
kill <PID>
```

---

## Choosing between tmux and nohup

| Scenario                                          | Recommended tool |
|---------------------------------------------------|------------------|
| You need to return interactively to a running session | tmux         |
| You want to watch output as it scrolls            | tmux             |
| You are running a single fire-and-forget command  | nohup            |
| You want the simplest possible setup              | nohup            |

---

## Important reminders

- Login node limits of **1 CPU core and 8 GB of memory** apply to all your processes combined, including those inside tmux or running via nohup.
- Processes that exceed these limits or consume excessive I/O may be terminated without notice.
- For any compute- or memory-intensive work, use [interactive jobs](../slurm/interactive-jobs.md) or [batch jobs](../slurm/batch-jobs.md) on dedicated compute nodes.
