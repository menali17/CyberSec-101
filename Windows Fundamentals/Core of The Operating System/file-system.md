# File System

Windows supports several file systems. The main ones introduced in this section are:

```text
FAT12
FAT16
FAT32
NTFS
exFAT
```

`FAT12` and `FAT16` are no longer commonly used by modern Windows systems. This section focuses primarily on **FAT32** and **NTFS**, especially NTFS.

---

# FAT32

**FAT32 (File Allocation Table 32)** is commonly used on storage devices such as:

```text
USB drives
SD cards
External storage
```

The `32` refers to the use of **32 bits to identify data clusters** on the storage device.

---

# FAT32 Advantages

One of FAT32's biggest advantages is **compatibility**.

It is supported by many types of devices and operating systems:

```text
Windows
Linux
macOS
Digital cameras
Gaming consoles
Smartphones
Tablets
```

This makes FAT32 useful when a storage device needs to work across many different systems.

---

# FAT32 Disadvantages

FAT32 also has important limitations.

Most notably:

```text
Maximum individual file size
        ↓
       4 GB
```

It also lacks built-in features for:

```text
File permissions / protection
Compression
Encryption
```

So the basic idea is:

```text
FAT32
   │
   ├── Excellent compatibility
   │
   └── Limited features and file size
```

---

# NTFS

**NTFS (New Technology File System)** has been the default Windows file system since Windows NT 3.1.

It improves on many FAT32 limitations and provides better:

* Performance.
* Metadata support.
* Reliability.
* Security.
* Support for large partitions.

For Windows security, NTFS is particularly important because it supports **granular permissions on files and directories**.

---

# NTFS Advantages

NTFS provides several important features.

### Reliability

NTFS can restore filesystem consistency after events such as:

```text
System failure
Power loss
```

### Permissions

NTFS allows permissions to be assigned to:

```text
Files
Folders
```

This lets Windows control what different users and groups can do with filesystem objects.

### Large Partitions

NTFS supports very large partitions.

### Journaling

NTFS includes **journaling**.

File modifications such as:

```text
Creation
Modification
Deletion
```

are logged by the filesystem.

---

# NTFS Disadvantages

The main disadvantages discussed in the material involve compatibility.

NTFS is not natively supported by many:

```text
Mobile devices
Older televisions
Digital cameras
Other media devices
```

Therefore:

```text
FAT32
   ↓
Compatibility


NTFS
   ↓
Features + Security + Reliability
```

---

# FAT32 vs NTFS

| Feature                          | FAT32   | NTFS  |
| -------------------------------- | ------- | ----- |
| Cross-device compatibility       | High    | Lower |
| Files larger than 4 GB           | No      | Yes   |
| Granular permissions             | No      | Yes   |
| Journaling                       | No      | Yes   |
| Built-in security features       | Limited | Yes   |
| Common Windows system filesystem | No      | Yes   |

The most important distinction for this module is:

```text
NTFS
  ↓
Windows filesystem
  +
Permissions
  +
Security
```

---

# NTFS Permissions

NTFS provides several permissions that determine what a user or group can do with a file or directory.

The important permissions introduced in the material are:

| Permission             | Allows                                        |
| ---------------------- | --------------------------------------------- |
| `Full Control`         | Read, write, modify, and delete               |
| `Modify`               | Read, write, and delete                       |
| `List Folder Contents` | View/list folders and execute files           |
| `Read and Execute`     | Read and execute files                        |
| `Write`                | Create/write files                            |
| `Read`                 | View files and their contents                 |
| `Traverse Folder`      | Pass through folders to access deeper objects |

---

# Understanding Permissions

Suppose we have:

```text
C:\Company\
    │
    └── reports.txt
```

Different users could have different permissions.

For example:

```text
Administrator
     ↓
Full Control


Employee
     ↓
Read


Backup Service
     ↓
Read and Execute
```

This means Windows can control access to the same filesystem object depending on **which user or group is accessing it**.

---

# Full Control

```text
Full Control
```

provides the highest level of the basic permissions introduced here.

It allows operations such as:

```text
Read
Write
Modify
Delete
```

---

# Modify

```text
Modify
```

allows:

```text
Read
Write
Delete
```

but does not provide every capability included with Full Control.

---

# Read and Execute

```text
Read and Execute
```

allows us to:

```text
Read files
List files/directories
Execute files
```

This commonly appears abbreviated as:

```text
RX
```

---

# Read

```text
Read
```

allows viewing:

```text
Files
Directories
File contents
```

It does not automatically provide permission to modify them.

---

# Write

```text
Write
```

allows operations such as:

```text
Create files
Write data to files
```

---

# Permission Inheritance

NTFS permissions can be **inherited**.

This means that files and directories can receive permissions from their **parent directory**.

For example:

```text
C:\Company
   │
   │ Read permission
   │
   └── Reports
          │
          └── report.txt
```

The child objects can inherit permissions from:

```text
C:\Company
```

Conceptually:

```text
Parent Folder
     ↓
Permissions
     ↓
Child Folder
     ↓
Files
```

This prevents administrators from having to configure permissions individually for every file.

Inheritance can also be disabled when specific permissions need to be configured directly.

---

# `icacls`

NTFS permissions can be managed through the Windows graphical interface, but Windows also provides the command-line utility:

```cmd
icacls
```

`icacls` allows us to inspect and manage permissions from the command line.

---

# Viewing Permissions with `icacls`

For example:

```cmd
C:\htb> icacls C:\Windows
```

The material shows output such as:

```cmd
c:\windows NT SERVICE\TrustedInstaller:(F)
           NT SERVICE\TrustedInstaller:(CI)(IO)(F)
           NT AUTHORITY\SYSTEM:(M)
           NT AUTHORITY\SYSTEM:(OI)(CI)(IO)(F)
           BUILTIN\Administrators:(M)
           BUILTIN\Administrators:(OI)(CI)(IO)(F)
           BUILTIN\Users:(RX)
```

At first this output looks complicated, but it follows a general structure:

```text
Account / Group
       ↓
Inheritance information
       ↓
Permission
```

For example:

```cmd
BUILTIN\Users:(RX)
```

means the built-in Users group has:

```text
RX
 ↓
Read and Execute
```

---

# Basic `icacls` Permission Codes

The material introduces these basic permission abbreviations:

| Code | Permission       |
| ---- | ---------------- |
| `F`  | Full access      |
| `D`  | Delete           |
| `N`  | No access        |
| `M`  | Modify           |
| `RX` | Read and execute |
| `R`  | Read only        |
| `W`  | Write only       |

For example:

```cmd
BUILTIN\Users:(RX)
```

can be read as:

```text
BUILTIN\Users
       ↓
Read + Execute
```

While:

```cmd
NT SERVICE\TrustedInstaller:(F)
```

means:

```text
TrustedInstaller
       ↓
Full access
```

---

# Inheritance Codes

`icacls` also displays how permissions are inherited.

The material introduces:

| Code   | Meaning                          |
| ------ | -------------------------------- |
| `(CI)` | Container Inherit                |
| `(OI)` | Object Inherit                   |
| `(IO)` | Inherit Only                     |
| `(NP)` | Do Not Propagate                 |
| `(I)`  | Permission inherited from parent |

---

# Container Inherit — `(CI)`

```text
CI = Container Inherit
```

The permission can be inherited by **subdirectories**.

Think:

```text
Container
    ↓
Folder
```

---

# Object Inherit — `(OI)`

```text
OI = Object Inherit
```

The permission can be inherited by **files**.

Think:

```text
Object
   ↓
File
```

Therefore:

```text
(CI)
 ↓
Folders


(OI)
 ↓
Files
```

---

# Inherit Only — `(IO)`

```text
IO = Inherit Only
```

The permission is intended to be inherited by child objects rather than applying directly to the current object.

---

# Reading an `icacls` Entry

Consider:

```cmd
NT AUTHORITY\SYSTEM:(OI)(CI)(IO)(F)
```

We can break it down:

```text
NT AUTHORITY\SYSTEM
        │
        ├── (OI) → Object Inherit
        │
        ├── (CI) → Container Inherit
        │
        ├── (IO) → Inherit Only
        │
        └── (F)  → Full Access
```

The material explains that this gives the SYSTEM account full-control permissions that apply through inheritance to filesystem objects within the directory structure.

---

# Granting Permissions

`icacls` can also modify permissions.

The material uses:

```cmd
icacls C:\users /grant joe:f
```

This grants the user:

```text
joe
```

the permission:

```text
F
↓
Full Control
```

over:

```cmd
C:\Users
```

So we can read:

```cmd
/grant joe:f
```

as:

```text
grant
  │
  └── joe
       │
       └── Full Control
```

---

# Important Inheritance Detail

The command:

```cmd
icacls C:\users /grant joe:f
```

does **not** include:

```text
(OI)
(CI)
```

Therefore, according to the example, Joe receives Full Control over:

```cmd
C:\Users
```

but those rights are not automatically applied to all files and subdirectories inside it.

After granting the permission, `icacls` shows:

```cmd
c:\users WS01\joe:(F)
```

---

# Removing Permissions

Permissions can also be removed.

The material uses:

```cmd
icacls C:\users /remove joe
```

This removes the permission entries associated with `joe`.

---

# Why `icacls` Matters

`icacls` provides command-line control over NTFS permissions.

It can be used to:

```text
View permissions
Grant permissions
Remove permissions
Deny access
Manage inheritance
Change ownership
```

It can also be used in Windows domain environments to assign specific permissions to users and groups.

For security, understanding permissions is important because incorrect filesystem permissions can allow users to access or modify files they should not control.

---

# Core Mental Model

The main relationship in this section is:

```text
Windows
   ↓
NTFS
   ↓
Files / Folders
   ↓
Permissions
   ↓
Users / Groups
```

For example:

```text
C:\Data\report.txt
       │
       ├── Administrator → Full Control
       │
       ├── Employee      → Read
       │
       └── Service       → Read + Execute
```

And:

```text
Parent Folder
     │
     │ permissions
     ▼
Child Folder
     │
     │ inheritance
     ▼
Files
```

`icacls` lets us inspect and modify these permissions from the command line:

```text
NTFS Permissions
       ↕
     icacls
```

---

# Quick Reference

### View permissions

```cmd
icacls C:\Windows
```

### Grant Full Control

```cmd
icacls C:\users /grant joe:f
```

### Remove permission entries

```cmd
icacls C:\users /remove joe
```

### Basic permissions

```text
F  → Full Access
D  → Delete
N  → No Access
M  → Modify
RX → Read and Execute
R  → Read
W  → Write
```

### Inheritance

```text
CI → Container Inherit
OI → Object Inherit
IO → Inherit Only
NP → Do Not Propagate
I  → Inherited from parent
```

---

## Key Takeaway

**NTFS is the primary Windows file system covered in this module and provides features such as reliability, journaling, support for large partitions, and granular file and folder permissions. NTFS permissions determine what users and groups can do with filesystem objects and can be inherited from parent directories. The `icacls` command allows us to inspect and manage these permissions directly from the Windows command line.**
