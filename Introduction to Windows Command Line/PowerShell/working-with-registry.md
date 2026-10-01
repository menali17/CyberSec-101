
# Working with the Registry

---

## Overview

The **Windows Registry** is a hierarchical database that stores configuration information for the operating system, installed applications, hardware, and user preferences.

Understanding the Registry allows us to examine system configurations, identify installed software, investigate security settings, and detect potentially suspicious modifications.

In this section, we will learn how to:

- Understand registry keys, values, and hives.
- Navigate the Registry using PowerShell.
- Query registry entries with `Get-Item`, `Get-ItemProperty`, and `reg.exe`.
- Search the Registry recursively.
- Create, modify, and delete registry keys and values.
- Understand how registry modifications can be abused for persistence.

---

## 1. Understanding the Windows Registry

The Windows Registry is organized as a hierarchical tree containing **keys** and **values**.

Its structure is similar to a filesystem: keys resemble directories, while values represent configuration data stored within those directories.

### Registry Keys

A registry key is a container that can hold other keys, called *subkeys*, and configuration values.

For example:

```text
HKEY_LOCAL_MACHINE
└── SOFTWARE
    └── Microsoft
        └── Windows
            └── CurrentVersion
                └── Run
```

Each component of this path is a registry key.

### Registry Values

Registry values contain the actual configuration data associated with a key.

Each value has three main components:

| Component | Description |
|---|---|
| Name | Identifies the registry value. |
| Type | Defines the format of the stored data. |
| Data | Contains the actual configuration information. |

For example, the `7-Zip` registry key may contain:

```text
HKEY_LOCAL_MACHINE\SOFTWARE\7-Zip

Name       Type      Data
----       ----      ----
Path64     REG_SZ    C:\Program Files\7-Zip\
Path       REG_SZ    C:\Program Files\7-Zip\
```

In this example, `Path64` and `Path` are values, `REG_SZ` is their data type, and the installation directory is their data.

### Common Registry Value Types

| Type | Description |
|---|---|
| `REG_SZ` | A text string. |
| `REG_EXPAND_SZ` | A string that can contain environment variables. |
| `REG_DWORD` | A 32-bit integer. |
| `REG_QWORD` | A 64-bit integer. |
| `REG_BINARY` | Binary data. |
| `REG_MULTI_SZ` | Multiple strings. |

---

## 2. Registry Hives

The Registry contains several predefined root keys known as **hives**.

Each hive organizes information associated with a particular aspect of the operating system.

| Hive | Abbreviation | Description |
|---|---|---|
| `HKEY_LOCAL_MACHINE` | `HKLM` | Computer-wide configuration, including operating system, hardware, drivers, and installed software. |
| `HKEY_CURRENT_CONFIG` | `HKCC` | Information about the current hardware configuration. |
| `HKEY_CLASSES_ROOT` | `HKCR` | File associations, registered applications, and COM-related configuration. |
| `HKEY_CURRENT_USER` | `HKCU` | Configuration and preferences associated with the current user. |
| `HKEY_USERS` | `HKU` | Configuration associated with loaded user profiles. |

### HKLM vs. HKCU

The distinction between `HKLM` and `HKCU` is particularly important.

**HKLM** stores computer-wide settings, while **HKCU** contains settings associated with the current user.

For example, an application might store its installation directory under `HKLM` while keeping an individual user's preferences under `HKCU`.

### Registry Hive Files

Many important registry hives are backed by files stored in:

```text
C:\Windows\System32\Config\
```

We can list this directory using:

```powershell
Get-ChildItem C:\Windows\System32\Config\
```

Important files include:

| File | Purpose |
|---|---|
| `SYSTEM` | System and hardware configuration. |
| `SOFTWARE` | Installed software and operating system configuration. |
| `SAM` | Local account database. |
| `SECURITY` | Local security policy and related protected information. |
| `DEFAULT` | Default system-profile settings. |

Other registry data, particularly user-specific configuration, is stored elsewhere on the system.

**Security consideration:** Some registry hive files contain highly sensitive information and are protected by Windows access controls.

---

## 3. Accessing the Registry with PowerShell

PowerShell provides a Registry provider that allows us to interact with registry keys using commands similar to those used for files and directories.

The HTB module introduces three primary cmdlets:

| Cmdlet | Purpose |
|---|---|
| `Get-Item` | Retrieves a registry key. |
| `Get-ChildItem` | Enumerates subkeys. |
| `Get-ItemProperty` | Retrieves values stored within a registry key. |

We can access registry locations through paths such as:

```powershell
HKLM:\SOFTWARE
HKCU:\SOFTWARE
```

Alternatively, we can use the full registry provider path:

```powershell
Registry::HKEY_LOCAL_MACHINE\SOFTWARE
```

### Get-Item

The `Get-Item` cmdlet retrieves information about a particular key.

For example, the HTB module examines the Windows `Run` key:

```powershell
Get-Item -Path Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Run |
    Select-Object -ExpandProperty Property
```

This displays the names of the values stored directly inside the specified key.

The `Run` key is significant because its entries can specify applications that Windows launches when a user logs in.

### Get-ChildItem

We can enumerate subkeys using `Get-ChildItem`.

To search through an entire registry subtree, we can use `-Recurse`:

```powershell
Get-ChildItem -Path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion -Recurse
```

This recursively enumerates accessible subkeys beneath the specified path.

**Important:** Recursive registry searches may produce substantial output. We should narrow our search when we already know the relevant configuration path.

### Get-ItemProperty

While `Get-Item` retrieves a registry key, `Get-ItemProperty` retrieves the values stored inside it.

For example:

```powershell
Get-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
```

The output may include value names and executable paths associated with applications configured to start at logon.

This makes `Get-ItemProperty` particularly useful when we want to inspect the actual configuration data rather than simply list key or value names.

---

## 4. Querying the Registry with Reg.exe

Windows also includes `reg.exe`, a command-line utility specifically designed to manage the Registry.

Unlike the PowerShell registry cmdlets, `reg.exe` is an external Windows executable.

We can use it from both CMD and PowerShell.

### Querying a Specific Key

For example, we can retrieve the values stored under the 7-Zip configuration key:

```powershell
reg query "HKEY_LOCAL_MACHINE\SOFTWARE\7-Zip"
```

Example output:

```text
HKEY_LOCAL_MACHINE\SOFTWARE\7-Zip
    Path64    REG_SZ    C:\Program Files\7-Zip\
    Path      REG_SZ    C:\Program Files\7-Zip\
```

The output displays each value's name, data type, and stored data.

---

## 5. Searching the Registry

The `reg query` command also supports searching for particular strings, value types, and key names.

The HTB material demonstrates the following example:

```powershell
reg query HKCU /F "Password" /T REG_SZ /S /K
```

### Understanding the Parameters

| Parameter | Description |
|---|---|
| `query` | Queries the Registry. |
| `HKCU` | Specifies the registry subtree to search. |
| `/F "Password"` | Searches for the specified string. |
| `/T REG_SZ` | Restricts the search to the specified value type. |
| `/S` | Searches subkeys recursively. |
| `/K` | Searches registry key names. |

This example searches for registry keys associated with the word `Password`.

Depending on our objective, we can also search for terms such as `Username` and `Credentials`.

### Security Relevance

Registry searches can reveal configuration information associated with installed software, user preferences, authentication-related settings, and security applications.

However, a matching registry entry does not necessarily contain an actual credential. We need to inspect its context before drawing conclusions.

---

## 6. Creating Registry Keys and Values

PowerShell provides several cmdlets for creating and modifying registry entries.

| Cmdlet | Description |
|---|---|
| `New-Item` | Creates a new registry key. |
| `New-ItemProperty` | Creates a value inside a registry key. |
| `Set-ItemProperty` | Modifies an existing value. |
| `Remove-ItemProperty` | Deletes a registry value. |
| `Remove-Item` | Deletes a registry key. |

### Creating a Registry Key

In a laboratory environment, we can create a test key under our current user's registry subtree:

```powershell
New-Item -Path HKCU:\SOFTWARE -Name HTB-Lab
```

This creates:

```text
HKEY_CURRENT_USER
└── SOFTWARE
    └── HTB-Lab
```

### Creating a Registry Value

After creating the key, we can add a value using `New-ItemProperty`:

```powershell
New-ItemProperty `
    -Path HKCU:\SOFTWARE\HTB-Lab `
    -Name "TestValue" `
    -PropertyType String `
    -Value "Hello from PowerShell"
```

The resulting configuration contains a value named `TestValue`, of type `REG_SZ`, with the specified text.

### Reading the New Value

We can verify that our value was created:

```powershell
Get-ItemProperty -Path HKCU:\SOFTWARE\HTB-Lab
```

### Modifying a Value

To update an existing value, we can use `Set-ItemProperty`:

```powershell
Set-ItemProperty `
    -Path HKCU:\SOFTWARE\HTB-Lab `
    -Name "TestValue" `
    -Value "Updated value"
```

This modifies the existing value without creating another key.

### Deleting a Value

We can delete a registry value using:

```powershell
Remove-ItemProperty `
    -Path HKCU:\SOFTWARE\HTB-Lab `
    -Name "TestValue"
```

Notice that `Remove-ItemProperty` deletes the specified value, not its parent registry key.

To remove our test key afterward:

```powershell
Remove-Item -Path HKCU:\SOFTWARE\HTB-Lab
```

---

## 7. Registry Persistence: Run and RunOnce

The HTB module introduces registry-based persistence through Windows logon startup entries.

Two particularly relevant registry keys are:

```text
HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run

HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce
```

### Run

The `Run` key contains entries that configure applications to launch when a user logs in.

Because the entries can remain registered between logons, this location is frequently examined during persistence investigations.

### RunOnce

The `RunOnce` key serves a similar purpose, but its entries are intended to execute only once.

An administrator might use it to complete a software installation after the next logon.

However, an attacker could also abuse this mechanism to launch an unauthorized executable.

The HTB module demonstrates creating registry entries associated with `RunOnce` and discusses how this functionality could be used to regain access to a compromised machine.

**Technical distinction:** Windows normally processes executable entries stored as values directly beneath the appropriate `RunOnce` key. Creating a nested subkey, as shown in the module's example, does not by itself establish a functional RunOnce startup entry.

### Defensive Considerations

During an authorized security assessment, we can inspect these locations to identify unexpected startup entries.

For example:

```powershell
Get-ItemProperty -Path HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
```

And:

```powershell
Get-ItemProperty -Path HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce
```

We should investigate unexpected executables, unusual file paths, and entries that cannot be associated with legitimate applications.

---

## 8. Registry Modification with Reg.exe

The `reg.exe` utility can also create, modify, and delete registry entries.

The HTB material introduces the `reg add` command, which provides an alternative to PowerShell's registry modification cmdlets.

Its general syntax is:

```cmd
reg add <key> /v <value-name> /t <type> /d <data>
```

| Parameter | Description |
|---|---|
| `add` | Creates or modifies a registry entry. |
| `/v` | Specifies the value name. |
| `/t` | Specifies the value type. |
| `/d` | Specifies the data to store. |

For example, we can add a value to our laboratory key:

```powershell
reg add "HKCU\SOFTWARE\HTB-Lab" /v TestValue /t REG_SZ /d "Example data"
```

This performs a similar operation to `New-ItemProperty`.

**Important:** Registry modifications can affect application behavior, security settings, and system stability. We should inspect existing values and document any changes before modifying important keys.

---

## Command Summary

| Command | Purpose |
|---|---|
| `Get-Item` | Retrieves a registry key. |
| `Get-ChildItem` | Enumerates registry subkeys. |
| `Get-ChildItem -Recurse` | Recursively enumerates registry subkeys. |
| `Get-ItemProperty` | Retrieves values stored inside a registry key. |
| `New-Item` | Creates a registry key. |
| `New-ItemProperty` | Creates a registry value. |
| `Set-ItemProperty` | Modifies a registry value. |
| `Remove-ItemProperty` | Deletes a registry value. |
| `Remove-Item` | Deletes a registry key. |
| `reg query` | Queries registry entries. |
| `reg add` | Creates or modifies registry entries. |

---

## Key Takeaways

- The Windows Registry is a hierarchical database containing keys and values.
- Keys act as containers, while values store configuration information.
- Registry hives organize settings related to the computer, users, hardware, and applications.
- `HKLM` contains computer-wide configuration, while `HKCU` represents the current user's settings.
- PowerShell's Registry provider allows us to navigate and manage registry entries using familiar cmdlets.
- `Get-ItemProperty` retrieves configuration values, while `Get-ChildItem -Recurse` enumerates subkeys.
- `reg.exe` provides an alternative command-line interface for searching and modifying the Registry.
- `Run` and `RunOnce` are important locations to inspect during investigations of logon-based persistence.
- Registry modifications should be performed carefully because incorrect changes may affect system functionality and security.
