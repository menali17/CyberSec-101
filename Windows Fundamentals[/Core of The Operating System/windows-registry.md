# Windows Registry

The **Windows Registry** is a hierarchical database used by Windows and many installed applications to store configuration information.

The Registry contains settings related to:

* User profiles
* Installed software
* Hardware
* Services
* Security policies
* The operating system itself

For example, Windows may consult Registry settings to determine:

* Which applications execute when a user logs in.
* How a service starts.
* Whether a security feature is enabled.

System administrators frequently interact with the Registry when configuring Windows systems, deploying software, and troubleshooting problems.

Because the Registry contains important system configuration, modifying the wrong value can cause applications or Windows components to stop working.

We should therefore understand a Registry setting before modifying it and preferably test changes in a lab environment first.

---

# Registry Structure

The Windows Registry is organized into four main components:

```text
Hive
 ↓
Key
 ↓
Subkey
 ↓
Value
```

These components form a hierarchy similar to directories and files in a filesystem.

| Component  | Description                                                  |
| ---------- | ------------------------------------------------------------ |
| **Hive**   | Top-level section containing a category of Registry settings |
| **Key**    | Container inside a hive, similar to a directory              |
| **Subkey** | Key located underneath another key                           |
| **Value**  | Individual configuration setting stored inside a key         |

A useful analogy is:

```text
Windows File System          Windows Registry

Drive / Root                 Hive
     │                        │
     └── Folder              └── Key
          │                       │
          └── Subfolder           └── Subkey
                                      │
                                      └── Value
```

---

# Registry Paths

Consider the Registry path:

```text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion
```

We can break it down into:

```text
HKEY_LOCAL_MACHINE
        │
        └── SOFTWARE
              │
              └── Microsoft
                    │
                    └── Windows
                          │
                          └── CurrentVersion
```

In this example:

```text
HKEY_LOCAL_MACHINE → Hive
SOFTWARE           → Key
Microsoft          → Subkey
Windows            → Subkey
CurrentVersion     → Subkey
```

Selecting `CurrentVersion` in Registry Editor displays the values contained inside that key.

Registry paths therefore resemble Windows filesystem paths:

```text
C:\Users\htb-student\Documents
```

versus:

```text
HKCU\Software\Microsoft\Windows
```

Both represent hierarchical structures, but they refer to completely different systems.

---

# Registry Values

Registry **values** contain the actual configuration information.

Each value contains three primary components:

| Component | Purpose                                       |
| --------- | --------------------------------------------- |
| `Name`    | Identifies the setting                        |
| `Type`    | Determines what kind of data is stored        |
| `Data`    | Contains the actual configuration information |

For example:

```text
Name: EnableLUA
Type: REG_DWORD
Data: 1
```

can be visualized as:

```text
EnableLUA
    │
    ├── Name → EnableLUA
    │
    ├── Type → REG_DWORD
    │
    └── Data → 1
```

---

# Primary Registry Hives

Windows contains several primary Registry hives.

| Hive                  | Abbreviation | Purpose                                         |
| --------------------- | ------------ | ----------------------------------------------- |
| `HKEY_CURRENT_USER`   | `HKCU`       | Settings for the currently logged-on user       |
| `HKEY_LOCAL_MACHINE`  | `HKLM`       | System-wide hardware, software, and OS settings |
| `HKEY_CLASSES_ROOT`   | `HKCR`       | File associations and application registration  |
| `HKEY_USERS`          | `HKU`        | User profiles currently loaded                  |
| `HKEY_CURRENT_CONFIG` | `HKCC`       | Current hardware configuration                  |

---

# `HKEY_CURRENT_USER` — HKCU

```text
HKEY_CURRENT_USER
```

or:

```text
HKCU
```

contains settings associated with the **currently logged-on user**.

Conceptually:

```text
HKCU
 │
 └── Current User
       │
       ├── User preferences
       ├── Application configuration
       └── Other user-specific settings
```

Changes under `HKCU` generally affect only that user.

---

# `HKEY_LOCAL_MACHINE` — HKLM

```text
HKEY_LOCAL_MACHINE
```

or:

```text
HKLM
```

contains configuration affecting the **entire computer**.

It includes system-wide information related to:

```text
Hardware
Software
Operating System
Services
Security configuration
```

Because these settings can affect the whole machine, modifying many locations under `HKLM` requires administrative privileges.

The basic distinction is:

```text
HKCU
 ↓
Current User


HKLM
 ↓
Entire Machine
```

These are two of the Registry hives we will interact with most often.

---

# `HKEY_CLASSES_ROOT` — HKCR

```text
HKCR
```

contains information related to:

```text
File associations
Application registration
```

For example, Windows needs to know which applications are associated with particular file types.

---

# `HKEY_USERS` — HKU

```text
HKU
```

contains user profiles currently loaded on the system.

Therefore:

```text
HKCU
 ↓
Current user


HKU
 ↓
Loaded user profiles
```

---

# `HKEY_CURRENT_CONFIG` — HKCC

```text
HKCC
```

contains information about the system's **current hardware configuration**.

---

# Registry Data Types

Registry values can contain different types of data.

Some common types are:

| Type           | Description           |
| -------------- | --------------------- |
| `REG_SZ`       | Standard text string  |
| `REG_DWORD`    | 32-bit number         |
| `REG_QWORD`    | 64-bit number         |
| `REG_MULTI_SZ` | Multiple text strings |
| `REG_BINARY`   | Raw binary data       |

---

# `REG_SZ`

```text
REG_SZ
```

stores a normal text string.

For example:

```text
CourseName
    │
    ├── Type → REG_SZ
    └── Data → Windows Fundamentals
```

---

# `REG_DWORD`

```text
REG_DWORD
```

stores a **32-bit number**.

It is commonly used for settings that can be represented numerically, including enabled/disabled configuration.

For example:

```text
EnableLUA
    │
    └── REG_DWORD
          │
          ├── 1 → Enabled
          └── 0 → Disabled
```

---

# `REG_QWORD`

```text
REG_QWORD
```

stores a **64-bit number**.

The main difference from `REG_DWORD` is the size of the numeric value it can represent.

---

# `REG_MULTI_SZ`

```text
REG_MULTI_SZ
```

stores multiple strings within one Registry value.

---

# `REG_BINARY`

```text
REG_BINARY
```

stores raw binary data.

---

# Registry Editor

Windows includes a graphical Registry management utility called:

```cmd
regedit.exe
```

or simply:

```cmd
regedit
```

We can launch it through the Start menu or by pressing:

```text
Win + R
```

and entering:

```cmd
regedit
```

---

# Registry Editor Interface

Registry Editor contains two main panes.

```text
Registry Editor
      │
      ├── Left Pane
      │      ↓
      │   Registry hierarchy
      │
      └── Right Pane
             ↓
          Values
```

The left side displays:

```text
Hives
 ↓
Keys
 ↓
Subkeys
```

The right side displays the values stored in the selected key.

The address bar displays the complete path of the currently selected key.

---

# Creating a Practice Registry Key

The material creates a practice key inside:

```text
HKEY_CURRENT_USER\Software
```

Because this is under:

```text
HKCU
```

we are working with the current user's Registry configuration rather than a system-wide `HKLM` location.

A new key is created named:

```text
HTB-Academy
```

which produces:

```text
HKEY_CURRENT_USER
       │
       └── Software
              │
              └── HTB-Academy
```

---

# Creating a String Value

Inside:

```text
HKCU\Software\HTB-Academy
```

the material creates a new **String Value** named:

```text
CourseName
```

with the data:

```text
Windows Fundamentals
```

Therefore:

```text
HTB-Academy
     │
     └── CourseName
             │
             ├── Type → REG_SZ
             │
             └── Data → Windows Fundamentals
```

---

# Creating a DWORD Value

A second value is created:

```text
LabComplete
```

using:

```text
DWORD (32-bit)
```

and its value is set to:

```text
1
```

Therefore:

```text
HTB-Academy
     │
     ├── CourseName
     │      └── REG_SZ → Windows Fundamentals
     │
     └── LabComplete
            └── REG_DWORD → 1
```

Values can later be modified by double-clicking them.

Keys and values can also be deleted, but Registry Editor does **not** determine whether Windows or another application depends on the selected data before deletion.

---

# Interacting with the Registry Using `reg.exe`

Windows also includes the command-line utility:

```cmd
reg.exe
```

It allows us to query and modify the Registry from:

```text
Windows terminals
Scripts
Remote administration sessions
```

To display its help:

```cmd
C:\htb> reg /?

REG Operation [Parameter List]

  Operation  [ QUERY   | ADD    | DELETE  | COPY    |
               SAVE    | LOAD   | UNLOAD  | RESTORE |
               COMPARE | EXPORT | IMPORT  | FLAGS ]

Return Code: (Except for REG COMPARE)

  0 - Successful
  1 - Failed
```

Some important operations are:

```text
QUERY
ADD
DELETE
COPY
SAVE
LOAD
RESTORE
EXPORT
IMPORT
```

For this section, the most relevant are:

```text
query
add
delete
export
import
```

---

# Getting Help for a Registry Operation

We can request help for a specific operation.

For example:

```cmd
reg query /?
```

The general pattern is:

```cmd
reg <operation> /?
```

Examples:

```cmd
reg query /?
reg add /?
reg delete /?
```

---

# `reg query`

The:

```cmd
reg query
```

command allows us to retrieve Registry information.

Its general structure includes:

```cmd
reg query <KeyName>
```

Some useful parameters introduced by the material are:

| Parameter | Purpose                              |
| --------- | ------------------------------------ |
| `/v`      | Query a specific value               |
| `/ve`     | Query the default value              |
| `/s`      | Recursively query subkeys and values |
| `/f`      | Search for data/pattern              |
| `/k`      | Search key names                     |
| `/d`      | Search data                          |
| `/c`      | Case-sensitive search                |
| `/e`      | Exact match                          |
| `/t`      | Filter by Registry data type         |
| `/reg:32` | Use 32-bit Registry view             |
| `/reg:64` | Use 64-bit Registry view             |

---

# Recursive Registry Queries

The:

```cmd
/s
```

option recursively queries all subkeys and values.

The material compares this to:

```cmd
dir /s
```

Conceptually:

```text
reg query <key> /s
          │
          ▼
        Key
         │
         ├── Values
         │
         ├── Subkey
         │     ├── Values
         │     └── Subkey
         │
         └── Subkey
               └── Values
```

This is useful when we need to examine an entire Registry hierarchy rather than only one key.

---

# Querying Registry Data

The practice key can be queried using:

```cmd
C:\htb> reg query "HKCU\Software\HTB-Academy"
```

Output:

```cmd
HKEY_CURRENT_USER\Software\HTB-Academy
    CourseName      REG_SZ       Windows Fundamentals
    LabComplete     REG_DWORD    0x1
```

This output reveals:

```text
Value Name        Type          Data

CourseName        REG_SZ        Windows Fundamentals
LabComplete       REG_DWORD     0x1
```

---

# Querying a Specific Value

To retrieve only one value, we can use:

```cmd
/v
```

For example:

```cmd
C:\htb> reg query "HKCU\Software\HTB-Academy" /v CourseName
```

Output:

```cmd
HKEY_CURRENT_USER\Software\HTB-Academy
    CourseName    REG_SZ    Windows Fundamentals
```

The command can be interpreted as:

```text
reg query
    │
    ├── HKCU\Software\HTB-Academy
    │
    └── /v CourseName
             ↓
      Query this value
```

---

# Adding and Modifying Registry Values

The:

```cmd
reg add
```

command can:

```text
Create a key
Create a value
Modify an existing value
```

Important parameters are:

| Parameter | Purpose                                |
| --------- | -------------------------------------- |
| `/v`      | Value name                             |
| `/t`      | Value type                             |
| `/d`      | Data to store                          |
| `/f`      | Perform operation without confirmation |

---

# Creating a Value with `reg add`

The material creates:

```text
CreatedBy
```

using:

```cmd
C:\htb> reg add "HKCU\Software\HTB-Academy" /v CreatedBy /t REG_SZ /d "reg.exe" /f

The operation completed successfully.
```

Breaking it down:

```text
reg add
   │
   ├── "HKCU\Software\HTB-Academy"
   │           ↓
   │        Target key
   │
   ├── /v CreatedBy
   │           ↓
   │       Value name
   │
   ├── /t REG_SZ
   │           ↓
   │       Value type
   │
   ├── /d "reg.exe"
   │           ↓
   │          Data
   │
   └── /f
           ↓
      No confirmation
```

The result is:

```text
CreatedBy
    │
    ├── REG_SZ
    └── reg.exe
```

---

# Modifying an Existing Value

`reg add` can also modify an existing value.

The material changes:

```text
LabComplete
```

from:

```text
1
```

to:

```text
0
```

using:

```cmd
C:\htb> reg add "HKCU\Software\HTB-Academy" /v LabComplete /t REG_DWORD /d 0 /f

The operation completed successfully.
```

Afterward:

```cmd
C:\htb> reg query "HKCU\Software\HTB-Academy"

HKEY_CURRENT_USER\Software\HTB-Academy
    CourseName     REG_SZ       Windows Fundamentals
    LabComplete    REG_DWORD    0x0
    CreatedBy      REG_SZ       reg.exe
```

---

# Deleting Registry Data

The:

```cmd
reg delete
```

command can remove:

```text
Individual values
Entire keys
```

---

# Deleting a Value

To remove only the:

```text
CreatedBy
```

value:

```cmd
C:\htb> reg delete "HKCU\Software\HTB-Academy" /v CreatedBy /f

The operation completed successfully.
```

Because `/v CreatedBy` is specified, only that value is removed.

---

# Deleting an Entire Key

To delete the entire practice key:

```cmd
C:\htb> reg delete "HKCU\Software\HTB-Academy" /f

The operation completed successfully.
```

This removes:

```text
HTB-Academy
    │
    ├── CourseName
    ├── LabComplete
    └── Any other values/subkeys
```

We should carefully review the Registry path before using `reg delete`, especially when `/f` is used because confirmation is skipped.

---

# Registry Permissions

Registry keys have permissions controlling which users and groups can read or modify them.

A standard user can generally modify appropriate areas under:

```text
HKCU
```

because they correspond to that user's configuration.

System-wide keys under:

```text
HKLM
```

are more restricted and commonly require administrative privileges.

Without sufficient permissions, Windows may return:

```text
Access is denied
```

or:

```text
The requested operation requires elevation
```

This gives us another useful distinction:

```text
HKCU
 ↓
Usually user-level configuration


HKLM
 ↓
System-wide configuration
 ↓
Often requires elevation
```

---

# User Account Control and the Registry

**User Account Control (UAC)** helps prevent applications from making administrative changes without approval.

Windows stores its primary UAC policy values under:

```text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System
```

Notice that this path begins with:

```text
HKEY_LOCAL_MACHINE
```

which makes sense because UAC is a **system-wide security configuration**.

---

# `EnableLUA`

One value inside the UAC configuration is:

```text
EnableLUA
```

It has the type:

```text
REG_DWORD
```

and its values mean:

```text
1 → UAC enabled
0 → UAC disabled
```

We can query it without modifying anything:

```cmd
C:\htb> reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v EnableLUA
```

Output:

```cmd
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System
    EnableLUA    REG_DWORD    0x1
```

Here:

```text
0x1
 ↓
1
 ↓
UAC enabled
```

Changing this setting requires administrative privileges and normally requires a restart before the full change takes effect.

---

# Why the Registry Matters

The Registry influences many aspects of Windows behavior.

```text
Windows Registry
      │
      ├── User Profiles
      ├── Software
      ├── Hardware
      ├── Services
      ├── Security Policies
      └── Operating System
```

For example:

```text
User logs in
     │
     ▼
Windows checks configuration
     │
     ▼
Registry
     │
     └── Applications configured to run
```

or:

```text
Windows starts a service
        │
        ▼
Registry configuration
        │
        ▼
How should the service start?
```

This is why understanding the Registry is useful for both Windows administration and security.

---

# GUI vs. Command Line

We now have two primary ways to interact with the Registry:

```text
Registry
   │
   ├── GUI
   │    ↓
   │  regedit.exe
   │
   └── CLI
        ↓
      reg.exe
```

`regedit.exe` is useful for visually navigating and modifying Registry keys.

`reg.exe` is useful when working from terminals, scripts, or remote administration sessions.

---

# Core Mental Model

```text
Windows Registry
       │
       ├── Hive
       │    │
       │    └── Key
       │         │
       │         └── Subkey
       │              │
       │              └── Value
       │                    │
       │                    ├── Name
       │                    ├── Type
       │                    └── Data
       │
       ├── HKCU
       │     ↓
       │   Current User
       │
       └── HKLM
             ↓
         Entire Machine
```

To interact with it:

```text
Registry
   │
   ├── regedit.exe
   │      ↓
   │     GUI
   │
   └── reg.exe
          │
          ├── query
          ├── add
          └── delete
```

---

# Quick Reference

### Open Registry Editor

```cmd
regedit
```

### Display `reg.exe` help

```cmd
reg /?
```

### Query a Registry key

```cmd
reg query "HKCU\Software\HTB-Academy"
```

### Query a specific value

```cmd
reg query "HKCU\Software\HTB-Academy" /v CourseName
```

### Query recursively

```cmd
reg query <key> /s
```

### Add a string value

```cmd
reg add <key> /v <name> /t REG_SZ /d <data> /f
```

### Add a DWORD value

```cmd
reg add <key> /v <name> /t REG_DWORD /d <number> /f
```

### Delete a value

```cmd
reg delete <key> /v <value> /f
```

### Delete an entire key

```cmd
reg delete <key> /f
```

### Main Registry Hives

```text
HKCU → HKEY_CURRENT_USER
HKLM → HKEY_LOCAL_MACHINE
HKCR → HKEY_CLASSES_ROOT
HKU  → HKEY_USERS
HKCC → HKEY_CURRENT_CONFIG
```

### Common Data Types

```text
REG_SZ       → Text
REG_DWORD    → 32-bit number
REG_QWORD    → 64-bit number
REG_MULTI_SZ → Multiple strings
REG_BINARY   → Binary data
```

### UAC Registry Path

```text
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System
```

### Check UAC

```cmd
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v EnableLUA
```

```text
EnableLUA = 1 → UAC enabled
EnableLUA = 0 → UAC disabled
```

---

## Key Takeaway

**The Windows Registry is a hierarchical database that stores configuration for users, applications, hardware, services, security policies, and Windows itself. Its structure follows `Hive → Key → Subkey → Value`, with each value containing a name, type, and data. `HKCU` primarily contains settings for the current user, while `HKLM` contains system-wide configuration. We can interact with the Registry graphically through `regedit.exe` or from the command line with `reg.exe`, using operations such as `query`, `add`, and `delete`. Because Registry settings can directly affect Windows behavior and security, modifications should be made carefully.**
