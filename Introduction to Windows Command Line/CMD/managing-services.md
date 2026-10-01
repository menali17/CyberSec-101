
# Managing Services

---

## Overview

Windows services are background processes responsible for performing system and application tasks, such as managing network connections, printing documents, and delivering Windows updates.

During penetration testing, understanding Windows services allows us to identify running processes, examine service configurations, and investigate potential privilege escalation opportunities.

In this section, we will learn how to:

- Enumerate running and stopped services.
- Inspect service states and process information.
- Start and stop services.
- Modify service configurations.
- Identify alternative commands for service enumeration.

---

## Service Controller (SC)

The **Service Controller (`sc.exe`)** is a Windows command-line utility that communicates with the Service Control Manager (SCM).

It allows us to query, configure, start, and stop Windows services locally or remotely, provided we have the necessary permissions.

Running `sc` without parameters displays its available commands and usage information.

```cmd
sc
```

| Command | Description |
|---|---|
| `sc query` | Retrieves service information and current states. |
| `sc queryex` | Retrieves extended service information, including the PID. |
| `sc start` | Starts a service. |
| `sc stop` | Requests that a service stop. |
| `sc pause` | Requests that a service pause, if supported. |
| `sc config` | Modifies an existing service's configuration. |

We can also query services on a remote Windows host:

```cmd
sc \\HOSTNAME query
```

Remote operations require appropriate network access and permissions.

---

## Querying Services

### Listing Active Services

We can enumerate currently running Windows services using:

```cmd
sc query type= service
```

Example output:

```text
SERVICE_NAME: Audiosrv
DISPLAY_NAME: Windows Audio
        TYPE               : 10  WIN32_OWN_PROCESS
        STATE              : 4  RUNNING
        WIN32_EXIT_CODE    : 0
        SERVICE_EXIT_CODE  : 0
```

The output provides information about each service, including its name, type, current state, and exit codes.

To enumerate both running and stopped services, we can execute:

```cmd
sc query type= service state= all
```

**Important:** The space following the equals sign is required in `sc` parameters.

For example, `type= service` is valid, while `type=service` is not.

### Understanding Service States

| State | Value | Description |
|---|---|---|
| STOPPED | 1 | The service is not running. |
| START_PENDING | 2 | The service is starting. |
| STOP_PENDING | 3 | The service is stopping. |
| RUNNING | 4 | The service is running. |

These states help us determine whether a service is active or transitioning between states.

### Querying a Specific Service

Instead of enumerating every service, we can query a particular service by its name.

For example, to inspect Windows Defender:

```cmd
sc query windefend
```

Example output:

```text
SERVICE_NAME: windefend
        TYPE               : 10  WIN32_OWN_PROCESS
        STATE              : 4  RUNNING
                             (NOT_STOPPABLE, NOT_PAUSABLE)
```

This indicates that the Windows Defender service is running and does not currently accept stop or pause requests.

### Extended Service Information

We can retrieve additional information using `queryex`:

```cmd
sc queryex Spooler
```

The extended output includes the Process ID (PID), allowing us to identify the process associated with the service.

---

## Starting and Stopping Services

The `sc` utility allows us to send control requests to Windows services.

However, these operations depend on our account's permissions, the service configuration, and whether the service supports the requested action.

### Stopping Services

We can stop a service using:

```cmd
sc stop <service_name>
```

For example, the Print Spooler service manages print jobs on Windows.

Before stopping it, we can verify its current state:

```cmd
sc query Spooler
```

To request that it stop:

```cmd
sc stop Spooler
```

Example output:

```text
SERVICE_NAME: Spooler
        STATE              : 3  STOP_PENDING
```

The `STOP_PENDING` state indicates that Windows has accepted the request and the service is shutting down.

We can verify the final state:

```cmd
sc query Spooler
```

Expected output:

```text
SERVICE_NAME: Spooler
        STATE              : 1  STOPPED
```

### Starting Services

We can start a stopped service using:

```cmd
sc start Spooler
```

The service may initially report:

```text
STATE : 2 START_PENDING
```

After initialization, another query should show:

```text
STATE : 4 RUNNING
```

### Service Permissions

Not every service can be stopped or modified by every user.

For example, attempting to stop Windows Defender from a standard user account may return:

```cmd
sc stop windefend
```

```text
Access is denied.
```

Some protected services may also reject requests from local administrators.

From a penetration testing perspective, we need to understand which operations our current account is authorized to perform.

Repeatedly attempting unauthorized service modifications can also generate security events and trigger monitoring alerts.

---

## Modifying Services

The `sc config` command allows us to modify existing service configurations.

Changes are recorded through the Service Control Manager and persisted in the Windows registry.

### Service Startup Types

| Startup Type | Description |
|---|---|
| `auto` | Starts automatically during system startup. |
| `demand` | Starts manually or when requested by another component. |
| `disabled` | Prevents ordinary attempts to start the service. |
| `delayed-auto` | Starts automatically after other automatic services have started. |

We can inspect a service's configuration using:

```cmd
sc qc Spooler
```

The output includes information such as its executable path, startup type, and service account.

### Changing a Service's Startup Type

The general syntax is:

```cmd
sc config <service_name> start= <startup_type>
```

For example, we can configure the Print Spooler service for manual startup:

```cmd
sc config Spooler start= demand
```

Successful execution returns:

```text
[SC] ChangeServiceConfig SUCCESS
```

Changing the startup type does not necessarily stop or start an already running service.

### Windows Update Services

The module demonstrates service configuration changes using two components associated with Windows updates.

| Service | Description |
|---|---|
| `wuauserv` | Windows Update service. |
| `BITS` | Background Intelligent Transfer Service, which transfers files in the background. |

We can inspect their current states using:

```cmd
sc query wuauserv
sc query bits
```

The module demonstrates how changing their startup configurations to `disabled` can interfere with normal update functionality.

**Security considerations:**

- Disabling update-related services can prevent the installation of important security updates.
- BITS is also used by applications other than Windows Update.
- Service configuration changes may persist after restarting the computer.
- These operations generally require elevated permissions and can generate security alerts.

After testing service configuration changes in an authorized laboratory, we should restore each service's original startup configuration.

---

## Alternative Commands for Service Enumeration

Although `sc` provides extensive functionality, Windows includes additional utilities for investigating services.

### Tasklist

The `tasklist` command displays processes running on a Windows host.

Using `/svc`, we can identify which services are associated with each process.

```cmd
tasklist /svc
```

Example output:

```text
Image Name       PID    Services
=============== ====== ==============================
lsass.exe         796   KeyIso, SamSs, VaultSvc
svchost.exe       984   DcomLaunch, PlugPlay, Power
svchost.exe       616   RpcEptMapper, RpcSs
```

This is especially useful when multiple services share a single process.

For example, Windows commonly uses `svchost.exe` to host several services.

### Net Start

The `net start` command displays all currently running services.

```cmd
net start
```

We can also use related commands to control services:

| Command | Description |
|---|---|
| `net start` | Lists running services or starts a specified service. |
| `net stop` | Stops a specified service. |
| `net pause` | Pauses a service that supports pausing. |
| `net continue` | Resumes a paused service. |

For example:

```cmd
net start Spooler
```

### WMIC

Windows Management Instrumentation Command-line (`WMIC`) allows us to retrieve information about the operating system, processes, services, and other Windows components.

To enumerate services:

```cmd
wmic service list brief
```

Example output:

```text
Name       ProcessId   StartMode   State     Status
Appinfo    5016        Manual      Running   OK
AppXSvc    9996        Manual      Running   OK
Audiosrv   2332        Auto        Running   OK
BITS       0           Manual      Stopped   OK
```

This provides a consolidated overview of each service's name, process ID, startup configuration, and current state.

**Important:** WMIC is deprecated and may be unavailable on newer Windows installations. For modern administration, PowerShell and supported Windows management APIs are preferable.

---

## Command Summary

| Command | Purpose |
|---|---|
| `sc query type= service` | Enumerates active Windows services. |
| `sc query type= service state= all` | Enumerates running and stopped services. |
| `sc query <service>` | Retrieves the current state of a specific service. |
| `sc queryex <service>` | Retrieves extended service information, including the PID. |
| `sc qc <service>` | Displays a service's configuration. |
| `sc stop <service>` | Requests that a service stop. |
| `sc start <service>` | Requests that a service start. |
| `sc config` | Modifies service configuration. |
| `tasklist /svc` | Displays running processes and their associated services. |
| `net start` | Lists currently running services. |
| `wmic service list brief` | Displays a summary of installed services, when WMIC is available. |

---

## Key Takeaways

- Windows services perform background tasks and are managed through the Service Control Manager.
- `sc.exe` allows us to query, start, stop, and configure services.
- Service states help us distinguish running, stopped, and transitioning services.
- Service control operations depend on account privileges and service protections.
- Misconfigured services may introduce security risks, including privilege escalation opportunities.
- `tasklist`, `net start`, and `wmic` provide alternative methods for service enumeration.
- Monitoring service modifications is important because unauthorized changes may indicate malicious activity.
