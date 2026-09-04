# Desktop Experience vs. Server Core

Windows Server can be installed using either **Desktop Experience** or **Server Core**.

The main difference is that **Desktop Experience includes the full graphical interface**, while **Server Core removes most GUI components and focuses on command-line and remote administration**.

---

# Windows Server Core

**Server Core** was introduced with Windows Server 2008 as a minimal installation option containing only essential server functionality.

Because it does not include the full desktop GUI, Server Core generally has:

* Lower management overhead
* Smaller disk usage
* Lower memory usage
* Smaller attack surface

Administration is primarily performed through:

```text
Command Prompt
PowerShell
Remote MMC tools
Remote Server Administration Tools (RSAT)
```

---

# Server Core Management

Even though Server Core does not provide the normal Windows desktop environment, administration can still be performed locally or remotely.

Common management methods include:

```cmd
cmd
```

```powershell
PowerShell
```

and remote tools such as:

```text
MMC
RSAT
```

This means Server Core does not eliminate management functionality; it changes how that management is performed.

---

# Graphical Applications Still Available

Server Core removes most GUI components, but some graphical programs are still supported.

Examples include:

* Registry Editor
* Notepad
* System Information
* Windows Installer
* Task Manager
* PowerShell

Some Sysinternals tools are also supported, including:

* Active Directory Explorer
* Process Explorer
* Process Monitor
* TCPView

So Server Core is not completely graphical-interface-free, but it lacks the normal Windows desktop experience.

---

# Installation Choice

Starting with Windows Server 2019, the choice between:

```text
Server Core
Desktop Experience
```

must be made during installation.

According to the material, the installation type cannot later be converted from Server Core to Desktop Experience or vice versa.

Therefore, the intended use of the server should be considered before installation.

---

# Sconfig

After Server Core is installed, its initial configuration can be performed using:

```cmd
sconfig
```

`Sconfig` provides a text-based interface for common administrative tasks.

It can be used for tasks such as:

* Configuring networking
* Checking for Windows updates
* Installing updates
* Managing accounts
* Configuring remote management
* Activating Windows
* Changing server settings

This gives administrators a menu-based configuration interface without requiring the full graphical desktop.

---

# Server Core Limitations

Some Windows Server applications cannot run on Server Core.

Examples from the material include:

* Microsoft Server Virtual Machine Manager 2019
* System Center Data Protection Manager 2019
* SharePoint Server 2019
* Project Server 2019

Because of these compatibility limitations, Server Core is not appropriate for every server workload.

---

# Desktop Experience

**Desktop Experience** provides the traditional Windows graphical environment.

It includes tools and interfaces that are missing from Server Core, such as:

* Windows Explorer
* Control Panel
* Server Manager
* Event Viewer
* MMC
* Services console
* Disk Management
* Web browsers

This can make administration easier, especially for administrators who rely heavily on graphical management tools.

However, the additional components also require more system resources and increase the overall software footprint.

---

# Server Core vs. Desktop Experience

| Application               | Server Core   | Desktop Experience |
| ------------------------- | ------------- | ------------------ |
| Command Prompt            | Available     | Available          |
| Windows PowerShell / .NET | Available     | Available          |
| Registry Editor           | Available     | Available          |
| `diskmgmt.msc`            | Not Available | Available          |
| Server Manager            | Not Available | Available          |
| `mmc.exe`                 | Not Available | Available          |
| Event Viewer              | Not Available | Available          |
| `services.msc`            | Not Available | Available          |
| Control Panel             | Not Available | Available          |
| Windows Explorer          | Not Available | Available          |
| Task Manager              | Available     | Available          |
| Internet Explorer / Edge  | Not Available | Available          |
| Remote Desktop Services   | Available     | Available          |

---

# Advantages of Server Core

Server Core is useful when we want:

* Lower resource consumption
* Smaller installation footprint
* Reduced attack surface
* Fewer GUI components to maintain
* Command-line and remote administration

These characteristics can make it attractive for servers that do not require a graphical environment.

---

# Disadvantages of Server Core

The main drawbacks include:

* Steeper learning curve
* Greater dependence on PowerShell and command-line administration
* Some GUI-based management tools are unavailable locally
* Some server applications are unsupported
* Administration may be more difficult for teams accustomed to GUI tools

---

# Choosing Between Them

The choice should depend mainly on:

* Business requirements
* Intended server role
* Software compatibility
* Administrative requirements
* Experience of the administrators maintaining the system

A server that requires applications or tools only available with the full GUI may need **Desktop Experience**.

A server that can be fully managed through PowerShell and remote tools may benefit from **Server Core**.

---

# Quick Reference

### Open Server Core Configuration

```cmd
sconfig
```

### Main Server Core Administration Methods

```text
Command Prompt
PowerShell
MMC from a remote machine
RSAT
Sconfig
```

### Main Difference

```text
Server Core
→ Minimal interface
→ Lower resource usage
→ Smaller attack surface
→ More CLI/remote management

Desktop Experience
→ Full Windows GUI
→ More local graphical tools
→ Easier GUI-based administration
→ Larger system footprint
```

---

## Key Takeaway

**Server Core is a lightweight Windows Server installation that removes most graphical components and relies primarily on command-line, PowerShell, and remote administration. Desktop Experience provides the full Windows GUI and a larger set of local graphical tools. Server Core generally uses fewer resources and has a smaller attack surface, but it requires stronger command-line administration skills and does not support every Windows Server application.**
