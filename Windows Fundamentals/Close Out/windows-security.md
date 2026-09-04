# Windows Security

Windows security is a critical topic because Windows systems contain many applications, services, features, and configuration layers. This creates a large **attack surface**, and misconfigurations can expose a system even when it is fully patched.

Microsoft has continuously added security mechanisms to help administrators harden systems, control access, and detect malicious activity.

This section introduces several important Windows security concepts:

* Security Identifiers (SIDs)
* SAM, ACLs, and ACEs
* User Account Control (UAC)
* Windows Registry security concepts
* Run and RunOnce keys
* Application whitelisting
* AppLocker
* Local Group Policy
* Windows Defender Antivirus

---

# Security Identifier (SID)

Every **security principal** in Windows has a unique **Security Identifier (SID)**.

Security principals include entities such as:

* Users
* Groups
* Computers
* Processes operating under a security identity

Windows uses the SID rather than the visible account name to uniquely identify an account and determine its permissions.

This means that two accounts with the same visible name can still be distinguished because they have different SIDs.

We can display the SID associated with the current user using:

```powershell id="1pc4vw"
PS C:\htb> whoami /user

USER INFORMATION
----------------

User Name           SID
=================== =============================================
ws01\bob            S-1-5-21-674899381-4069889467-2080702030-1002
```

---

## SID Structure

A SID follows the general format:

```text id="8d2n3p"
(SID)-(Revision)-(Identifier Authority)-(Subauthorities)-(RID)
```

For example:

```text id="32ap9c"
S-1-5-21-674899381-4069889467-2080702030-1002
```

The material breaks it down as:

| Part                              | Meaning                                              |
| --------------------------------- | ---------------------------------------------------- |
| `S`                               | Identifies the string as a SID                       |
| `1`                               | Revision level                                       |
| `5`                               | Identifier Authority                                 |
| `21`                              | Subauthority                                         |
| `674899381-4069889467-2080702030` | Identifies the computer or domain                    |
| `1002`                            | Relative Identifier (RID) distinguishing the account |

The **RID** is especially important because it distinguishes accounts within the same SID authority.

---

# SAM, ACLs, and ACEs

Windows uses several related mechanisms to determine what an identity is allowed to access.

The **Security Accounts Manager (SAM)** is involved in Windows account and security information.

Permissions on securable objects are represented using:

* **ACL — Access Control List**
* **ACE — Access Control Entry**

An ACL contains individual ACEs describing which users, groups, or processes have particular rights over an object.

For example, an ACE could specify that a particular user has:

```text id="y9k1ne"
Read
Write
Modify
Full Control
```

over a file or directory.

---

## DACL and SACL

Security descriptors can contain two important types of ACL:

| Type     | Purpose                                                                      |
| -------- | ---------------------------------------------------------------------------- |
| **DACL** | Discretionary Access Control List — controls who is allowed or denied access |
| **SACL** | System Access Control List — controls auditing of access attempts            |

Access tokens are also involved in the authorization process.

When a user starts a process or thread, Windows uses security information associated with the user's token, including SIDs, to determine what actions are authorized.

The **Local Security Authority (LSA)** is an important component of this authorization process.

These concepts are particularly important when studying **Windows privilege escalation**.

---

# User Account Control (UAC)

**User Account Control (UAC)** is designed to prevent unauthorized or unintended system-wide changes.

A common example occurs when software requires administrator privileges.

Windows displays a consent or credential prompt before allowing the elevated action to continue.

For an administrator, this may require confirmation.

For a standard user, Windows may instead require administrator credentials.

UAC therefore creates an additional elevation step before privileged actions can execute.

---

# Windows Registry

The **Windows Registry** is a hierarchical database containing configuration information for Windows and applications.

It stores both:

* Computer-specific configuration
* User-specific configuration

Registry Editor can be opened with:

```cmd id="6es1xr"
regedit
```

The Registry follows a hierarchy of:

```text id="cn5by7"
Root Key → Key → Subkey → Value
```

---

## Registry Value Types

The material introduces several Registry data types:

| Type                      | Description                                        |
| ------------------------- | -------------------------------------------------- |
| `REG_BINARY`              | Binary data                                        |
| `REG_DWORD`               | 32-bit number                                      |
| `REG_DWORD_LITTLE_ENDIAN` | 32-bit little-endian number                        |
| `REG_DWORD_BIG_ENDIAN`    | 32-bit big-endian number                           |
| `REG_EXPAND_SZ`           | String containing expandable environment variables |
| `REG_LINK`                | Symbolic-link target                               |
| `REG_MULTI_SZ`            | Multiple strings                                   |
| `REG_NONE`                | No defined type                                    |
| `REG_QWORD`               | 64-bit number                                      |
| `REG_QWORD_LITTLE_ENDIAN` | 64-bit little-endian number                        |
| `REG_SZ`                  | String                                             |

We already encountered the most common ones:

```text id="as3v8q"
REG_SZ       → String
REG_DWORD    → 32-bit number
REG_QWORD    → 64-bit number
REG_MULTI_SZ → Multiple strings
REG_BINARY   → Binary data
```

---

# HKEY_LOCAL_MACHINE (HKLM)

Registry root keys begin with `HKEY`.

One important example is:

```text id="f7mw5d"
HKEY_LOCAL_MACHINE
```

which is commonly abbreviated:

```text id="w91bqn"
HKLM
```

`HKLM` contains configuration relevant to the local machine.

The material lists important subkeys such as:

```text id="oz8f31"
SAM
SECURITY
SYSTEM
SOFTWARE
HARDWARE
BCD
```

---

# Registry Files on Disk

The system Registry is backed by files stored on disk.

Important system Registry files can be found under:

```text id="9um4v2"
C:\Windows\System32\Config\
```

Examples include:

```text id="q9e0fv"
SAM
SECURITY
SOFTWARE
SYSTEM
DEFAULT
```

The current user's Registry hive is stored separately in the user's profile as:

```text id="9khd7b"
C:\Users\<USERNAME>\NTUSER.DAT
```

For example:

```text id="2kz8vn"
C:\Users\bob\NTUSER.DAT
```

---

# Run and RunOnce Registry Keys

Windows contains Registry locations that can cause applications to execute when Windows starts or when a user logs in.

The four keys introduced in the material are:

```text id="mx61ue"
HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run

HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run

HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\RunOnce

HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\RunOnce
```

These locations are important from both administrative and security perspectives because they can be used to automatically start software.

---

## Querying Run Keys

We can inspect the machine-wide `Run` key with:

```powershell id="nq7d1j"
PS C:\htb> reg query HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run
```

Example:

```powershell id="ix0u5y"
HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run
    SecurityHealth    REG_EXPAND_SZ    %windir%\system32\SecurityHealthSystray.exe
    RTHDVCPL          REG_SZ           "C:\Program Files\Realtek\Audio\HDA\RtkNGUI64.exe" -s
    Greenshot         REG_SZ           C:\Program Files\Greenshot\Greenshot.exe
```

For the current user:

```powershell id="jq5w4n"
PS C:\htb> reg query HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run
```

Example:

```powershell id="t17qkp"
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run
    OneDrive          REG_SZ    "C:\Users\bob\AppData\Local\Microsoft\OneDrive\OneDrive.exe" /background
    OPENVPN-GUI       REG_SZ    C:\Program Files\OpenVPN\bin\openvpn-gui.exe
    Docker Desktop    REG_SZ    C:\Program Files\Docker\Docker\Docker Desktop.exe
```

These keys are relevant to security because automatic execution mechanisms can also be used to maintain access to a system.

---

# Application Whitelisting

**Application whitelisting** defines which applications or executables are explicitly allowed to run.

The basic principle is:

```text id="bh70ma"
Approved → Allowed
Everything else → Not trusted / blocked
```

Its purpose is to prevent malware and unauthorized software from executing.

The material recommends initially deploying whitelisting in **audit mode** to identify legitimate applications that would otherwise be accidentally blocked.

---

## Whitelisting vs. Blacklisting

| Whitelisting                             | Blacklisting               |
| ---------------------------------------- | -------------------------- |
| Defines what is allowed                  | Defines what is blocked    |
| Everything else is considered unapproved | Everything else is allowed |
| Allow-based model                        | Block-based model          |

Whitelisting therefore follows a more restrictive model in which software must be explicitly approved.

---

# AppLocker

**AppLocker** is Microsoft's application whitelisting solution.

It allows administrators to control which applications and files users can execute.

AppLocker can create rules for:

* Executables
* Scripts
* Windows Installer files
* DLLs
* Packaged applications
* Packaged application installers

Rules can be based on properties such as:

```text id="ft2s31"
Publisher
Product name
File name
Version
Path
Hash
```

Rules can also target:

* Individual users
* Security groups

Like general application whitelisting, AppLocker can first be deployed in **audit mode** before rules are enforced.

---

# Local Group Policy

**Group Policy** allows administrators to configure and enforce Windows settings.

In an Active Directory environment, policies can be distributed from a Domain Controller through **Group Policy Objects (GPOs)**.

Individual Windows machines can also have their own **Local Group Policy**.

The Local Group Policy Editor can be opened with:

```cmd id="q9c0js"
gpedit.msc
```

The editor contains two main categories:

```text id="w5n3kt"
Computer Configuration
User Configuration
```

---

# Security Uses of Local Group Policy

Local Group Policy can be used to enforce security settings such as:

* Password requirements
* Application restrictions
* Auditing configuration
* AppLocker rules
* Network settings
* System security settings

The material also provides **Credential Guard** as an example.

Credential Guard can be configured through the policy:

```text id="0zh4xs"
Turn On Virtualization Based Security
```

Credential Guard helps protect against credential theft by isolating the operating system's **LSA process**.

---

# Windows Defender Antivirus

**Windows Defender Antivirus** is Microsoft's built-in antivirus solution included with Windows.

The material highlights several Defender capabilities:

* Real-time protection
* Cloud-delivered protection
* Automatic sample submission
* Tamper Protection
* Controlled Folder Access
* Exclusions

---

## Real-Time and Cloud Protection

**Real-time protection** monitors the system for known threats as activity occurs.

**Cloud-delivered protection** can work with automatic sample submission to analyze suspicious files.

These mechanisms allow Defender to react to suspicious or known malicious content.

---

# Tamper Protection

**Tamper Protection** helps prevent Windows security settings from being modified through mechanisms such as:

* Registry changes
* PowerShell cmdlets
* Group Policy

This helps prevent software or attackers from simply disabling certain security protections.

---

# Controlled Folder Access

**Controlled Folder Access** is Defender's built-in ransomware protection mechanism.

It can protect selected files, folders, and memory areas against unauthorized modifications.

Defender can also maintain **exclusions**, which prevent specified files or folders from being scanned.

The material gives penetration-testing tool directories as an example because security tools may otherwise be detected and quarantined.

---

# Checking Defender Status with PowerShell

We can inspect Defender protection status with:

```powershell id="76ofl1"
Get-MpComputerStatus
```

The material filters the result for enabled properties using:

```powershell id="s3r8yu"
PS C:\htb> Get-MpComputerStatus | findstr "True"
```

Example output:

```powershell id="r5v1qh"
AMServiceEnabled                : True
AntispywareEnabled              : True
AntivirusEnabled                : True
BehaviorMonitorEnabled          : True
IoavProtectionEnabled           : True
IsTamperProtected               : True
NISEnabled                      : True
OnAccessProtectionEnabled       : True
RealTimeProtectionEnabled       : True
```

This gives us a quick way to identify which Defender protection mechanisms are currently enabled.

---

# Defense in Depth

Windows Defender should not be treated as the only security mechanism protecting a Windows system.

The material emphasizes a **defense-in-depth** approach involving multiple security layers, including:

* Secure configuration
* Patch management
* Access control
* Application control
* Antivirus protection
* Monitoring

Defender can detect payloads from common security frameworks and unmodified tools such as Metasploit and Mimikatz, but antivirus protection alone is not sufficient to secure a Windows environment.

---

# Quick Reference

### Current User SID

```powershell id="n7v3ar"
whoami /user
```

### Registry Editor

```cmd id="k8y5eq"
regedit
```

### System Registry Files

```text id="1q5t0b"
C:\Windows\System32\Config\
```

### Current User Registry File

```text id="u2fb4z"
C:\Users\<USERNAME>\NTUSER.DAT
```

### Important Startup Keys

```text id="39m4da"
HKLM\Software\Microsoft\Windows\CurrentVersion\Run
HKCU\Software\Microsoft\Windows\CurrentVersion\Run
HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnce
HKCU\Software\Microsoft\Windows\CurrentVersion\RunOnce
```

### Query a Registry Key

```cmd id="k4sf3w"
reg query <key>
```

### Local Group Policy

```cmd id="z9p6ma"
gpedit.msc
```

### Check Defender Status

```powershell id="42mvbe"
Get-MpComputerStatus
```

### Core Security Concepts

| Concept          | Purpose                                           |
| ---------------- | ------------------------------------------------- |
| **SID**          | Uniquely identifies a security principal          |
| **RID**          | Distinguishes an account within its SID authority |
| **ACL**          | Collection of access-control entries              |
| **ACE**          | Defines permissions for a principal               |
| **DACL**         | Controls allowed/denied access                    |
| **SACL**         | Controls auditing                                 |
| **UAC**          | Controls elevation of privileged actions          |
| **AppLocker**    | Controls which applications can execute           |
| **Group Policy** | Configures and enforces Windows settings          |
| **Defender**     | Built-in Windows antimalware protection           |

---

## Key Takeaway

**Windows security is built from multiple layers rather than a single protection mechanism. SIDs and access tokens identify security principals, ACLs and ACEs control access, UAC controls privileged elevation, the Registry stores critical configuration, AppLocker and Group Policy restrict system behavior, and Windows Defender provides antimalware protection. Understanding how these mechanisms interact is fundamental for Windows administration, defensive security, and later privilege-escalation concepts.**
