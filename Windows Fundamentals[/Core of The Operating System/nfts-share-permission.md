# NTFS vs. Share Permissions

Windows environments commonly use **network shares** to make resources such as files and printers available to other computers.

The protocol primarily used for this is:

```text
SMB
Server Message Block
```

SMB allows a client to access shared resources hosted by another system over the network.

A basic model is:

```text
Client
   │
   │ SMB request
   ▼
Windows Server
   │
   ├── Shared Folder
   ├── Files
   └── Printers
```

---

# SMB

**SMB (Server Message Block)** is a protocol used by Windows for sharing resources across a network.

Common resources include:

```text
Files
Folders
Printers
```

For example, suppose a Windows machine contains:

```cmd
C:\Company Data
```

If this folder is configured as a network share, another computer may access it through SMB.

Conceptually:

```text
Computer A
   │
   │ Network
   │ SMB
   ▼
Computer B
   │
   └── C:\Company Data
```

---

# NTFS Permissions vs. Share Permissions

A very important distinction is:

> **NTFS permissions and Share permissions are not the same thing.**

However, both can apply to the **same shared resource**.

The easiest way to understand the difference is:

```text
NTFS Permissions
      ↓
Permissions on the actual
files and folders


Share Permissions
      ↓
Permissions when accessing
the resource through SMB
```

---

# NTFS Permissions

NTFS permissions apply to files and folders on the Windows filesystem.

For example:

```cmd
C:\Company Data
```

could have NTFS permissions such as:

```text
Administrator → Full Control
Employee      → Read
Developer     → Modify
```

These permissions apply to the filesystem objects themselves.

Therefore:

```text
Windows Filesystem
       ↓
      NTFS
       ↓
File / Folder
       ↓
NTFS Permissions
```

---

# Share Permissions

Share permissions apply when a resource is accessed **through SMB over the network**.

The three share permissions introduced in the material are:

| Share Permission | Allows                                          |
| ---------------- | ----------------------------------------------- |
| `Full Control`   | Read, change files, and manage permissions      |
| `Change`         | Read, edit, delete, and create files/subfolders |
| `Read`           | View files and subfolders                       |

These are simpler than the NTFS permission system.

---

# Share vs. NTFS Permission Types

Share permissions:

```text
Full Control
Change
Read
```

NTFS basic permissions:

```text
Full Control
Modify
Read & Execute
List Folder Contents
Read
Write
Special Permissions
```

NTFS therefore provides much more granular control.

---

# When Does Each Permission Apply?

This is the central concept of this section.

Suppose the folder:

```cmd
C:\Company Data
```

is shared through SMB.

If we are logged directly into the Windows machine, including through RDP:

```text
Windows
   │
   └── C:\Company Data
             ↓
       NTFS Permissions
```

Share permissions do not need to be considered because we are accessing the folder directly through the filesystem.

But if another computer accesses it through SMB:

```text
Linux / Windows Client
        │
        │ SMB
        ▼
Windows Server
        │
        ▼
C:\Company Data
```

then both:

```text
Share Permissions
        +
NTFS Permissions
```

must be considered.

---

# Core Permission Model

This distinction is worth memorizing:

```text
LOCAL / RDP ACCESS

User
 │
 ▼
NTFS
 │
 ▼
File
```

But:

```text
SMB NETWORK ACCESS

User
 │
 ▼
Share Permissions
 │
 ▼
NTFS Permissions
 │
 ▼
File
```

Therefore:

> **NTFS permissions apply to the filesystem. Share permissions apply when accessing a shared resource through SMB.**

---

# Example

Suppose:

```cmd
C:\Company Data
```

has:

```text
NTFS:
htb-student → Full Control
```

But the SMB share has:

```text
Share:
Everyone → Read
```

If `htb-student` accesses the folder directly through RDP:

```text
htb-student
     ↓
NTFS → Full Control
```

The user has Full Control.

But if the same user accesses it remotely through SMB:

```text
htb-student
     ↓
Share → Read
     ↓
NTFS → Full Control
```

the Share permission restricts what can be done through that network share.

In this example, the effective access through SMB is therefore limited to:

```text
Read
```

This illustrates why we need to consider both permission layers for network shares.

---

# NTFS Special Permissions

NTFS also provides more granular **special permissions**, including:

```text
Traverse folder / execute file
List folder / read data
Read attributes
Read extended attributes
Create files / write data
Create folders / append data
Write attributes
Write extended attributes
Delete subfolders and files
Delete
Read permissions
Change permissions
Take ownership
```

These allow administrators to define precisely what users and groups can do.

For this section, the important idea is simply:

```text
Share Permissions
       ↓
Relatively simple


NTFS Permissions
       ↓
Much more granular
```

---

# ACLs and ACEs

Windows uses **Access Control Lists (ACLs)** to manage access.

An ACL contains:

```text
ACE
Access Control Entry
```

An ACE usually associates a:

```text
User / Group
      +
Permission
```

For example:

```text
ACL
 │
 ├── Administrators → Full Control
 ├── Developers     → Change
 └── Everyone       → Read
```

Users and groups appearing in these entries are also called **security principals**.

---

# Creating a Network Share

The material creates a folder on the Windows target and enables:

```text
Advanced Sharing
```

Once the folder is shared, Windows creates an SMB share associated with that directory.

Conceptually:

```text
C:\Users\htb-student\Desktop\Company Data
                    │
                    │ Advanced Sharing
                    ▼
              SMB Share
                    │
                    ▼
             "Company Data"
```

The share can then be accessed by other computers over the network.

---

# Client and Server

When accessing the share from Pwnbox:

```text
Pwnbox
  │
  │ Client
  │
  │ SMB
  ▼
Windows Target
  │
  │ Server
  ▼
Company Data
```

The material emphasizes that **server** refers to the software/system providing a service to clients.

---

# `smbclient`

From Linux, we can interact with SMB shares using:

```bash
smbclient
```

This tool allows us to:

* Discover available shares.
* Authenticate to SMB.
* Connect to shares.
* Interact with files inside shares.

---

# Listing Available Shares

The material uses:

```bash
smbclient -L SERVER_IP -U htb-student
```

Example:

```bash
menali@htb[/htb]$ smbclient -L SERVER_IP -U htb-student
Enter WORKGROUP\htb-student's password:

    Sharename       Type      Comment
    ---------       ----      -------
    ADMIN$          Disk      Remote Admin
    C$              Disk      Default share
    Company Data    Disk
    IPC$            IPC       Remote IPC
```

The important options are:

```text
-L
 ↓
List shares


-U
 ↓
Specify user
```

So:

```bash
smbclient -L SERVER_IP -U htb-student
```

means:

> Connect to the SMB server and list the shares available to `htb-student`.

---

# Connecting to a Share

Once we know the share name, we can connect directly to it.

The material uses:

```bash
smbclient '\\SERVER_IP\Company Data' -U htb-student
```

After successful authentication:

```bash
smb: \>
```

appears.

This means we now have an interactive SMB session.

Conceptually:

```text
Linux Terminal
     │
     │ smbclient
     ▼
SMB Session
     │
     ▼
Company Data
```

---

# Windows Defender Firewall

Even if:

```text
IP address       → correct
Username         → correct
Password         → correct
Permissions      → correct
```

the connection may still fail.

Why?

Because the **Windows Defender Firewall** can block the network connection.

Firewalls control traffic flowing:

```text
Inbound
Outbound
```

---

# Windows Firewall Profiles

Windows Defender Firewall uses different profiles:

```text
Public
Private
Domain
```

Different firewall rules can apply depending on which profile is active.

Instead of completely disabling the firewall, the preferred approach is to enable the required predefined rules or create appropriate exceptions.

---

# Workgroup vs. Domain Authentication

The material also introduces an important distinction.

In a **Workgroup**:

```text
Authentication
      ↓
Local SAM database
```

In a **Windows Domain**:

```text
Authentication
      ↓
Active Directory
```

So:

```text
WORKGROUP
   │
   └── Each Windows system
       handles local accounts
       through its SAM


DOMAIN
   │
   └── Authentication can be
       centralized through
       Active Directory
```

This distinction becomes much more important when we study Active Directory.

---

# Mounting an SMB Share

Instead of interacting with a share through the `smbclient` prompt, we can **mount** it into the Linux filesystem.

The material uses:

```bash
sudo mount -t cifs -o username=htb-student,password=Academy_WinFun! //ipaddoftarget/"Company Data" /home/user/Desktop/
```

Here:

```text
mount
  ↓
Attach a filesystem/resource


-t cifs
  ↓
Use CIFS/SMB filesystem type


//target/Company Data
  ↓
Remote SMB share


/home/user/Desktop/
  ↓
Local mount point
```

Conceptually:

```text
Windows
Company Data
     │
     │ SMB
     ▼
Linux
/home/user/Desktop/
```

After mounting it, the remote Windows share can be accessed through the Linux filesystem.

---

# CIFS Utilities

If CIFS support tools are missing, the material installs:

```bash
sudo apt-get install cifs-utils
```

These utilities provide tools needed for mounting SMB/CIFS shares on Linux.

---

# `net share`

On Windows, we can display shared resources using:

```cmd
net share
```

The material shows:

```cmd
C:\Users\htb-student> net share

Share name    Resource
-----------------------------------------------
C$            C:\
IPC$
ADMIN$        C:\WINDOWS
Company Data  C:\Users\htb-student\Desktop\Company Data
```

This reveals several shares.

---

# Administrative Shares

Notice:

```text
C$
ADMIN$
IPC$
```

Windows automatically creates certain administrative shares.

For example:

```text
C$
 ↓
C:\
```

and:

```text
ADMIN$
   ↓
C:\Windows
```

The material points out that `C$` exists even though we did not manually share the entire `C:` drive. Access still requires appropriate permissions.

---

# Monitoring SMB Shares

Windows provides several places where shared resources can be inspected.

One is:

```text
Computer Management
```

There we can examine:

```text
Shares
Sessions
Open Files
```

These can help us understand:

```text
What is shared?
Who is connected?
Which files are open?
```

This can be useful both for system administration and incident investigation.

---

# Event Viewer

Windows also records many activities in logs that can be inspected through:

```text
Event Viewer
```

The material describes logs as similar to a **journal maintained by the computer**, recording actions and associated details.

Activities involving accessing and modifying shared resources can therefore leave evidence in Windows logs.

This gives us another important security relationship:

```text
SMB activity
     ↓
Windows Logs
     ↓
Event Viewer
     ↓
Investigation
```

---

# Complete SMB Access Model

The entire process can be visualized as:

```text
Linux Client
    │
    │ smbclient
    ▼
Network
    │
    │ SMB
    ▼
Windows Defender Firewall
    │
    ▼
Share Permissions
    │
    ▼
NTFS Permissions
    │
    ▼
File / Folder
```

Several different layers can therefore prevent access:

```text
Network connectivity
        ↓
Firewall
        ↓
Authentication
        ↓
Share permissions
        ↓
NTFS permissions
```

---

# Quick Reference

### SMB

```text
Server Message Block
```

Used for network resource sharing.

### List SMB shares

```bash
smbclient -L SERVER_IP -U htb-student
```

### Connect to an SMB share

```bash
smbclient '\\SERVER_IP\Company Data' -U htb-student
```

### List Windows shares

```cmd
net share
```

### Mount SMB share on Linux

```bash
sudo mount -t cifs -o username=<user>,password=<password> //<target>/<share> <mount_point>
```

### Install CIFS utilities

```bash
sudo apt-get install cifs-utils
```

### Share permissions

```text
Full Control
Change
Read
```

### Windows Firewall profiles

```text
Public
Private
Domain
```

### Authentication

```text
Workgroup → local SAM

Domain    → Active Directory
```

---

# Core Mental Model

```text
NTFS PERMISSIONS
"Can this user access this file/folder
on the filesystem?"


SHARE PERMISSIONS
"Can this user perform this action
through the SMB share?"


SMB
"How is the resource being shared
over the network?"
```

When accessing locally or through RDP:

```text
User
 ↓
NTFS
 ↓
File
```

When accessing through SMB:

```text
User
 ↓
SMB
 ↓
Share Permissions
 ↓
NTFS Permissions
 ↓
File
```

---

## Key Takeaway

**NTFS permissions and Share permissions are separate access-control layers. NTFS permissions apply to files and folders on the Windows filesystem, while Share permissions apply when those resources are accessed through SMB over the network. When accessing a network share, both permission layers must be considered. Tools such as `smbclient` allow us to discover and access SMB shares from Linux, while `net share`, Computer Management, and Event Viewer help us inspect and monitor shared resources from Windows.**
