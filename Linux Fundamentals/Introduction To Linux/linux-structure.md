# Linux Structure

Linux is an operating system widely used in personal computers, servers, mobile devices, embedded systems, and cybersecurity environments.

An **Operating System (OS)** manages the computer's hardware resources and provides communication between software applications and hardware components.

Linux exists in many different versions called **distributions (distros)**.

Examples include:

* Ubuntu
* Debian
* Fedora
* OpenSUSE
* Manjaro
* Gentoo
* Red Hat
* Linux Mint
* Parrot OS

In Hack The Box, the **Pwnbox** environment is based on **Parrot OS**, a Debian-based Linux distribution focused on security, privacy, and development.

---

# Linux History

Linux has its roots in Unix and the free software movement.

Important milestones:

* **1970** → Unix was released by Ken Thompson and Dennis Ritchie.
* **1977** → Berkeley Software Distribution (BSD) was released.
* **1983** → Richard Stallman started the GNU Project.
* The GNU project also contributed to the creation of the **GNU General Public License (GPL)**.
* **1991** → Linus Torvalds started developing the Linux kernel.

Linux began as a personal project but evolved into a major open-source operating system kernel.

Today, Linux is used in:

* Servers
* Mainframes
* Desktop computers
* Routers
* TVs
* Gaming consoles
* Embedded devices
* Smartphones through Android

Linux is **free and open-source**, meaning its source code can be modified and redistributed.

---

# Linux Philosophy

Linux follows a philosophy based on:

* Simplicity
* Modularity
* Openness
* Flexibility

The general idea is to create **small tools that perform one task well** and combine them to accomplish larger tasks.

---

## Everything Is a File

One of the main Linux principles is:

> **Everything is a file.**

Many system resources are represented through files, including:

* Hardware devices
* Processes
* Configuration information
* Some system interfaces

This allows users and programs to interact with many resources using common tools and commands.

---

## Small, Single-Purpose Programs

Linux provides many small tools designed to perform specific tasks.

Instead of one large program doing everything:

`Small Tool + Small Tool + Small Tool → Complex Task`

This makes Linux highly modular.

---

## Chaining Programs

Linux tools can be combined to perform more complex operations.

For example, the output of one command can be passed to another command for:

* Filtering
* Processing
* Searching
* Formatting

This is one of the reasons the Linux command line is so powerful.

---

## Avoid Captive User Interfaces

Linux is designed to provide strong control through the **shell or terminal**.

The command line allows users to directly interact with the operating system and combine tools efficiently.

---

## Configuration in Text Files

Linux commonly stores configuration information in plain text files.

Example:

`/etc/passwd`

This file contains information about users registered on the system.

Text-based configuration makes settings easier to:

* Read
* Modify
* Automate
* Back up
* Compare

---

# Linux Components

A Linux system consists of several important components.

| Component           | Description                                                |
| ------------------- | ---------------------------------------------------------- |
| **Bootloader**      | Starts the operating system during boot                    |
| **Kernel**          | Core component responsible for managing hardware resources |
| **Daemons**         | Background services                                        |
| **Shell**           | Interface between the user and the operating system        |
| **Graphics Server** | Provides graphical functionality                           |
| **Window Manager**  | Provides the graphical user interface                      |
| **Utilities**       | Programs that perform specific tasks                       |

---

# Bootloader

The **bootloader** is responsible for starting the operating system.

It loads the components required to begin the boot process.

Parrot Linux uses:

**GRUB — GRand Unified Bootloader**

Basic concept:

`Computer Starts → GRUB → Linux Kernel → Operating System`

---

# Kernel

The **kernel** is the core of the Linux operating system.

It manages hardware and system resources such as:

* CPU
* Memory
* Storage
* Input/Output devices
* Processes

The kernel provides an abstraction layer between hardware and software.

Conceptually:

`Applications → Kernel → Hardware`

---

# Daemons

**Daemons** are programs that run in the background.

They provide services required by the operating system.

Examples of tasks handled by daemons include:

* Scheduling
* Printing
* Networking
* Multimedia
* System services

Daemons commonly start during boot or when a user logs in.

---

# Shell

The **shell** is the interface between the user and the operating system.

It interprets commands entered by the user and allows interaction with the system.

Common Linux shells include:

* Bash
* Zsh
* Fish
* Ksh
* Tcsh/Csh

Example:

```bash
ls
```

The shell interprets the command and requests the appropriate action from the operating system.

---

# Graphics Server

Linux can use a graphical subsystem called the:

**X Server / X Window System**

It provides the foundation that allows graphical applications to run locally or remotely.

---

# Window Manager / Desktop Environment

The graphical interface used by users is provided through graphical environments.

Examples include:

* GNOME
* KDE
* MATE
* Unity
* Cinnamon

These environments usually provide applications such as:

* File managers
* Web browsers
* Settings tools
* System utilities

---

# Utilities

**Utilities** are applications that perform specific functions for users or other programs.

Linux provides many utilities for:

* File management
* Networking
* System administration
* Text processing
* Security
* Monitoring

---

# Linux Architecture

The Linux operating system can be represented as different layers:

```text
User
  ↓
System Utilities
  ↓
Shell
  ↓
Kernel
  ↓
Hardware
```

---

## Hardware

The **hardware layer** contains the physical components of the computer.

Examples:

* CPU
* RAM
* Hard drives
* Network interfaces
* Peripheral devices

---

## Kernel

The kernel controls and virtualizes hardware resources.

It manages resources such as:

* CPU time
* Memory
* Data access
* Devices

The kernel also gives processes their own virtual resources and helps prevent conflicts between processes.

---

## Shell

The shell provides a **Command-Line Interface (CLI)** where users can enter commands.

It provides access to functions exposed by the operating system.

---

## System Utilities

System utilities make operating system functionality available to the user.

They include tools used for:

* Managing files
* Managing users
* Monitoring processes
* Configuring the system
* Working with networks

---

# File System Hierarchy

Linux uses a **tree-like directory structure**.

The filesystem starts at the:

`/`

directory, called the **root directory**.

Everything else exists below it.

```text
/
├── bin
├── boot
├── dev
├── etc
├── home
├── lib
├── media
├── mnt
├── opt
├── root
├── sbin
├── tmp
├── usr
└── var
```

The structure follows the **Filesystem Hierarchy Standard (FHS)**.

---

# Important Linux Directories

## `/`

The **root directory** is the top-level directory of the Linux filesystem.

All other filesystems and directories exist below `/`.

---

## `/bin`

Contains **essential command binaries**.

These are important commands required by users and the operating system.

---

## `/boot`

Contains files required to boot Linux.

Examples include:

* Bootloader files
* Kernel files
* Boot-related configuration

---

## `/dev`

Contains **device files** representing hardware devices.

Examples may include:

* Disks
* Terminals
* USB devices

This connects directly with the Linux philosophy:

**Everything is a file.**

---

## `/etc`

Contains **system configuration files**.

Many installed applications also store configuration files here.

Example:

`/etc/passwd`

---

## `/home`

Contains home directories for regular users.

Example:

```text
/home/alex
/home/user
/home/htb
```

Users normally store their personal files and settings inside their home directory.

---

## `/lib`

Contains shared libraries required by programs and by the system during boot.

---

## `/media`

Used to mount removable media.

Examples:

* USB drives
* External storage

---

## `/mnt`

Used as a temporary mount point for filesystems.

Administrators commonly use it when manually mounting storage devices or remote filesystems.

---

## `/opt`

Used for optional software and third-party applications.

Example:

```text
/opt/tool
```

Security tools installed manually may sometimes be stored here.

---

## `/root`

The home directory of the **root user**.

Important distinction:

`/` → Root of the filesystem

`/root` → Root user's home directory

---

## `/sbin`

Contains binaries mainly used for **system administration**.

---

## `/tmp`

Contains temporary files created by the operating system and applications.

Files in this directory may be removed automatically, especially during system boot.

Therefore, data stored here should not be considered permanent.

---

## `/usr`

Contains many user-space resources, including:

* Executables
* Libraries
* Manual pages
* Applications

---

## `/var`

Contains **variable data**, meaning files whose contents frequently change.

Examples include:

* Log files
* Email data
* Web application files
* Cron-related files

A particularly important directory for cybersecurity is commonly:

`/var/log`

because system and application logs are usually stored there.

---

# File System Quick Reference

| Directory | Purpose                                    |
| --------- | ------------------------------------------ |
| `/`       | Root of the filesystem                     |
| `/bin`    | Essential commands                         |
| `/boot`   | Boot-related files                         |
| `/dev`    | Device files                               |
| `/etc`    | Configuration files                        |
| `/home`   | Regular users' home directories            |
| `/lib`    | Shared libraries                           |
| `/media`  | Removable media                            |
| `/mnt`    | Temporary filesystem mounts                |
| `/opt`    | Optional / third-party software            |
| `/root`   | Root user's home                           |
| `/sbin`   | System administration binaries             |
| `/tmp`    | Temporary files                            |
| `/usr`    | Applications, binaries, libraries, manuals |
| `/var`    | Variable data such as logs                 |

---

# Quick Reference

**Linux**
→ Free and open-source operating system based on the Linux kernel.

**Distribution / Distro**
→ A Linux-based operating system packaged for a specific purpose.

**Parrot OS**
→ Debian-based security, privacy, and development distribution used by HTB Pwnbox.

**Kernel**
→ Manages hardware and system resources.

**Shell**
→ Command interpreter used to interact with Linux.

**Daemon**
→ Background service.

**GRUB**
→ Bootloader used to start Linux.

**FHS**
→ Filesystem Hierarchy Standard.

**/**
→ Filesystem root.

**/etc**
→ Configuration files.

**/home**
→ Regular user home directories.

**/root**
→ Root user's home directory.

**/var**
→ Variable data such as logs.

**/tmp**
→ Temporary data.

**/dev**
→ Hardware represented as device files.

---

## Key Takeaway

**Linux is built around simplicity, modularity, and openness. Its architecture separates hardware, the kernel, the shell, and system utilities, while its filesystem follows a standardized tree structure beginning at `/`. Understanding components such as the kernel, shell, daemons, and directories like `/etc`, `/home`, `/var`, and `/dev` is fundamental for working effectively with Linux in cybersecurity.**
