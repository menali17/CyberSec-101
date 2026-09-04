# Introduction to Windows

A solid understanding of both **Windows and Linux** is important in penetration testing because many systems encountered during assessments, whether on-premise or in the cloud, are based on these operating systems.

We should understand:

* How Windows systems operate.
* How they can be attacked.
* How they can be defended.
* How Windows can be used as a platform for further penetration testing activities.

---

# The Windows Operating System

Microsoft introduced Windows in **1985**. The first version was a graphical operating system shell for **MS-DOS**.

Over time, Windows evolved into two major environments we frequently encounter:

```text
Windows
   │
   ├── Windows Desktop
   │      └── Workstations / personal computers
   │
   └── Windows Server
          └── Servers / enterprise environments
```

Windows Server was introduced with **Windows NT 3.1 Advanced Server** in 1993.

Later versions introduced important technologies such as:

```text
IIS
Active Directory
MMC
Hyper-V
Event Viewer
Windows Firewall
```

Active Directory was introduced with Windows 2000 and became especially important in enterprise Windows environments.

---

# Legacy Windows Systems

As new Windows versions are released, older versions eventually reach **End of Life (EOL)** and stop receiving normal security updates.

However, organizations may still operate older Windows systems because of:

* Legacy applications.
* Operational requirements.
* Budget limitations.
* Compatibility requirements.

This matters during security assessments because different Windows versions may contain different:

```text
Misconfigurations
       +
Vulnerabilities
       +
Security mechanisms
```

Therefore, identifying the Windows version is an important part of understanding a target system.

---

# Windows Versions

Some important Windows versions and their corresponding version numbers are:

| Operating System                       | Version |
| -------------------------------------- | ------: |
| Windows NT 4                           |   `4.0` |
| Windows 2000                           |   `5.0` |
| Windows XP                             |   `5.1` |
| Windows Server 2003 / 2003 R2          |   `5.2` |
| Windows Vista / Server 2008            |   `6.0` |
| Windows 7 / Server 2008 R2             |   `6.1` |
| Windows 8 / Server 2012                |   `6.2` |
| Windows 8.1 / Server 2012 R2           |   `6.3` |
| Windows 10 / Server 2016 / Server 2019 |  `10.0` |

The important idea is that the Windows **product name** and its internal **version number** are not necessarily the same.

---

# Identifying the Windows Version

PowerShell can be used to retrieve information about the operating system.

The material introduces:

```powershell
Get-WmiObject
```

`Get-WmiObject` can retrieve information from **WMI (Windows Management Instrumentation)** classes.

To retrieve the Windows version and build number:

```powershell
PS C:\htb> Get-WmiObject -Class win32_OperatingSystem | select Version,BuildNumber

Version    BuildNumber
-------    -----------
10.0.19041 19041
```

Here:

```text
Version     → 10.0.19041
BuildNumber → 19041
```

This identifies information about the specific Windows installation.

---

# Useful WMI Classes

The material also introduces other useful classes:

| WMI Class               | Information                  |
| ----------------------- | ---------------------------- |
| `Win32_OperatingSystem` | Operating system information |
| `Win32_Process`         | Running processes            |
| `Win32_Service`         | Services                     |
| `Win32_Bios`            | BIOS information             |

For example:

```powershell
Get-WmiObject -Class Win32_Process
```

can retrieve information about processes.

While:

```powershell
Get-WmiObject -Class Win32_Service
```

can retrieve information about services.

`Get-WmiObject` can also interact with remote computers through the `ComputerName` parameter.

For now, the main concept is:

```text
Get-WmiObject
       ↓
Query WMI
       ↓
Retrieve Windows system information
```

---

# Accessing Windows

There are two general ways we can interact with a Windows system:

```text
Access
  │
  ├── Local Access
  │
  └── Remote Access
```

---

# Local Access

**Local access** means interacting directly with the computer.

Typical input devices include:

```text
Keyboard
Mouse
Trackpad
```

Output usually comes through:

```text
Display / Monitor
```

For example, sitting in front of a Windows workstation and using its keyboard and monitor is local access.

---

# Remote Access

**Remote access** means accessing another computer **over a network**.

Conceptually:

```text
Our Computer
     │
     │ Network
     ▼
Remote Windows Computer
```

Remote administration is extremely common in:

* IT teams.
* Security teams.
* Software development.
* MSPs.
* MSSPs.

It allows administrators and technical professionals to manage systems without being physically present at the machine.

---

# Common Remote Access Technologies

The material introduces several technologies:

| Technology | Meaning                   |
| ---------- | ------------------------- |
| `VPN`      | Virtual Private Network   |
| `SSH`      | Secure Shell              |
| `FTP`      | File Transfer Protocol    |
| `VNC`      | Virtual Network Computing |
| `WinRM`    | Windows Remote Management |
| `RDP`      | Remote Desktop Protocol   |

In this module, the main focus is:

```text
RDP
```

---

# Remote Desktop Protocol — RDP

**RDP (Remote Desktop Protocol)** allows us to remotely interact with a Windows computer through a graphical desktop environment.

RDP uses a **client/server architecture**.

```text
RDP Client
    │
    │ Network
    ▼
RDP Server
Windows Target
```

The computer initiating the connection runs the **client**.

The target Windows computer accepting the connection acts as the **server**.

RDP listens by default on:

```text
TCP/3389
```

---

# IP Address vs Port

The material uses a useful analogy:

```text
Network → Street

IP Address → House

Port → Door / Window
```

The IP address identifies the computer on the network:

```text
10.10.10.50
```

The port identifies the application/service on that computer:

```text
3389 → RDP
```

Therefore:

```text
10.10.10.50:3389
       │       │
       │       └── RDP service
       │
       └── Target computer
```

A network packet reaches the correct computer through its IP address and is then directed to the appropriate application based on its destination port.

---

# Connecting from Windows

Windows includes an RDP client called:

```text
Remote Desktop Connection
```

Its executable is:

```bash
mstsc.exe
```

Conceptually:

```text
Windows Host
     │
     │ mstsc.exe
     │ RDP
     ▼
Windows Target
```

For the connection to work, RDP access must already be enabled on the target system. The HTB lab Windows machines used in this module are configured to permit RDP access when appropriate.

---

# Saved RDP Connections

Remote Desktop Connection allows connection profiles to be saved as:

```text
.rdp
```

files.

This is convenient for administrators who frequently access remote systems.

From a penetration-testing perspective, saved `.rdp` files can also be interesting artifacts to look for during an assessment.

---

# Connecting from Linux

We can also connect from a Linux attack host to a Windows machine using RDP.

The main tool introduced by HTB is:

```bash
xfreerdp
```

The architecture becomes:

```text
Linux Attack Host
      │
      │ xfreerdp
      │ RDP
      ▼
Windows Target
```

`xfreerdp` is used throughout HTB because it provides command-line access to many useful RDP features.

---

# Other RDP Clients

Other RDP clients mentioned in the material include:

```text
Remmina
rdesktop
```

However, HTB primarily uses:

```text
xfreerdp
```

when connecting from Linux.

---

# Core Mental Model

The most important concepts from this introduction are:

```text
WINDOWS
   │
   ├── Desktop
   │
   └── Server
          │
          └── Enterprise environments
```

Windows information can be queried using tools such as:

```text
PowerShell
    ↓
Get-WmiObject
    ↓
WMI Classes
    ↓
System Information
```

And Windows machines can be accessed:

```text
Locally
   OR
Remotely
```

For remote graphical access:

```text
Linux
  │
  │ xfreerdp
  │
  │ RDP — TCP/3389
  ▼
Windows
```

---

# Quick Reference

### Windows version and build

```powershell
Get-WmiObject -Class win32_OperatingSystem | select Version,BuildNumber
```

### Process information

```powershell
Get-WmiObject -Class Win32_Process
```

### Service information

```powershell
Get-WmiObject -Class Win32_Service
```

### BIOS information

```powershell
Get-WmiObject -Class Win32_Bios
```

### Windows RDP client

```bash
mstsc.exe
```

### Linux RDP client

```bash
xfreerdp
```

### Default RDP port

```text
TCP/3389
```

---

## Key Takeaway

**Windows is one of the primary operating systems we will encounter in penetration testing and enterprise environments. We should be able to identify Windows versions, retrieve system information, and understand local and remote access. RDP provides graphical remote access to Windows systems and normally uses TCP port `3389`; from Linux, HTB primarily uses `xfreerdp` to establish these connections.**
