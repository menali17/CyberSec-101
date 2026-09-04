# Windows Services & Processes

## Windows Services

**Windows services** are long-running processes that perform background functions for the operating system and installed applications.

Services can start automatically during system boot without user interaction and can continue running even after a user logs out. They are responsible for functions such as networking, diagnostics, credential management, Windows updates, and application-specific background tasks.

Windows services are managed by the **Service Control Manager (SCM)**. The graphical interface can be opened with:

```cmd id="rcn8pq"
services.msc
```

It displays information such as the service name, description, status, startup type, and the account under which the service runs.

Services can also be queried and managed from the command line using `sc.exe` or PowerShell cmdlets such as `Get-Service`.

For example:

```powershell id="r22mvo"
PS C:\htb> Get-Service | ? {$_.Status -eq "Running"} | select -First 2 | fl
```

Output:

```powershell id="3nqojp"
Name                : AdobeARMservice
DisplayName         : Adobe Acrobat Update Service
Status              : Running
DependentServices   : {}
ServicesDependedOn  : {}
CanPauseAndContinue : False
CanShutdown         : False
CanStop             : True
ServiceType         : Win32OwnProcess

Name                : Appinfo
DisplayName         : Application Information
Status              : Running
DependentServices   : {}
ServicesDependedOn  : {RpcSs, ProfSvc}
CanPauseAndContinue : False
CanShutdown         : False
CanStop             : True
ServiceType         : Win32OwnProcess, Win32ShareProcess
```

Here:

* `Get-Service` retrieves the services.
* `? {$_.Status -eq "Running"}` filters for running services.
* `select -First 2` selects the first two results.
* `fl` formats the output as a list.

### Service States and Startup

Services may appear in states such as:

* `Running`
* `Stopped`
* `Paused`
* `Starting`
* `Stopping`

They can be configured to start manually, automatically, or with a delay during system boot.

Windows has three categories of services:

* Local Services
* Network Services
* System Services

Creating, modifying, and deleting services usually requires administrative privileges. Misconfigured service permissions are also a common **Windows privilege escalation vector**.

---

## Critical Windows Services and Processes

Some Windows components are critical and cannot be safely stopped and restarted without restarting the system.

Important examples include:

| Process        | Purpose                                            |
| -------------- | -------------------------------------------------- |
| `smss.exe`     | Session Manager SubSystem; handles system sessions |
| `csrss.exe`    | Client Server Runtime Process                      |
| `wininit.exe`  | Handles Windows initialization tasks               |
| `logonui.exe`  | Facilitates user login                             |
| `lsass.exe`    | Handles authentication and security policy         |
| `services.exe` | Manages starting and stopping services             |
| `winlogon.exe` | Handles secure logon and user profiles             |
| `System`       | Background process running the Windows kernel      |
| `svchost.exe`  | Hosts Windows services implemented through DLLs    |

`svchost.exe`, or **Service Host**, is particularly common because Windows uses it to host services implemented as DLLs, including components associated with Windows Update, Windows Firewall, and Plug and Play.

---

# Processes

A **process** is a running instance of a program.

Processes can start automatically as part of Windows or be created by installed applications. Many application processes can be terminated without seriously affecting the operating system, but terminating critical Windows processes can cause components or the entire system to stop functioning correctly.

The important distinction is:

```text id="ap7rku"
Process → a running program

Service → a Windows-managed background function
          that runs through a process
```

A normal application can create processes without being a Windows service.

---

# Local Security Authority Subsystem Service (LSASS)

`lsass.exe` is one of the most important Windows processes from a security perspective.

**LSASS (Local Security Authority Subsystem Service)** is responsible for enforcing Windows security policy.

When a user attempts to log in, LSASS verifies the logon attempt and creates **access tokens** based on the user's permission level. It also handles user password changes.

Logon and logoff activity associated with LSASS is recorded in the **Windows Security Log**.

LSASS is also a high-value security target because credential-related information can exist in its process memory, and tools exist that can extract cleartext or hashed credentials from that memory.

For this section, the main points to remember are:

```text id="yp60c7"
lsass.exe
├── Authentication
├── Security policy
├── Access tokens
├── Password changes
└── Credential-related information
```

---

# Sysinternals Tools

**Sysinternals** is a suite of portable Windows administration and troubleshooting tools. Most can be used without traditional installation.

The tools can be downloaded or accessed through Microsoft's network share:

```text id="m5ib9f"
\\live.sysinternals.com\tools
```

For example, `ProcDump` can be executed directly from the share:

```cmd id="h2c4d9"
C:\htb> \\live.sysinternals.com\tools\procdump.exe -accepteula
```

Output:

```cmd id="qcmqnr"
ProcDump v9.0 - Sysinternals process dump utility
Copyright (C) 2009-2017 Mark Russinovich and Andrew Richards
Sysinternals - www.sysinternals.com

Monitors a process and writes a dump file when the process exceeds the
specified criteria or has an exception.
```

Important Sysinternals tools introduced in this section include:

| Tool                 | Purpose                                             |
| -------------------- | --------------------------------------------------- |
| **Process Explorer** | Advanced process inspection                         |
| **Process Monitor**  | Monitors filesystem, Registry, and process activity |
| **TCPView**          | Monitors network activity                           |
| **PsExec**           | Remote system management through SMB                |
| **ProcDump**         | Creates process memory dumps                        |

These tools are useful in penetration testing for discovering interesting processes, privilege escalation opportunities, and lateral movement possibilities.

---

# Task Manager

**Task Manager** is Windows' built-in interface for inspecting processes, resource usage, services, startup applications, and logged-in users.

It can be opened with:

```text id="vy6j6m"
Ctrl + Shift + Esc
```

or from CMD/PowerShell:

```cmd id="yyv5uq"
taskmgr
```

Its main tabs include:

| Tab             | Purpose                                                                        |
| --------------- | ------------------------------------------------------------------------------ |
| **Processes**   | Running applications/processes and CPU, memory, disk, network, and power usage |
| **Performance** | CPU, memory, disk, network, GPU, uptime, and access to Resource Monitor        |
| **App history** | Historical application resource usage                                          |
| **Startup**     | Applications configured to start automatically                                 |
| **Users**       | Logged-in users and their processes/resource usage                             |
| **Details**     | PID, status, username, CPU, and memory information                             |
| **Services**    | Installed services, PID, description, and status                               |

A **PID (Process ID)** uniquely identifies a particular running process.

For example:

```text id="nm8jhc"
Process             PID
powershell.exe      5820
chrome.exe          4312
```

---

# Process Explorer

**Process Explorer** is part of the Sysinternals suite and provides more detailed process information than Task Manager.

It can display:

* Currently running processes
* Handles opened by processes
* Loaded DLLs
* Memory-mapped files
* Parent-child process relationships

It can also search for processes associated with a specific handle or DLL.

Parent-child relationships are particularly useful when investigating how a process was created:

```text id="6g5kxr"
explorer.exe
    └── powershell.exe
            └── another_process.exe
```

Here, `explorer.exe` created `powershell.exe`, which then created `another_process.exe`.

Process Explorer can also help identify orphaned processes that remain after their original parent process terminates.

---

# Security Perspective

Services and processes are important sources of information during Windows security analysis.

For **services**, we may be interested in:

* Which services are installed and running.
* Which accounts they run under.
* How they start.
* Whether their permissions are misconfigured.

For **processes**, we may examine:

* Which programs are currently running.
* Which user owns a process.
* Its PID.
* Its parent and child processes.
* Loaded DLLs and handles.
* Associated system or network activity.

This information can help identify suspicious behavior, interesting system components, and possible privilege escalation paths.

---

# Quick Reference

### Services

```cmd id="vx2gtt"
services.msc
```

```powershell id="ffxlj4"
Get-Service
```

```powershell id="9yrxxk"
Get-Service | ? {$_.Status -eq "Running"}
```

### Task Manager

```text id="e1u4br"
Ctrl + Shift + Esc
```

```cmd id="1tjg3r"
taskmgr
```

### Important Processes

| Process        | Main Role                     |
| -------------- | ----------------------------- |
| `lsass.exe`    | Authentication and security   |
| `services.exe` | Service management            |
| `svchost.exe`  | Hosts Windows services        |
| `winlogon.exe` | Logon/session handling        |
| `smss.exe`     | Session management            |
| `csrss.exe`    | Windows subsystem             |
| `System`       | Kernel-related system process |

### Sysinternals

| Tool             | Main Role                  |
| ---------------- | -------------------------- |
| Process Explorer | Detailed process analysis  |
| Process Monitor  | System activity monitoring |
| TCPView          | Network monitoring         |
| PsExec           | Remote management via SMB  |
| ProcDump         | Process memory dumps       |

---

## Key Takeaway

**Windows services are long-running background components managed by the Service Control Manager, while processes are running instances of programs. Services and processes are important security targets because they reveal what is running, under which account, and with which privileges. `lsass.exe` is particularly important because of its role in authentication and credential-related activity. Task Manager provides general visibility, while Sysinternals tools such as Process Explorer and Process Monitor provide deeper system and process analysis.**
