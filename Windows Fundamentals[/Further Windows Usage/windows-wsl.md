# Windows Subsystem for Linux (WSL)

**Windows Subsystem for Linux (WSL)** is a Windows feature that allows Linux binaries and command-line tools to run directly on Windows.

It was originally designed for developers who needed access to Linux tools such as:

* Bash
* Ruby
* `sed`
* `awk`
* `grep`

without leaving their Windows workstation.

The second version, **WSL 2**, introduced a real Linux kernel using a subset of Hyper-V functionality.

---

# Enabling WSL

WSL can be enabled from an elevated PowerShell session with:

```powershell id="y8x1kt"
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

After enabling the feature, a Linux distribution can be installed either:

* From the Microsoft Store.
* Manually from the command line.

---

# Starting Bash

WSL installs a Bash environment that can be opened by typing:

```cmd id="u3m7ka"
bash
```

This launches a Linux Bash shell from Windows.

Once inside the shell, we can interact with the environment much like a normal Linux system.

---

# Linux Directory Structure

WSL provides the standard Linux-style filesystem structure.

For example:

```powershell id="k4j8vn"
PS C:\htb> ls /

bin dev home lib lib64 media opt root sbin srv tmp var
boot etc init lib32 libx32 mnt proc run snap sys usr
```

Directories such as:

```text id="p7q2mf"
/home
/etc
/usr
/var
/tmp
/mnt
```

behave as expected in a Linux environment.

---

# Accessing Windows Drives

Windows drives are accessible from WSL through the:

```bash id="v5f1rs"
/mnt
```

directory.

For example, the Windows `C:` drive is typically available as:

```bash id="a9d3wp"
/mnt/c
```

This allows us to move easily between the Linux environment and files stored on the Windows host.

For example:

```bash id="z1n8hy"
cd /mnt/c
```

would move into the Windows `C:` drive from WSL.

---

# Working Inside WSL

Once inside the Bash shell, we can use WSL like a Linux-based operating system.

We can:

* Navigate directories.
* Run Linux commands.
* Install packages.
* Install updates.
* Use Linux command-line tools.
* Work with files stored on the Windows host.

---

# Checking the Linux Environment

We can inspect information about the Linux system with:

```bash id="c6r2xe"
uname -a
```

Example output from the material:

```powershell id="m8t4qs"
PS C:\htb> uname -a

Linux WS01 4.4.0-18362-Microsoft #476-Microsoft Fri Nov 01 16:53:00 PST 2019 x86_64 GNU/Linux
```

This shows information about the Linux kernel and system architecture running through WSL.

---

# WSL 1 vs. WSL 2

The main distinction introduced in this section is:

| Version   | Main Characteristic                                                   |
| --------- | --------------------------------------------------------------------- |
| **WSL 1** | Original Windows compatibility layer for running Linux tools          |
| **WSL 2** | Uses a real Linux kernel with Hyper-V-based virtualization components |

The important point is that WSL 2 provides a more complete Linux environment because it runs a real Linux kernel.

---

# WSL in Cybersecurity

WSL is useful in cybersecurity because many tools and workflows are traditionally built for Linux.

With WSL, we can use Linux command-line tools while remaining on a Windows workstation.

This can be convenient for:

* Scripting
* Log analysis
* Enumeration
* Network utilities
* Security tooling
* General Linux command-line workflows

For this section, the main idea is simply that **WSL provides an integrated Linux environment inside Windows**.

---

# Quick Reference

### Enable WSL

```powershell id="f2k9bg"
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

### Start Bash

```cmd id="r7w3pm"
bash
```

### List the Linux Root Directory

```bash id="n4s8vu"
ls /
```

### Access the Windows `C:` Drive

```bash id="q6h1zc"
cd /mnt/c
```

### Check Linux System Information

```bash id="b3y7lt"
uname -a
```

---

## Key Takeaway

**Windows Subsystem for Linux (WSL) allows Linux binaries and command-line tools to run directly on Windows. It provides a Linux-style filesystem, Bash shell, and access to Windows drives through `/mnt`. WSL 2 improves this integration by using a real Linux kernel, making WSL a practical way to use Linux tools without leaving a Windows environment.**
