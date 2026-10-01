
# Working with Services — PowerShell

---

## Overview

Windows services are background components responsible for maintaining system functionality and supporting applications.

In this section, we will learn how to manage services using PowerShell, including:

- Enumerating and filtering Windows services.
- Identifying stopped or potentially misconfigured services.
- Starting, stopping, restarting, and modifying services.
- Querying services on remote Windows hosts.
- Using PowerShell Remoting to investigate multiple computers.

The HTB module approaches these operations from a **system administrator's perspective**, using a scenario in which we investigate a computer experiencing problems with Microsoft Defender.

---

## 1. Managing Services with PowerShell

Windows services generally operate in the background without requiring direct user interaction.

PowerShell provides several cmdlets for managing them through the `Microsoft.PowerShell.Management` module.

### Discovering Service Cmdlets

If we are unsure which command to use, we can search PowerShell's help system:

```powershell
Get-Help *-Service
```

Common service management cmdlets include:

| Cmdlet | Description |
|---|---|
| `Get-Service` | Retrieves information about Windows services. |
| `Start-Service` | Starts a stopped service. |
| `Stop-Service` | Stops a running service. |
| `Restart-Service` | Restarts a service. |
| `Suspend-Service` | Pauses a service, if supported. |
| `Resume-Service` | Resumes a paused service. |
| `Set-Service` | Modifies service properties and configurations. |
| `New-Service` | Creates a new Windows service. |
| `Remove-Service` | Deletes a service on supported PowerShell versions. |

**Permissions:** Querying services generally requires fewer privileges than modifying them. Starting, stopping, and reconfiguring services requires appropriate permissions, often local administrator privileges.

---

## 2. Enumerating Windows Services

### Get-Service

The `Get-Service` cmdlet retrieves information about the services installed on a Windows host.

```powershell
Get-Service
```

It displays properties such as the service's name, display name, and current status.

We can customize the output using `Format-Table`:

```powershell
Get-Service | Format-Table DisplayName,Status
```

The HTB example returns services with different statuses:

    DisplayName                           Status
    -----------                           ------
    Windows Audio                         Running
    Application Identity                  Stopped
    Microsoft Defender Antivirus Service Stopped

### Counting Services

We can determine how many services were retrieved using `Measure-Object`:

```powershell
Get-Service | Measure-Object
```

In the HTB scenario, this command returns 321 services.

Rather than inspecting every service individually, we can narrow our results using `Where-Object`.

---

## 3. Filtering Services

### Investigating Microsoft Defender

In the HTB scenario, Mr. Tanaka reports that Microsoft Defender appears to have been disabled and his computer is performing poorly.

Our first task is to identify which Defender-related services are running.

```powershell
Get-Service |
    Where-Object DisplayName -like '*Defender*' |
    Format-Table DisplayName,ServiceName,Status
```

The command performs three operations:

1. `Get-Service` retrieves the system's services.
2. `Where-Object` selects services whose display names contain `Defender`.
3. `Format-Table` displays only the relevant properties.

The HTB example produces the following results:

| Service | Internal name | Status |
|---|---|---|
| Windows Defender Firewall | `mpssvc` | Running |
| Defender Advanced Threat Protection | `Sense` | Stopped |
| Defender Antivirus Network Inspection | `WdNisSvc` | Running |
| Microsoft Defender Antivirus | `WinDefend` | Stopped |

From this output, we can identify that the `WinDefend` service is not running.

---

## 4. Starting and Stopping Services

### Start-Service

We can request that a stopped service start using `Start-Service`.

The HTB exercise demonstrates this with Microsoft Defender:

```powershell
Start-Service WinDefend
```

Afterward, we can verify its status:

```powershell
Get-Service WinDefend
```

Expected output in the laboratory:

    Status   Name       DisplayName
    ------   ----       -----------
    Running  WinDefend  Microsoft Defender Antivirus Service

**Important:** On an actual Windows installation, Defender may have additional protections or configuration restrictions. Even an administrator may be unable to start or stop certain protected services directly.

### Stop-Service

We can stop a running service using `Stop-Service`.

For example:

```powershell
Stop-Service Spooler
```

To verify the result:

```powershell
Get-Service Spooler
```

If successful, its status changes to `Stopped`.

### Restart-Service

We can also restart a service:

```powershell
Restart-Service Spooler
```

This requests that Windows stop the service and start it again.

### Service States

| State | Description |
|---|---|
| `Running` | The service is currently executing. |
| `Stopped` | The service is not running. |
| `Paused` | The service has been temporarily suspended. |
| `StartPending` | The service is starting. |
| `StopPending` | The service is stopping. |

Not every service supports stopping or pausing. The requested operation must also be permitted by its security configuration.

---

## 5. Modifying Service Configurations

The `Set-Service` cmdlet allows us to modify the configuration of an existing Windows service.

### Investigating a Suspicious Service

In the HTB scenario, we discover that the Print Spooler service has an unusual display name:

    Name       DisplayName
    ----       -----------
    Spooler    Totally still used for Print Spooling...

An unexpected service name or configuration change may warrant further investigation, although it does not independently prove that the system has been compromised.

In the laboratory, we stop the service while the security team investigates.

### Checking Service Properties

We can examine the service's current status, startup type, and display name:

```powershell
Get-Service Spooler |
    Select-Object Name,StartType,Status,DisplayName
```

Example:

    Name     StartType  Status   DisplayName
    ----     ---------  ------   -----------
    Spooler  Automatic  Stopped  Totally still used...

### Changing the Startup Type

We can change a service's startup configuration using `Set-Service`:

```powershell
Set-Service -Name Spooler -StartupType Disabled
```

The `Disabled` startup type prevents the service from starting normally until its configuration is changed again.

We can verify the modification:

```powershell
Get-Service Spooler |
    Select-Object Name,StartType,Status
```

Expected output:

    Name     StartType  Status
    ----     ---------  ------
    Spooler  Disabled   Stopped

### Service Startup Types

| Startup type | Description |
|---|---|
| `Automatic` | Starts automatically during system startup. |
| `Manual` | Starts when requested by a user or another component. |
| `Disabled` | Prevents the service from starting normally. |

**Important distinction:** The service's current status and startup type are separate properties. A service can currently be running even if its startup configuration is subsequently changed to `Disabled`.

For administrative work, we should document the original configuration before changing it and restore services when appropriate.

---

## 6. Removing Services

The `Remove-Service` cmdlet is available in newer versions of PowerShell, including PowerShell 7.

It is not available by default in Windows PowerShell 5.1, which is the version used in much of the HTB laboratory.

For Windows PowerShell 5.1, the module identifies the Windows Service Controller executable, `sc.exe`, as an alternative for service administration.

We should distinguish between stopping a service and deleting its registration: stopping a service leaves its configuration intact.

---

## 7. Managing Remote Services

PowerShell also allows us to investigate services on other Windows computers when we have appropriate network connectivity and permissions.

### Get-Service -ComputerName

The HTB laboratory demonstrates querying a remote domain controller:

```powershell
Get-Service -ComputerName ACADEMY-ICL-DC
```

This retrieves the services on the specified remote computer.

**Version note:** The `-ComputerName` parameter is available in Windows PowerShell 5.1 but is not supported by `Get-Service` in PowerShell 7. For PowerShell 7, we can use PowerShell Remoting instead.

### Filtering Remote Services

In Windows PowerShell 5.1, we can combine remote enumeration with `Where-Object`:

```powershell
Get-Service -ComputerName ACADEMY-ICL-DC |
    Where-Object { $_.Status -eq 'Running' }
```

This retrieves services from the remote host and displays only those currently running.

Because PowerShell returns structured objects, we can continue filtering and selecting their properties even when the information originates from another computer.

---

## 8. Invoke-Command

The `Invoke-Command` cmdlet allows us to execute PowerShell commands on local or remote computers.

It is particularly useful when we need to perform the same administrative operation across multiple hosts.

### Querying Services on Multiple Computers

The HTB module uses the following example:

```powershell
Invoke-Command `
    -ComputerName ACADEMY-ICL-DC,LOCALHOST `
    -ScriptBlock {
        Get-Service -Name 'WinDefend'
    }
```

Let's examine the parameters:

| Component | Description |
|---|---|
| `Invoke-Command` | Executes a command on one or more computers. |
| `-ComputerName` | Specifies the computers on which the command will execute. |
| `-ScriptBlock` | Contains the PowerShell commands to execute remotely. |
| `{ }` | Defines the boundaries of the script block. |

The command queries the Defender service on both computers.

The resulting objects include a `PSComputerName` property identifying which host returned each result.

For example:

    Status   Name       PSComputerName
    ------   ----       --------------
    Running  WinDefend  LOCALHOST
    Running  WinDefend  ACADEMY-ICL-DC

Remote execution requires a suitable PowerShell Remoting configuration, network connectivity, authentication, and sufficient permissions.

### Administrative Applications

This approach allows us to inspect multiple computers without manually opening an interactive session on each machine.

For example, we can examine whether a particular service is running across several domain-joined systems.

It can also support incident investigations when we need to identify unusual service configurations across an organization.

---

## 9. Security Considerations

Windows services are relevant to both defensive administration and penetration testing.

During a security assessment, we may encounter:

- Services running with unnecessarily high privileges.
- Weak service permissions that allow unauthorized modifications.
- Unexpected changes to service executables or configurations.
- Disabled security services.
- Suspicious service display names.
- Services that have been created or modified without authorization.

Misconfigured service permissions can introduce privilege escalation opportunities. Unexpected service changes may also provide evidence of persistence or other unauthorized activity.

From an administrative perspective, regularly inspecting service status and configuration helps us maintain system security and investigate potential compromises.

---

## Command Summary

| Command | Purpose |
|---|---|
| `Get-Help *-Service` | Discovers service-related cmdlets. |
| `Get-Service` | Lists Windows services. |
| `Get-Service WinDefend` | Retrieves a particular service. |
| `Get-Service \| Measure-Object` | Counts the retrieved services. |
| `Where-Object` | Filters services by their properties. |
| `Start-Service` | Starts a service. |
| `Stop-Service` | Stops a service. |
| `Restart-Service` | Restarts a service. |
| `Set-Service` | Changes a service's configuration. |
| `Get-Service -ComputerName` | Queries remote services in Windows PowerShell 5.1. |
| `Invoke-Command` | Executes commands locally or remotely. |

---

## Key Takeaways

- PowerShell provides dedicated cmdlets for administering Windows services.
- `Get-Service` retrieves service information, and `Where-Object` allows us to filter the results.
- `Start-Service`, `Stop-Service`, and `Restart-Service` control service execution.
- `Set-Service` modifies persistent service configurations.
- A service's current status is different from its startup type.
- Managing services requires appropriate permissions, and some services have additional protections.
- Windows PowerShell 5.1 supports remote service queries through `Get-Service -ComputerName`.
- `Invoke-Command` allows us to execute commands on multiple computers through PowerShell Remoting.
- Investigating unusual or unauthorized service modifications is an important part of Windows security administration.
