# Service Permissions

Windows services are an important security consideration because **misconfigured service permissions** can create opportunities for:

* Malicious DLL or executable execution
* Privilege escalation
* Persistence
* Service disruption

These problems are often introduced by third-party software or mistakes made during installation and configuration.

---

# Service Accounts

A Windows service executes under the context of a particular **user account**.

If a critical service is configured to run using a normal employee account, disabling that account can cause the service to stop working.

For example, if a DHCP service runs as the user `Bob` and Bob's account is later disabled, the service may fail to start. Since DHCP is responsible for leasing IP addresses, this could cause network disruption.

For this reason, organizations commonly use dedicated **service accounts** for services rather than normal user accounts.

---

# Examining Services with `services.msc`

The graphical Services console can be opened with:

```cmd id="8c3fgq"
services.msc
```

It allows us to inspect and configure properties of installed services.

One important property is:

```text id="n3fs49"
Path to executable
```

This specifies the program and command that Windows executes when the service starts.

If the NTFS permissions on the executable or its directory are too permissive, an attacker may be able to replace the legitimate executable with a malicious one.

---

# Service Logon Accounts

Services can execute using different accounts.

Notable built-in Windows service accounts include:

| Account          | Description                                                   |
| ---------------- | ------------------------------------------------------------- |
| `LocalService`   | Built-in account with limited local privileges                |
| `NetworkService` | Built-in account used by services that require network access |
| `LocalSystem`    | Highly privileged built-in system account                     |

`LocalSystem` has extremely high privileges on the local Windows system.

Not every service requires this level of access. Services should therefore run with the minimum privileges necessary, following the **Principle of Least Privilege**.

Dedicated accounts can also be created specifically to run individual services.

---

# Service Recovery

The **Recovery** configuration determines what Windows should do when a service fails.

Possible recovery actions can include restarting the service, restarting the computer, or executing a program.

A service configured to execute a program after failure can therefore become another security concern if an attacker is able to manipulate that configuration.

---

# Examining Services with `sc`

The Windows:

```cmd id="i81wtt"
sc.exe
```

utility can query, configure, start, stop, and inspect services.

To query the configuration of the Windows Update service:

```cmd id="0tb8ls"
C:\Users\htb-student> sc qc wuauserv

[SC] QueryServiceConfig SUCCESS

SERVICE_NAME: wuauserv
        TYPE               : 20  WIN32_SHARE_PROCESS
        START_TYPE         : 3   DEMAND_START
        ERROR_CONTROL      : 1   NORMAL
        BINARY_PATH_NAME   : C:\WINDOWS\system32\svchost.exe -k netsvcs -p
        LOAD_ORDER_GROUP   :
        TAG                : 0
        DISPLAY_NAME       : Windows Update
        DEPENDENCIES       : rpcss
        SERVICE_START_NAME : LocalSystem
```

Here, several fields are particularly useful:

| Field                | Meaning                         |
| -------------------- | ------------------------------- |
| `SERVICE_NAME`       | Internal service name           |
| `START_TYPE`         | How the service starts          |
| `BINARY_PATH_NAME`   | Program executed by the service |
| `DEPENDENCIES`       | Other services it depends on    |
| `SERVICE_START_NAME` | Account used to run the service |

For example:

```text id="jz19fb"
SERVICE_START_NAME : LocalSystem
```

shows that Windows Update runs under the highly privileged `LocalSystem` account.

---

# Querying Remote Services

`sc` can also query services on another Windows machine.

The hostname or IP address is placed immediately after `sc`:

```cmd id="vhbfjr"
C:\Users\htb-student> sc \\hostname query ServiceName
```

This makes `sc` useful for remote administration as well as local service inspection.

---

# Starting and Stopping Services

`sc` can control the state of a service.

For example:

```cmd id="1s6hwf"
C:\Users\htb-student> sc stop wuauserv

[SC] OpenService FAILED 5:

Access is denied.
```

The command fails because the current terminal does not have sufficient privileges.

Many service-management operations require an **elevated administrative context**.

---

# Changing a Service Executable

With sufficient privileges, `sc config` can modify a service configuration.

For example, the material changes the executable associated with `wuauserv`:

```cmd id="s6wjxa"
C:\WINDOWS\system32> sc config wuauserv binPath=C:\Winbows\Perfectlylegitprogram.exe

[SC] ChangeServiceConfig SUCCESS
```

Querying it afterward:

```cmd id="2dtzfs"
C:\WINDOWS\system32> sc qc wuauserv

[SC] QueryServiceConfig SUCCESS

SERVICE_NAME: wuauserv
        TYPE               : 20  WIN32_SHARE_PROCESS
        START_TYPE         : 3   DEMAND_START
        ERROR_CONTROL      : 1   NORMAL
        BINARY_PATH_NAME   : C:\Winbows\Perfectlylegitprogram.exe
        LOAD_ORDER_GROUP   :
        TAG                : 0
        DISPLAY_NAME       : Windows Update
        DEPENDENCIES       : rpcss
        SERVICE_START_NAME : LocalSystem
```

The important change is:

```text id="vixmt9"
Before:
BINARY_PATH_NAME → C:\WINDOWS\system32\svchost.exe ...

After:
BINARY_PATH_NAME → C:\Winbows\Perfectlylegitprogram.exe
```

Because the service runs as `LocalSystem`, unauthorized ability to modify this configuration would be a serious security problem.

`sc` is also useful during malware investigations because it provides a script-friendly way to inspect commonly targeted or newly created services.

---

# Service Security Descriptors

We can inspect the permissions assigned directly to a service with:

```cmd id="t4wnm1"
C:\WINDOWS\system32> sc sdshow wuauserv
```

Output:

```cmd id="nqk60f"
D:(A;;CCLCSWRPLORC;;;AU)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;BA)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;SY)S:(AU;FA;CCDCLCSWRPWPDTLOSDRCWDWO;;;WD)
```

This output is a **security descriptor** represented using **Security Descriptor Definition Language (SDDL)**.

Windows securable objects have security descriptors that identify information such as the object's owner and access-control information.

Two important components are:

| Component | Purpose                                               |
| --------- | ----------------------------------------------------- |
| **DACL**  | Controls who is allowed or denied access to an object |
| **SACL**  | Controls auditing/logging of access attempts          |

---

# Understanding SDDL

Consider the first Access Control Entry (ACE):

```text id="eewykc"
D:(A;;CCLCSWRPLORC;;;AU)
```

The initial:

```text id="3udqh5"
D:
```

indicates that the following entries belong to the **DACL**.

Inside the ACE:

```text id="7hh6us"
(A;;CCLCSWRPLORC;;;AU)
```

we can identify:

```text id="0z4afg"
A                  → Allow
CCLCSWRPLORC       → Allowed service operations
AU                 → Authenticated Users
```

The individual permission codes in this example include:

| Code | Permission                     |
| ---- | ------------------------------ |
| `CC` | `SERVICE_QUERY_CONFIG`         |
| `LC` | `SERVICE_QUERY_STATUS`         |
| `SW` | `SERVICE_ENUMERATE_DEPENDENTS` |
| `RP` | `SERVICE_START`                |
| `LO` | `SERVICE_INTERROGATE`          |
| `RC` | `READ_CONTROL`                 |

Therefore:

```text id="om01xp"
(A;;CCLCSWRPLORC;;;AU)
```

essentially describes **which operations Authenticated Users are allowed to perform on this service**.

Each pair of characters represents a particular permission, and the security principal appears after the final semicolons.

A complete service security descriptor can contain multiple ACEs because different users and groups may have different permissions.

---

# DACL, ACL, and ACE

The concepts connect directly with the ACL concepts seen previously:

```text id="1n2znr"
DACL
 ├── ACE → permissions for one principal
 ├── ACE → permissions for another principal
 └── ACE → permissions for another principal
```

An **ACL (Access Control List)** is a collection of **ACEs (Access Control Entries)**.

Each ACE specifies permissions for a particular **security principal**, such as a user or group.

The DACL determines who is allowed or denied access to the object.

---

# Examining Service Permissions with PowerShell

PowerShell provides a more readable way to inspect permissions associated with a service's Registry key.

The service configuration is stored under:

```text id="cnl6ov"
HKLM:\System\CurrentControlSet\Services\
```

For Windows Update:

```powershell id="cpipbb"
PS C:\Users\htb-student> Get-ACL -Path HKLM:\System\CurrentControlSet\Services\wuauserv | Format-List
```

Output includes:

```powershell id="t0vm7o"
Path   : Microsoft.PowerShell.Core\Registry::HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\wuauserv
Owner  : NT AUTHORITY\SYSTEM
Group  : NT AUTHORITY\SYSTEM
Access : BUILTIN\Users Allow ReadKey
         BUILTIN\Administrators Allow FullControl
         NT AUTHORITY\SYSTEM Allow FullControl
         APPLICATION PACKAGE AUTHORITY\ALL APPLICATION PACKAGES Allow ReadKey
```

The full output also includes the corresponding SDDL representation.

This makes information such as:

```text id="u5p03p"
Owner  → SYSTEM

Users
└── ReadKey

Administrators
└── FullControl

SYSTEM
└── FullControl
```

much easier to interpret than the raw SDDL output.

The PowerShell output also exposes the SIDs representing the different security principals.

---

# Why Command-Line Service Inspection Matters

GUI tools such as `services.msc` are useful for inspecting individual systems, but command-line tools are easier to automate.

This becomes increasingly important when working with:

* Multiple machines
* Large Windows environments
* Domains
* Automated security assessments

The primary tools covered are therefore:

| Tool            | Main Purpose                                             |
| --------------- | -------------------------------------------------------- |
| `services.msc`  | Graphical service inspection and configuration           |
| `sc qc`         | Query service configuration                              |
| `sc start/stop` | Control service state                                    |
| `sc config`     | Modify service configuration                             |
| `sc sdshow`     | Display service security descriptor                      |
| `Get-Acl`       | Inspect permissions in a more readable PowerShell format |

---

# Security Perspective

When examining a Windows service, important questions include:

1. **Which account runs the service?**
2. **How privileged is that account?**
3. **Which executable does the service run?**
4. **Who can modify that executable or its directory?**
5. **Who can modify the service configuration?**
6. **What happens when the service fails?**
7. **Which users or groups can start, stop, or reconfigure the service?**

A dangerous situation could therefore involve:

```text id="fh4apx"
Highly privileged service
        +
Weak service/file permissions
        =
Potential privilege escalation
```

---

# Quick Reference

### Open Services

```cmd id="kblyj9"
services.msc
```

### Query Service Configuration

```cmd id="x02kjm"
sc qc ServiceName
```

### Query a Remote Service

```cmd id="t8ulwq"
sc \\hostname query ServiceName
```

### Stop a Service

```cmd id="y8gw1g"
sc stop ServiceName
```

### Change the Executable Path

```cmd id="u0rbv3"
sc config ServiceName binPath=C:\Path\program.exe
```

### Display Service Permissions

```cmd id="cv07k9"
sc sdshow ServiceName
```

### Examine the Service Registry ACL

```powershell id="8bs5mk"
Get-ACL -Path HKLM:\System\CurrentControlSet\Services\ServiceName | Format-List
```

### Built-in Service Accounts

```text id="o8b0mh"
LocalService
NetworkService
LocalSystem
```

### SDDL Basics

```text id="wrx3t8"
D: → DACL
A  → Allow
D  → Deny

AU → Authenticated Users
BA → Built-in Administrators
SY → Local System
```

---

## Key Takeaway

**Windows services execute under specific accounts and have their own permissions, executable paths, and security descriptors. A service running with high privileges can become a privilege escalation vector if an unauthorized user can modify its configuration, executable, or related files. We can inspect services through `services.msc`, query and manage them with `sc.exe`, inspect raw permissions with `sc sdshow`, and use PowerShell `Get-Acl` for a more readable view of access permissions. Understanding service accounts, DACLs, ACEs, and SDDL is therefore important when analyzing Windows service security.**
