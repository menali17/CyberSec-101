# System Information

When working with Linux systems, it is important to know how to gather information about:

* The operating system
* Users and groups
* Hostname
* Processes
* Network configuration
* Devices
* Open files
* Current directories

This information is useful for normal administration tasks, but it is also essential during security assessments because it can help identify:

* Misconfigurations
* Excessive privileges
* Vulnerable software
* Potential privilege escalation paths

---

# Essential System Information Commands

| Command    | Description                                                       |
| ---------- | ----------------------------------------------------------------- |
| `whoami`   | Displays the current username                                     |
| `id`       | Displays user and group IDs                                       |
| `hostname` | Displays or sets the current hostname                             |
| `uname`    | Displays operating system and hardware information                |
| `pwd`      | Displays the current working directory                            |
| `ifconfig` | Displays or configures network interfaces                         |
| `ip`       | Displays or manipulates interfaces, routing, devices, and tunnels |
| `netstat`  | Displays network status                                           |
| `ss`       | Investigates network sockets                                      |
| `ps`       | Displays process information                                      |
| `who`      | Shows currently logged-in users                                   |
| `env`      | Displays environment variables                                    |
| `lsblk`    | Lists block devices                                               |
| `lsusb`    | Lists USB devices                                                 |
| `lsof`     | Lists open files                                                  |
| `lspci`    | Lists PCI devices                                                 |

---

# Logging In via SSH

**SSH — Secure Shell** is a protocol used to securely access and execute commands on remote systems.

SSH is commonly used by system administrators because it:

* Works without a GUI.
* Uses relatively few system resources.
* Provides secure remote access.
* Allows commands to be executed remotely.

## Syntax

```bash
menali@htb[/htb]$ ssh htb-student@[IP address]
```

Conceptually:

```text
Local Machine
     |
    SSH
     |
     v
Remote Linux System
```

After connecting, commands entered in the terminal are executed on the remote machine.

---

# `hostname`

The `hostname` command displays the name of the current computer.

```bash
menali@htb[/htb]$ hostname

nixfund
```

This can help identify which machine we are currently connected to.

---

# `whoami`

The `whoami` command displays the username of the current user.

```bash
cry0l1t3@htb[/htb]$ whoami

cry0l1t3
```

During a security assessment, this is one of the first commands that can be used after obtaining access to a system.

It helps answer:

> **Which user am I currently running as?**

The user's identity determines which files, commands, and resources may be accessible.

---

# `id`

The `id` command provides more information than `whoami`.

It displays:

* User ID (`UID`)
* Primary Group ID (`GID`)
* Group memberships

Example:

```bash
cry0l1t3@htb[/htb]$ id

uid=1000(cry0l1t3) gid=1000(cry0l1t3) groups=1000(cry0l1t3),1337(hackthebox),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),116(lpadmin),126(sambashare)
```

Important parts:

```text
uid=1000(cry0l1t3)
```

→ User ID and username.

```text
gid=1000(cry0l1t3)
```

→ Primary group ID.

```text
groups=...
```

→ Additional groups the user belongs to.

---

# Important Groups

Group membership can reveal important permissions.

## `adm`

Membership in:

```text
adm
```

may allow the user to read log files located in:

```text
/var/log
```

These logs may contain useful or sensitive information.

---

## `sudo`

Membership in:

```text
sudo
```

is especially important because it can allow the user to execute commands with elevated privileges.

Depending on the configuration, the user may be able to execute some or all commands as:

```text
root
```

For penetration testers, this can represent a possible **privilege escalation path**.

For administrators, excessive `sudo` permissions may indicate a configuration that should be reviewed.

---

## Non-Standard Groups

Groups that are not part of the normal Linux installation may also be interesting.

Custom groups may provide access to specific files, applications, or system resources.

---

# `uname`

The `uname` command displays information about the operating system and hardware.

Running it without options displays the kernel name.

The manual can be viewed with:

```bash
man uname
```

Relevant options include:

```bash
-s    # Kernel name
-n    # Network node hostname
-r    # Kernel release
-v    # Kernel version
-m    # Machine hardware name
-p    # Processor type
-i    # Hardware platform
-o    # Operating system
-a    # Display almost all available information
```

---

# `uname -a`

The `-a` option displays most available system information.

```bash
cry0l1t3@htb[/htb]$ uname -a

Linux box 4.15.0-99-generic #100-Ubuntu SMP Wed Apr 22 20:32:56 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
```

This output contains information such as:

```text
Linux
```

→ Kernel name.

```text
box
```

→ Hostname.

```text
4.15.0-99-generic
```

→ Kernel release.

```text
#100-Ubuntu SMP Wed Apr 22 20:32:56 UTC 2020
```

→ Kernel version.

```text
x86_64
```

→ Machine architecture.

```text
GNU/Linux
```

→ Operating system.

---

# `uname -r`

The `-r` option displays only the **kernel release**.

```bash
cry0l1t3@htb[/htb]$ uname -r

4.15.0-99-generic
```

This is particularly useful during security assessments because the kernel version can be researched for known vulnerabilities.

Conceptually:

```text
uname -r
   ↓
Kernel Version
   ↓
Research Known Vulnerabilities
   ↓
Potential Kernel Exploit
```

However, finding an exploit for a matching version does not automatically mean the system is vulnerable. Configuration, patches, distribution changes, and exploit requirements also matter.

---

# `pwd`

The `pwd` command displays the current working directory.

```bash
menali@htb[/htb]$ pwd

/htb
```

`pwd` stands for:

**Print Working Directory**

It is useful when navigating through the Linux filesystem.

---

# `who`

The `who` command displays users currently logged into the system.

```bash
menali@htb[/htb]$ who

htb-student pts/0 2026-09-01 10:20
```

It can help identify active sessions on the machine.

---

# `env`

The `env` command displays environment variables.

```bash
menali@htb[/htb]$ env

SHELL=/bin/bash
HOME=/home/htb-student
USER=htb-student
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

Environment variables can contain useful information about:

* Current user
* Home directory
* Shell
* Executable paths
* Application configuration

---

# Network Information

Several commands can be used to investigate network configuration.

---

## `ifconfig`

`ifconfig` can display or configure network interfaces.

```bash
menali@htb[/htb]$ ifconfig
```

It can provide information such as:

* IP addresses
* Network interfaces
* MAC addresses
* Interface status

---

## `ip`

The `ip` command is used to display or manipulate:

* Network interfaces
* IP addresses
* Routing
* Network devices
* Tunnels

Example:

```bash
menali@htb[/htb]$ ip addr
```

It is commonly used on modern Linux systems for network configuration and inspection.

---

## `netstat`

The `netstat` command displays network-related information.

```bash
menali@htb[/htb]$ netstat
```

It can be used to inspect network status and connections.

---

## `ss`

The `ss` command is used to investigate sockets.

```bash
menali@htb[/htb]$ ss
```

Sockets can reveal network connections and services communicating on the system.

---

# `ps`

The `ps` command displays process information.

```bash
menali@htb[/htb]$ ps
```

Processes are running instances of programs.

Inspecting processes can help identify:

* Running services
* Applications
* User activity
* Potentially interesting privileged processes

---

# `lsblk`

The `lsblk` command lists block devices.

```bash
menali@htb[/htb]$ lsblk
```

Block devices commonly include:

* Hard drives
* SSDs
* Disk partitions

---

# `lsusb`

The `lsusb` command lists USB devices connected to the system.

```bash
menali@htb[/htb]$ lsusb
```

---

# `lspci`

The `lspci` command lists devices connected through the PCI bus.

```bash
menali@htb[/htb]$ lspci
```

This can include hardware such as:

* Network adapters
* Graphics cards
* Audio devices
* Controllers

---

# `lsof`

The `lsof` command lists **open files**.

```bash
menali@htb[/htb]$ lsof
```

Because Linux follows the principle that many resources are represented as files, this command can provide information about:

* Files being accessed
* Processes
* Network sockets
* Devices

---

# Useful Situational Awareness Workflow

After obtaining access to a Linux machine, some basic commands can quickly provide useful information:

```bash
whoami
id
hostname
uname -a
pwd
ip addr
ps
```

These answer several important questions:

```text
whoami
→ Who am I?

id
→ What permissions/groups do I have?

hostname
→ Which machine am I on?

uname -a
→ What OS/kernel/architecture is this?

pwd
→ Where am I?

ip addr
→ What network interfaces exist?

ps
→ What is running?
```

---

# Security-Relevant Information

Some commands are particularly useful when assessing a Linux system.

| Command           | Security-Relevant Information |
| ----------------- | ----------------------------- |
| `whoami`          | Current account               |
| `id`              | Groups and privileges         |
| `hostname`        | Machine identity              |
| `uname -r`        | Kernel release                |
| `ip` / `ifconfig` | Network configuration         |
| `ss` / `netstat`  | Network sockets/connections   |
| `ps`              | Running processes             |
| `env`             | Environment information       |
| `lsof`            | Open files and resources      |

---

# Quick Reference

```bash
whoami
# Current username

id
# User ID, group ID, and groups

hostname
# Current hostname

uname -a
# Detailed system/kernel information

uname -r
# Kernel release

pwd
# Current working directory

ifconfig
# Display/configure network interfaces

ip addr
# Display IP addresses and interfaces

netstat
# Display network status

ss
# Investigate sockets

ps
# Display processes

who
# Display logged-in users

env
# Display environment variables

lsblk
# List block devices

lsusb
# List USB devices

lsof
# List open files

lspci
# List PCI devices
```

---

## Key Takeaway

**System information commands provide situational awareness about a Linux host. Commands such as `whoami`, `id`, `hostname`, `uname`, `ip`, `ss`, and `ps` help identify the current user, privileges, operating system, kernel, network configuration, and running processes. This information is fundamental both for Linux administration and for identifying vulnerabilities or privilege escalation opportunities during security assessments.**
