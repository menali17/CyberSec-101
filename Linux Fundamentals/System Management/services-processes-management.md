# Service and Process Management

Linux runs many programs in the background to keep the operating system functioning and provide additional functionality.

These background programs are commonly called **services** or **daemons**.

A daemon normally runs without direct user interaction.

Examples include:

```text id="m9v2kp"
sshd
systemd
```

Daemons are often recognizable by the:

```text id="q4n7wc"
d
```

at the end of their names.

---

# Types of Services

The material separates services into two main categories.

## System Services

System services are internal services required by the operating system.

They may:

```text id="t6p1xr"
Initialize hardware
Start system components
Provide essential OS functionality
```

They are commonly started during the boot process.

---

## User-Installed Services

These are services installed to provide additional functionality.

Examples may include:

```text id="c8w3nf"
SSH servers
Web servers
Database servers
Other background applications
```

They are not necessarily required for Linux itself to operate.

---

# Service and Process Management Goals

When managing services and processes, we generally want to be able to:

```text id="y5r8qm"
Start / restart
Stop
Check status
Enable / disable at boot
Find the service or process
```

---

# Processes

A **process** is an instance of a running program.

Every Linux process receives a:

```text id="g2k9vd"
PID
```

which stands for:

```text id="b7m4xp"
Process ID
```

Linux exposes information about processes through:

```text id="n1q6ws"
/proc/
```

Processes may also have a:

```text id="h8c3rf"
PPID
```

which stands for:

```text id="p4v7mk"
Parent Process ID
```

This indicates which process created another process.

Conceptually:

```text id="z6w2qn"
Parent Process
PID 100
    │
    ├── Child Process
    │   PID 101
    │   PPID 100
    │
    └── Child Process
        PID 102
        PPID 100
```

---

# systemd

Most modern Linux distributions use:

```text id="f9m5kc"
systemd
```

as their initialization system.

It is responsible for managing many system services and processes.

Services managed by `systemd` can be controlled using:

```bash id="r3q8vn"
systemctl
```

---

# Starting a Service

To start a service:

```bash id="x7p2wm"
systemctl start <service>
```

The HTB example starts SSH:

```bash id="k4n9cf"
menali@htb[/htb]$ systemctl start ssh
```

Conceptually:

```text id="d1v6qr"
SSH stopped
    │
systemctl start ssh
    │
    ▼
SSH running
```

---

# Checking Service Status

We can check whether a service is running correctly using:

```bash id="m8w3pk"
systemctl status <service>
```

Example:

```bash id="q5c1xn"
menali@htb[/htb]$ systemctl status ssh

● ssh.service - OpenBSD Secure Shell server
   Loaded: loaded (/lib/systemd/system/ssh.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2020-05-14 15:08:23 CEST; 24h ago
   Main PID: 846 (sshd)
   Tasks: 1 (limit: 4681)
   CGroup: /system.slice/ssh.service
           └─846 /usr/sbin/sshd -D
```

The particularly important line is:

```text id="t2r7mv"
Active: active (running)
```

which tells us that the service is currently running.

We can also see:

```text id="v6k4pw"
Main PID: 846 (sshd)
```

which identifies the main process associated with the service.

---

# Starting vs Enabling a Service

This distinction is very important.

```bash id="f3n8qx"
systemctl start ssh
```

means:

> Start SSH **now**.

However, this does not necessarily mean SSH will automatically start after reboot.

To configure that behavior, we use:

```bash id="w9c2mr"
systemctl enable ssh
```

Example:

```bash id="p1v7kd"
menali@htb[/htb]$ systemctl enable ssh

Synchronizing state of ssh.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable ssh
```

So:

```text id="n5q8wf"
start
│
└── Run now


enable
│
└── Start automatically during boot
```

A service can therefore theoretically be:

```text id="c7m3xr"
Running but not enabled

Stopped but enabled
```

because **current state and boot configuration are different concepts**.

---

# Finding a Process with `ps`

We can inspect running processes using:

```bash id="z4p9vn"
ps
```

The HTB example searches for SSH:

```bash id="r6w1kq"
menali@htb[/htb]$ ps -aux | grep ssh

root       846  0.0  0.1  72300  5660 ?        Ss   Mai14   0:00 /usr/sbin/sshd -D
```

Here:

```text id="h2c8mf"
ps -aux
```

lists processes.

Then:

```text id="b9v5qn"
|
```

passes the output to:

```text id="x1r7wk"
grep ssh
```

which keeps lines containing `ssh`.

Conceptually:

```text id="k6m3pv"
All processes
     │
     ▼
   ps -aux
     │
     │ pipe
     ▼
  grep ssh
     │
     ▼
SSH-related processes
```

---

# Listing Services

We can list services managed by `systemd` using:

```bash id="q8w4nc"
systemctl list-units --type=service
```

Example output:

```bash id="m2p7vr"
UNIT                     LOAD    ACTIVE  SUB      DESCRIPTION
accounts-daemon.service  loaded  active  running  Accounts Service
acpid.service            loaded  active  running  ACPI event daemon
apache2.service          loaded  active  running  The Apache HTTP Server
apparmor.service         loaded  active  exited   AppArmor initialization
```

This can help us discover which services exist and their current states.

---

# Service Logs with `journalctl`

Sometimes a service fails to start or behaves unexpectedly.

To investigate what happened, we can inspect logs using:

```bash id="v5k1xp"
journalctl
```

The HTB example is:

```bash id="n9q3wr"
menali@htb[/htb]$ journalctl -u ssh.service --no-pager
```

Example output:

```bash id="c4m8pf"
systemd[1]: Starting OpenBSD Secure Shell server...
sshd[2722]: Server listening on 0.0.0.0 port 22.
systemd[1]: Started OpenBSD Secure Shell server.
sshd[3939]: Connection closed by 10.22.2.1 port 36444 [preauth]
sshd[3942]: Accepted password for master from 10.22.2.1 port 36452 ssh2
```

Breaking down:

```text id="f7w2km"
journalctl
│
└── View systemd journal/logs

-u ssh.service
│
└── Show logs for the SSH service

--no-pager
│
└── Display output directly instead of opening a pager
```

This is especially useful when:

```text id="d3n6vq"
Service does not start
Service crashes
Authentication fails
Unexpected behavior occurs
```

---

# Process States

The material introduces four process states:

| State   | Meaning                                              |
| ------- | ---------------------------------------------------- |
| Running | Currently running                                    |
| Waiting | Waiting for an event or resource                     |
| Stopped | Execution has been stopped                           |
| Zombie  | Finished but still has an entry in the process table |

---

# Signals

Linux controls processes using **signals**.

A signal is essentially a message sent to a process telling it to perform some action.

Conceptually:

```text id="r8m4cw"
Shell
  │
  │ Signal
  ▼
Process
  │
  ▼
Responds to signal
```

We can display available signals using:

```bash id="p5v1kn"
kill -l
```

Example:

```bash id="x2q7mr"
menali@htb[/htb]$ kill -l

1) SIGHUP
2) SIGINT
3) SIGQUIT
...
9) SIGKILL
...
15) SIGTERM
...
19) SIGSTOP
20) SIGTSTP
```

---

# Important Signals

The most important signals introduced in the material are:

| Number | Signal    | Purpose                     |
| -----: | --------- | --------------------------- |
|    `1` | `SIGHUP`  | Hangup signal               |
|    `2` | `SIGINT`  | Interrupt process           |
|    `3` | `SIGQUIT` | Quit signal                 |
|    `9` | `SIGKILL` | Immediately kill process    |
|   `15` | `SIGTERM` | Request process termination |
|   `19` | `SIGSTOP` | Stop process                |
|   `20` | `SIGTSTP` | Terminal stop/suspend       |

Two especially important signals are:

```text id="w6n9pc"
SIGTERM = 15

SIGKILL = 9
```

---

# SIGTERM vs SIGKILL

`SIGTERM` requests that a process terminate.

```text id="b1r7qk"
SIGTERM
   │
   ▼
"Please terminate."
   │
   ▼
Process can terminate cleanly
```

`SIGKILL` immediately terminates the process.

```text id="m3v8xf"
SIGKILL
   │
   ▼
Kernel terminates process
immediately
```

Therefore, conceptually:

```text id="q9p4nw"
SIGTERM → Normal termination request

SIGKILL → Forced termination
```

---

# Killing a Process

We can send signals using:

```bash id="c6w2kr"
kill
```

The material gives the example:

```bash id="h5n1pv"
kill 9 <PID>
```

where:

```text id="t8q3vm"
9
│
└── SIGKILL

PID
│
└── Process we want to terminate
```

For example:

```bash id="f4m7xn"
kill 9 1234
```

sends SIGKILL to process:

```text id="z2p6wr"
PID 1234
```

---

# Other Process-Control Commands

The material also mentions:

```text id="k7q1mc"
kill
pkill
pgrep
killall
```

These are different tools for locating or interacting with processes.

At this stage, the main concept is:

> Processes can be identified and controlled using their PID, names, and signals.

---

# Ctrl+C

Pressing:

```text id="v9m3qw"
Ctrl + C
```

sends:

```text id="p2k8nr"
SIGINT
```

to the foreground process.

This is commonly used to interrupt a running command.

Conceptually:

```text id="f6w1xp"
Running command
      │
   Ctrl+C
      │
      ▼
    SIGINT
      │
      ▼
Process interrupted
```

---

# Ctrl+Z

Pressing:

```text id="c4q7mv"
Ctrl + Z
```

sends:

```text id="n8p2wk"
SIGTSTP
```

This **suspends** the foreground process.

For example:

```bash id="r5m9xc"
menali@htb[/htb]$ ping -c 10 www.hackthebox.eu

PING www.hackthebox.eu (104.20.55.68) 56(84) bytes of data.
[Ctrl + Z]
[1]+  Stopped                 ping -c 10 www.hackthebox.eu
```

The process has not necessarily been terminated.

It has been:

```text id="w3q6pn"
Suspended
```

---

# Jobs

The shell keeps track of jobs associated with the current session.

We can list them using:

```bash id="m1v8kr"
jobs
```

Example:

```bash id="q7p4xn"
menali@htb[/htb]$ jobs

[1]+  Stopped    ping -c 10 www.hackthebox.eu
[2]+  Stopped    vim tmpfile
```

Here:

```text id="k5n2wc"
[1]
[2]
```

are job IDs.

These are different from process IDs.

```text id="b9r3qm"
PID
→ System process identifier

Job ID
→ Shell job identifier
```

---

# Backgrounding a Suspended Process

After:

```text id="d6w1pk"
Ctrl + Z
```

the process is **suspended**, not actively running in the background.

To resume it in the background, we use:

```bash id="x4q8nv"
bg
```

Example:

```bash id="p7m2wr"
menali@htb[/htb]$ bg

[1]+ ping -c 10 www.hackthebox.eu &
```

The process now continues executing while we regain control of the shell.

---

# Starting Directly in the Background

Instead of:

```text id="f2k9mq"
Start process
↓
Ctrl+Z
↓
bg
```

we can start a command directly in the background by placing:

```text id="r8v3pn"
&
```

at the end.

Example:

```bash id="c5w1xq"
menali@htb[/htb]$ ping -c 10 www.hackthebox.eu &

[1] 10825
PING www.hackthebox.eu (172.67.1.1) 56(84) bytes of data.
```

So:

```bash id="n4q7km"
command &
```

means:

> Run this command in the background.

This allows us to continue using the terminal while the process runs.

---

# Foregrounding a Process

We can move a background job back into the foreground using:

```bash id="w6p2xr"
fg <ID>
```

First, list jobs:

```bash id="m9k4vn"
menali@htb[/htb]$ jobs

[1]+ Running    ping -c 10 www.hackthebox.eu &
```

Then:

```bash id="q3r8wc"
menali@htb[/htb]$ fg 1
```

The job returns to the foreground.

Conceptually:

```text id="p1v7km"
Background
    │
    │ fg
    ▼
Foreground
```

---

# Foreground vs Background

### Foreground

A foreground process occupies the current terminal interaction.

```text id="x8m4qr"
Terminal
   │
   ▼
Process
   │
   ▼
We wait/interact
```

### Background

A background process continues while we use the terminal for other commands.

```text id="k2n6wp"
Terminal
   │
   ├── Background process continues
   │
   └── We can enter other commands
```

---

# Job Control Workflow

A useful mental model is:

```text id="v5q9mr"
COMMAND
   │
   ▼
Foreground
   │
 Ctrl+Z
   ▼
Suspended
   │
   │ bg
   ▼
Background
   │
   │ fg
   ▼
Foreground
```

Or we can skip the first steps:

```text id="c7w3pn"
command &
    │
    ▼
Background immediately
```

---

# Executing Multiple Commands

Linux allows us to combine multiple commands.

The material introduces:

```text id="m4q8vk"
;
&&
|
```

These operators behave differently.

---

# Semicolon `;`

The semicolon separates commands.

Example:

```bash id="r1n7xp"
menali@htb[/htb]$ echo '1'; echo '2'; echo '3'

1
2
3
```

Each command runs one after another.

The important behavior is that the next command executes **even if the previous command fails**.

For example:

```bash id="p6w2km"
menali@htb[/htb]$ echo '1'; ls MISSING_FILE; echo '3'

1
ls: cannot access 'MISSING_FILE': No such file or directory
3
```

Even though:

```bash id="n9q4vc"
ls MISSING_FILE
```

failed, Linux still executed:

```bash id="f3m8wr"
echo '3'
```

Therefore:

```text id="k7p1xn"
command1 ; command2
```

means approximately:

> Run command1, then run command2 regardless of whether command1 succeeds.

---

# Double Ampersand `&&`

The:

```text id="q5v9mc"
&&
```

operator also runs commands sequentially.

However, the next command executes only if the previous command succeeds.

Example:

```bash id="w2n6pk"
menali@htb[/htb]$ echo '1' && ls MISSING_FILE && echo '3'

1
ls: cannot access 'MISSING_FILE': No such file or directory
```

The final:

```bash id="m8r3xq"
echo '3'
```

does not execute because:

```bash id="c4p7vn"
ls MISSING_FILE
```

failed.

Conceptually:

```text id="h1w5km"
command1
   │
   ├── Success ──→ command2
   │
   └── Failure ──→ STOP
```

---

# Pipe `|`

The pipe connects commands differently.

Instead of simply deciding which command runs next, it connects:

```text id="x7q2mr"
STDOUT of command 1
```

to:

```text id="v9n4pk"
STDIN of command 2
```

For example:

```bash id="b3w8xc"
ps -aux | grep ssh
```

means:

```text id="q6m1vr"
ps -aux
   │
   │ STDOUT
   ▼
grep ssh
   │
   ▼
Filtered result
```

This allows us to create processing pipelines.

---

# `;` vs `&&` vs `|`

These three operators should not be confused.

### Semicolon

```bash id="p4n9wk"
command1 ; command2
```

means:

```text id="m2q7vc"
Run command1
Then run command2
Even if command1 fails
```

### Double Ampersand

```bash id="r8w3xp"
command1 && command2
```

means:

```text id="k5v1mn"
Run command1
If successful → run command2
If unsuccessful → stop
```

### Pipe

```bash id="c9q4wr"
command1 | command2
```

means:

```text id="n6p2xv"
Run command1
     │
     │ STDOUT
     ▼
Use its output as input for command2
```

---

# Quick Reference

| Command                               | Purpose                     |
| ------------------------------------- | --------------------------- |
| `systemctl start`                     | Start a service             |
| `systemctl status`                    | Check service status        |
| `systemctl enable`                    | Enable service at boot      |
| `systemctl list-units --type=service` | List services               |
| `journalctl -u`                       | View logs for a service     |
| `ps`                                  | View processes              |
| `kill`                                | Send a signal to a process  |
| `kill -l`                             | List signals                |
| `jobs`                                | List shell jobs             |
| `bg`                                  | Resume job in background    |
| `fg`                                  | Move job to foreground      |
| `command &`                           | Start command in background |
| `Ctrl+C`                              | Send SIGINT                 |
| `Ctrl+Z`                              | Suspend with SIGTSTP        |

---

# Important Signals

```text id="t3q8nm"
2  → SIGINT
9  → SIGKILL
15 → SIGTERM
19 → SIGSTOP
20 → SIGTSTP
```

For now, the particularly important ones are:

```text id="w7m2pk"
SIGINT  → Ctrl+C

SIGKILL → Force kill

SIGTERM → Request termination

SIGTSTP → Ctrl+Z / suspend
```

---

# Commands to Remember First

Start a service:

```bash id="p9q4vn"
systemctl start ssh
```

Check it:

```bash id="m3w8xr"
systemctl status ssh
```

Enable it at boot:

```bash id="k6n1pc"
systemctl enable ssh
```

Find a process:

```bash id="v2q7wm"
ps -aux | grep ssh
```

Inspect service logs:

```bash id="r5m9xk"
journalctl -u ssh.service --no-pager
```

View shell jobs:

```bash id="c8q3pn"
jobs
```

Run in background:

```bash id="n1w6vr"
command &
```

Bring back to foreground:

```bash id="q4p8mk"
fg 1
```

---

# Essential Mental Models

### Service Management

```text id="h7v2qn"
systemctl start
→ Run now

systemctl status
→ Is it running?

systemctl enable
→ Run automatically after boot

journalctl
→ What happened?
```

### Process Control

```text id="x5m9wr"
Ctrl+C
→ Interrupt

Ctrl+Z
→ Suspend

bg
→ Continue in background

fg
→ Return to foreground

kill
→ Send signal
```

### Multiple Commands

```text id="p2q6vn"
;
→ Run next regardless

&&
→ Run next only if previous succeeds

|
→ Send output to next command
```

---

## Key Takeaway

**Linux distinguishes services from individual processes, and both can be controlled using specialized tools. `systemctl` manages systemd services, `journalctl` provides service logs, and commands such as `ps` and `kill` allow us to inspect and control processes. Shell job control with `Ctrl+Z`, `bg`, `fg`, and `&` allows us to move processes between foreground and background execution. Finally, `;`, `&&`, and `|` allow us to combine commands with different execution behaviors.**
