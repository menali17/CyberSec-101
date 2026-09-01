# File System Management

Linux supports multiple file systems and storage technologies. File system management involves organizing, storing, mounting, and maintaining data on disks and other storage devices.

Common Linux-supported file systems include:

```text
ext2
ext3
ext4
XFS
Btrfs
NTFS
```

Different file systems provide different trade-offs in areas such as performance, compatibility, reliability, and data integrity.

---

# Common File Systems

## ext2

`ext2` is an older Linux file system.

It does not provide:

```text
journaling
```

This makes it less suitable for many modern systems, although it can still be useful where low overhead is desired.

---

## ext3

`ext3` introduced journaling.

Journaling helps the file system recover after unexpected events such as:

```text
System crashes
Power failures
Unexpected shutdowns
```

---

## ext4

`ext4` is a widely used modern Linux file system.

It provides:

```text
Journaling
Good performance
Reliability
Support for large files
```

The material presents it as a common default choice for modern Linux systems.

---

## Btrfs

`Btrfs` provides more advanced storage features.

Examples include:

```text
Snapshots
Built-in data integrity checks
```

This can make it useful for more complex storage environments.

---

## XFS

`XFS` is designed for:

```text
High performance
Large files
High I/O workloads
```

---

## NTFS

`NTFS` was originally developed for Windows.

On Linux, it is particularly useful for compatibility scenarios such as:

```text
Dual-boot systems
External drives
Storage shared between Linux and Windows
```

---

# File System Overview

A useful mental model is:

```text
Physical Disk
     │
     ▼
Partitions
     │
     ▼
File System
     │
     ▼
Files and Directories
```

For example:

```text
/dev/sdb
   │
   ├── /dev/sdb1
   │       │
   │       ▼
   │     ext4
   │       │
   │       ▼
   │    /mnt/data
   │
   └── /dev/sdb2
```

---

# Inodes

One of the most important concepts in Linux file systems is the:

```text
inode
```

An inode is a data structure containing metadata about a file or directory.

The material mentions information such as:

```text
Permissions
Ownership
Size
Timestamps
Pointers to data blocks
```

However, the inode does not store the actual contents of the file or its filename.

---

# Inode Mental Model

Conceptually:

```text
Filename
   │
   ▼
Directory Entry
   │
   ▼
Inode
   │
   ├── Permissions
   ├── Owner
   ├── Size
   ├── Timestamps
   │
   └── Pointers
          │
          ▼
       Data Blocks
```

The inode tells Linux where the actual data is stored and contains metadata describing the file.

---

# Inode Table

The:

```text
inode table
```

is a collection of inodes used by the operating system to track files and directories.

A disk may theoretically run out of available inodes even before running out of raw storage space.

That would mean:

```text
Free disk space exists
BUT
No free inodes remain
```

and new files may no longer be created.

---

# Viewing Inodes

The material uses:

```bash
ls -il
```

Example:

```bash
menali@htb[/htb]$ ls -il

total 0
10678872 -rw-r--r-- 1 cry0l1t3 htb 234123 Feb 14 19:30 myscript.py
10678869 -rw-r--r-- 1 cry0l1t3 htb  43230 Feb 14 11:52 notes.txt
```

The first number is the inode number.

For example:

```text
10678872
```

is the inode associated with:

```text
myscript.py
```

---

# Common File Types

The material introduces three main file types:

```text
Regular files
Directories
Symbolic links
```

---

# Regular Files

Regular files contain data.

Examples include:

```text
Text files
Images
Audio files
Executables
Scripts
Binary files
```

They can exist anywhere within the Linux directory hierarchy.

---

# Directories

Directories organize files and other directories.

For example:

```text
/home/user/
├── notes.txt
├── script.sh
└── Documents/
```

Here:

```text
/home/user
```

is the parent directory of:

```text
notes.txt
script.sh
Documents/
```

---

# Symbolic Links

A symbolic link, or:

```text
symlink
```

acts as a reference to another file or directory.

Conceptually:

```text
shortcut
   │
   ▼
original file
```

For example:

```text
link.txt
   │
   └────────────► /home/user/documents/file.txt
```

The symlink does not need to duplicate the original data.

---

# Disks and Drives

Linux represents storage devices using paths under:

```text
/dev/
```

Examples include:

```text
/dev/sda
/dev/sdb
/dev/vda
/dev/nvme0n1
```

Disk management includes:

```text
Creating partitions
Deleting partitions
Inspecting partitions
Formatting partitions
Mounting file systems
```

The material introduces `fdisk` as an important disk-management tool.

---

# `fdisk`

We can inspect disks and partitions using:

```bash
sudo fdisk -l
```

Example:

```bash
Disk /dev/vda: 160 GiB, 171798691840 bytes, 335544320 sectors

Device      Boot     Start       End   Sectors  Size Id Type
/dev/vda1   *         2048 158974027 158971980 75.8G 83 Linux
/dev/vda2        158974028 167766794   8792767  4.2G 82 Linux swap / Solaris
```

Here:

```text
/dev/vda
```

is the disk.

And:

```text
/dev/vda1
/dev/vda2
```

are partitions on that disk.

---

# Disk vs Partition

This distinction is important.

```text
/dev/vda
→ physical or virtual disk
```

while:

```text
/dev/vda1
→ first partition

/dev/vda2
→ second partition
```

Conceptually:

```text
/dev/vda
│
├── /dev/vda1
│
└── /dev/vda2
```

---

# Partitioning

Partitioning divides a disk into separate logical sections.

For example:

```text
500 GB Disk
   │
   ├── 100 GB → /
   │
   ├── 350 GB → /home
   │
   └── 50 GB  → other use
```

Each partition can use its own file system.

For example:

```text
/dev/sda1 → ext4

/dev/sda2 → NTFS
```

---

# Mounting

Linux does not normally expose disks as separate drive letters like:

```text
C:
D:
E:
```

Instead, a file system is attached to the existing Linux directory hierarchy.

This process is called:

```text
mounting
```

The directory where it is attached is called a:

```text
mount point
```

---

# Mounting Mental Model

Suppose we have:

```text
/dev/sdb1
```

containing:

```text
photo.jpg
notes.txt
backup.zip
```

Before mounting:

```text
/dev/sdb1
→ storage device exists
```

We create:

```text
/mnt/usb
```

and mount the device there:

```text
/dev/sdb1
    │
    ▼
/mnt/usb
```

Now:

```bash
ls /mnt/usb
```

can show:

```text
photo.jpg
notes.txt
backup.zip
```

---

# Listing Mounted File Systems

Running:

```bash
mount
```

without arguments displays mounted file systems.

Example:

```bash
sysfs on /sys type sysfs (rw,nosuid,nodev,noexec,relatime)
proc on /proc type proc (rw,nosuid,nodev,noexec,relatime)
/dev/vda1 on / type btrfs (rw,noatime,...)
```

A useful way to read:

```text
/dev/vda1 on / type btrfs
```

is:

```text
Device
/dev/vda1

Mount point
/

File system
btrfs
```

---

# Mounting a USB Drive

The material gives:

```bash
sudo mount /dev/sdb1 /mnt/usb
```

The syntax is:

```text
mount DEVICE MOUNT_POINT
```

So:

```text
/dev/sdb1
→ storage device

/mnt/usb
→ directory where we want to access it
```

After mounting:

```bash
cd /mnt/usb && ls -l
```

allows us to access its files.

---

# Mount Point

A mount point is simply a directory.

For example:

```text
/mnt/usb
```

Before mounting, it is just a normal directory.

After:

```bash
sudo mount /dev/sdb1 /mnt/usb
```

it becomes the access point for the file system stored on:

```text
/dev/sdb1
```

---

# Unmounting

To disconnect a mounted file system, we use:

```bash
sudo umount /mnt/usb
```

Notice the command is:

```text
umount
```

not:

```text
unmount
```

The material uses the mount point to specify what should be detached.

---

# Why Unmounting Matters

We should not simply remove a storage device while the operating system is still using it.

Unmounting allows Linux to properly detach the file system.

However, Linux may refuse to unmount a file system if a process is currently using it.

Conceptually:

```text
Process
   │
   ▼
Open file on /mnt/usb
   │
   ▼
umount
   │
   └── cannot safely detach yet
```

---

# Finding Open Files with `lsof`

The command:

```bash
lsof
```

stands for the tool used to list open files.

The material combines it with:

```bash
lsof | grep cry0l1t3
```

to filter the output.

This can help identify processes using files that may prevent a file system from being unmounted.

---

# `/etc/fstab`

Manual mounts disappear after a reboot unless Linux is configured to recreate them.

Persistent mount configuration is stored in:

```text
/etc/fstab
```

This file describes file systems and where they should be mounted during system startup.

---

# Example `/etc/fstab`

The material shows entries such as:

```text
/dev/sda1 / ext4 defaults 0 0
/dev/sda2 /home ext4 defaults 0 0
/dev/sdb1 /mnt/usb ext4 rw,noauto,user 0 0
192.168.1.100:/nfs /mnt/nfs nfs defaults 0 0
```

A simplified structure is:

```text
DEVICE    MOUNT_POINT    TYPE    OPTIONS
```

For example:

```text
/dev/sdb1 /mnt/usb ext4 rw,noauto,user
```

means:

```text
Device
→ /dev/sdb1

Mount point
→ /mnt/usb

File system
→ ext4

Options
→ rw,noauto,user
```

---

# `noauto`

The option:

```text
noauto
```

tells Linux not to automatically mount that file system at boot.

So:

```text
auto
→ mount during boot

noauto
→ do not automatically mount during boot
```

---

# NFS in `/etc/fstab`

The material also shows:

```text
192.168.1.100:/nfs /mnt/nfs nfs defaults 0 0
```

This connects with the previous NFS section.

Here:

```text
192.168.1.100:/nfs
→ remote NFS share

/mnt/nfs
→ local mount point

nfs
→ file system type
```

So `/etc/fstab` can configure both local and network file systems.

---

# SWAP

Swap is disk space used as part of Linux memory management.

When physical RAM becomes heavily used, Linux may move inactive memory pages into:

```text
swap space
```

This frees physical RAM for more active processes.

---

# RAM vs Swap

Conceptually:

```text
RAM
│
├── Active application
├── Active process
├── Active data
│
└── Inactive data
        │
        ▼
       SWAP
```

Instead of keeping everything in RAM, some inactive pages can be moved to storage.

---

# Why Swap Is Slower

RAM is much faster than storage.

So:

```text
RAM
→ fast

Swap
→ slower
```

Swap helps prevent immediate memory exhaustion, but it is not a replacement for sufficient RAM.

---

# Creating Swap Space

The material introduces:

```bash
mkswap
```

and:

```bash
swapon
```

---

# `mkswap`

`mkswap` prepares a device or file to be used as Linux swap space.

Conceptually:

```text
Partition/File
     │
     │ mkswap
     ▼
Prepared Swap Area
```

---

# `swapon`

After the area has been prepared:

```bash
swapon
```

activates it.

Conceptually:

```text
mkswap
→ PREPARE

swapon
→ ACTIVATE
```

---

# Swap Security

The material notes that sensitive information may temporarily exist in swap.

This means that swap can contain data originating from memory.

Because of this, encrypting swap can provide additional protection against data exposure.

---

# Swap and Hibernation

Swap can also be used for:

```text
hibernation
```

During hibernation:

```text
Running system
      │
      ▼
System state written to swap
      │
      ▼
Computer powers off
      │
      ▼
Computer starts again
      │
      ▼
State restored from swap
```

This allows the system to resume from its previous state.

---

# Main Concepts Together

A useful way to connect everything is:

```text
PHYSICAL STORAGE
      │
      ▼
     DISK
      │
      ▼
  PARTITIONS
      │
      ▼
 FILE SYSTEM
      │
      ▼
   MOUNTING
      │
      ▼
DIRECTORY TREE
      │
      ▼
FILES / DIRECTORIES
```

Inside the file system:

```text
Files
  │
  └── metadata stored in inodes
```

And storage can also be reserved for:

```text
Swap
→ memory management
```

---

# Quick Reference

| Concept / Command | Purpose                        |
| ----------------- | ------------------------------ |
| `inode`           | Stores file metadata           |
| `ls -il`          | Display inode numbers          |
| `fdisk`           | Manage disk partitions         |
| `fdisk -l`        | List disks and partitions      |
| `mount`           | Attach a file system           |
| `umount`          | Detach a file system           |
| `/etc/fstab`      | Persistent mount configuration |
| `lsof`            | List open files                |
| `mkswap`          | Prepare swap area              |
| `swapon`          | Activate swap                  |

---

# Commands to Remember First

Inspect disks:

```bash
sudo fdisk -l
```

Show mounted file systems:

```bash
mount
```

Mount a device:

```bash
sudo mount /dev/sdb1 /mnt/usb
```

Unmount:

```bash
sudo umount /mnt/usb
```

Inspect automatic mount configuration:

```bash
cat /etc/fstab
```

Inspect inode numbers:

```bash
ls -il
```

---

# Essential Mental Models

## Disk → Partition → File System → Mount

```text
Disk
/dev/sdb
   │
   ▼
Partition
/dev/sdb1
   │
   ▼
File System
ext4
   │
   ▼
Mount Point
/mnt/usb
   │
   ▼
Files become accessible
```

## Inode

```text
File
 │
 ▼
Inode
 │
 ├── owner
 ├── permissions
 ├── timestamps
 ├── size
 │
 └── pointers to data
```

## Swap

```text
RAM fills
   │
   ▼
Inactive pages
   │
   ▼
Swap space
```

---

## Key Takeaway

**Linux storage management revolves around disks, partitions, file systems, and mount points. File metadata is tracked through inodes, while `fdisk` helps inspect and manage partitions. The `mount` and `umount` commands attach and detach file systems from Linux's directory hierarchy, and `/etc/fstab` defines persistent mounts. Swap provides additional memory-management space by moving inactive memory pages from RAM to storage.**
