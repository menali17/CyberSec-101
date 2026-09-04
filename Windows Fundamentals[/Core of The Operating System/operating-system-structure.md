# Operating System Structure

Windows organizes files and directories differently from Linux.

In Windows, the root of the file system is represented by a **drive letter**, usually:

```cmd
C:\
```

The drive containing the Windows installation is commonly the `C:` drive. Other physical or virtual drives can receive different letters, such as:

```cmd
D:\
E:\
F:\
```

A simple mental model is:

```text
Windows
   │
   ├── C:\
   │    └── Usually the Windows installation
   │
   ├── D:\
   │
   └── E:\
        └── Other physical or virtual drives
```

---

# Important Windows Directories

The root of a typical Windows installation contains several important directories.

```cmd
C:\
├── PerfLogs
├── Program Files
├── Program Files (x86)
├── ProgramData
├── Users
└── Windows
```

Understanding what these directories contain helps us navigate Windows systems and identify where important files may be stored.

---

# `C:\Program Files`

```cmd
C:\Program Files
```

This directory is primarily used for installed applications.

On a **64-bit Windows system**, it normally contains:

```text
64-bit applications
```

Example:

```cmd
C:\Program Files\Google
C:\Program Files\Microsoft Office
```

---

# `C:\Program Files (x86)`

On 64-bit Windows systems:

```cmd
C:\Program Files (x86)
```

is used for:

```text
32-bit and 16-bit applications
```

Therefore, on a 64-bit Windows system:

```text
Program Files
     ↓
64-bit applications


Program Files (x86)
     ↓
32-bit / 16-bit applications
```

---

# `C:\ProgramData`

```cmd
C:\ProgramData
```

is a **hidden directory** containing data required by installed applications.

Unlike user-specific application data, information stored here can be available regardless of which user is currently running the program.

Conceptually:

```text
ProgramData
     ↓
Application data
     ↓
Shared between users
```

---

# `C:\Users`

```cmd
C:\Users
```

contains the profiles of users who log into the system.

For example:

```cmd
C:\Users\Administrator
C:\Users\htb-student
C:\Users\john
```

A user's directory can contain personal files and settings.

Conceptually:

```text
C:\Users
   │
   ├── Administrator
   ├── htb-student
   ├── john
   ├── Public
   └── Default
```

---

# `Default`

The:

```cmd
C:\Users\Default
```

profile acts as a **template for new user profiles**.

When a new user profile is created, Windows uses the Default profile as its base.

```text
Default Profile
      ↓
New user created
      ↓
New user profile
```

---

# `Public`

The:

```cmd
C:\Users\Public
```

directory is intended for files that can be shared between users of the computer.

By default, it is accessible to users of the system.

---

# `AppData`

Inside individual user profiles, Windows contains the hidden:

```cmd
AppData
```

directory.

For example:

```cmd
C:\Users\htb-student\AppData
```

`AppData` stores application data and settings associated with that specific user.

It contains three important directories:

```text
AppData
   │
   ├── Roaming
   ├── Local
   └── LocalLow
```

### `Roaming`

```cmd
AppData\Roaming
```

contains data designed to follow the user's profile between computers in environments where profile roaming is used.

### `Local`

```cmd
AppData\Local
```

contains data specific to the current computer.

### `LocalLow`

```cmd
AppData\LocalLow
```

is similar to `Local`, but operates with a **lower integrity level**.

A useful mental model is:

```text
AppData
   │
   ├── Roaming
   │      └── User data that can follow the profile
   │
   ├── Local
   │      └── Data specific to this computer
   │
   └── LocalLow
          └── Local data with lower integrity
```

---

# `C:\Windows`

```cmd
C:\Windows
```

contains most of the files required by the Windows operating system.

Inside it are several important system directories.

---

# System32 and SysWOW64

Directories such as:

```cmd
C:\Windows\System32
C:\Windows\SysWOW64
```

contain many of the DLLs and other files required for core Windows functionality and the Windows API.

A **DLL (Dynamic-Link Library)** contains code and functionality that programs can load and use.

When an application requests a DLL without specifying its complete path, Windows searches appropriate system locations for it.

For now, the important idea is:

```text
System32 / SysWOW64
        ↓
Important Windows system files
        ↓
DLLs and Windows API components
```

---

# WinSxS

```cmd
C:\Windows\WinSxS
```

is the **Windows Component Store**.

It contains Windows components, updates, and service-pack-related files.

---

# Exploring Directories with `dir`

Windows provides the:

```cmd
dir
```

command to list files and directories.

This is conceptually similar to:

```bash
ls
```

on Linux.

The material uses:

```cmd
C:\htb> dir c:\ /a
```

Example output:

```cmd
Directory of c:\

$Recycle.Bin
Program Files
Program Files (x86)
ProgramData
Recovery
Users
Windows
```

The:

```cmd
/a
```

option allows the listing to include entries with different attributes, including hidden/system items that may otherwise not appear in a normal listing.

So we can roughly associate:

```text
Linux                   Windows

ls                      dir
 │                       │
 ▼                       ▼
List directory          List directory
contents                contents
```

---

# Files in the Root Directory

The HTB example also shows several files directly under:

```cmd
C:\
```

including:

```cmd
hiberfil.sys
pagefile.sys
swapfile.sys
```

At this stage, the important point is simply that the root directory contains both:

```text
Directories
    +
System files
```

Some of these files may normally be hidden from users.

---

# Exploring the Directory Tree

Windows also provides:

```cmd
tree
```

The `tree` command displays directories in a hierarchical structure.

For example:

```cmd
C:\htb> tree "c:\Program Files (x86)\VMware"
```

produces a structure similar to:

```cmd
C:\PROGRAM FILES (X86)\VMWARE
├───VMware VIX
│   ├───doc
│   ├───samples
│   └───Workstation-15.0.0
└───VMware Workstation
    ├───env
    ├───hostd
    ├───OVFTool
    └───x64
```

This makes it easier to visualize relationships between directories.

---

# `dir` vs `tree`

The difference can be understood as:

```text
dir
 │
 ▼
"What is inside this directory?"


tree
 │
 ▼
"How are these directories organized?"
```

For example:

```cmd
dir C:\
```

lists the contents of `C:\`.

While:

```cmd
tree C:\
```

shows the hierarchical directory structure.

---

# Showing Files with `tree`

The material also introduces:

```cmd
tree c:\ /f
```

The:

```cmd
/f
```

option tells `tree` to display **files as well as directories**.

Without `/f`, `tree` primarily displays the directory structure.

Therefore:

```text
tree C:\
      ↓
Directories


tree C:\ /f
      ↓
Directories + Files
```

---

# Combining `tree` with `more`

Displaying the entire `C:` drive can produce a huge amount of output.

The material therefore uses:

```cmd
tree c:\ /f | more
```

Here we have a concept already familiar from Linux:

```cmd
|
```

The pipe sends the output of one command into another command.

```text
tree c:\ /f
     │
     │ output
     ▼
     |
     │
     ▼
   more
```

`more` displays the output **one screen at a time**.

So:

```cmd
tree c:\ /f | more
```

means:

> Display all directories and files under `C:\`, but show the results one screen at a time.

---

# Windows vs Linux File System Structure

A useful comparison with what we already studied in Linux is:

| Linux                     | Windows                            |
| ------------------------- | ---------------------------------- |
| `/`                       | `C:\`                              |
| `/home/user`              | `C:\Users\user`                    |
| `ls`                      | `dir`                              |
| `tree`                    | `tree`                             |
| `/` separates directories | `\` commonly separates directories |

For example:

Linux:

```bash
/home/htb-student/Documents
```

Windows:

```cmd
C:\Users\htb-student\Documents
```

The major structural difference to remember is:

```text
Linux
  ↓
Single filesystem hierarchy beginning at /


Windows
  ↓
Drive letters such as C:\, D:\, E:\
```

---

# Core Mental Model

```text
C:\
│
├── Program Files
│      └── Applications
│
├── Program Files (x86)
│      └── 32-bit applications on 64-bit Windows
│
├── ProgramData
│      └── Shared application data
│
├── Users
│      │
│      └── <username>
│             │
│             └── AppData
│                   ├── Roaming
│                   ├── Local
│                   └── LocalLow
│
└── Windows
       │
       ├── System32
       ├── SysWOW64
       └── WinSxS
```

---

# Quick Reference

### Windows root directory

```cmd
C:\
```

### List directory contents

```cmd
dir
```

### List the root directory

```cmd
dir C:\
```

### Include entries with different attributes

```cmd
dir C:\ /a
```

### Display directory hierarchy

```cmd
tree C:\
```

### Display directories and files

```cmd
tree C:\ /f
```

### Display one screen at a time

```cmd
tree C:\ /f | more
```

### User profiles

```cmd
C:\Users
```

### Installed applications

```cmd
C:\Program Files
C:\Program Files (x86)
```

### Shared application data

```cmd
C:\ProgramData
```

### Windows operating system files

```cmd
C:\Windows
```

### User application data

```cmd
C:\Users\<username>\AppData
```

---

## Key Takeaway

**Windows organizes its filesystem around drive letters such as `C:\`, rather than the single `/` hierarchy used by Linux. Important locations include `C:\Users` for user profiles, `C:\Program Files` for applications, `C:\ProgramData` for shared application data, and `C:\Windows` for operating system files. We can explore the filesystem using `dir` to list directory contents and `tree` to visualize the directory hierarchy.**
