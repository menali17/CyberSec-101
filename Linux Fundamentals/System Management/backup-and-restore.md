# Backup and Restore

Backups protect our data against:

```text
Data loss
Corruption
Accidental deletion
Hardware failure
System problems
```

Linux provides several tools for creating, transferring, and restoring backups.

This section focuses primarily on:

```text
Rsync
Duplicity
Deja Dup
```

The most important tool for this section is:

```text
rsync
```

---

# Backup Tools

## Rsync

`rsync` is an open-source synchronization and file-transfer tool.

It can copy data:

```text
Locally
or
Across a network
```

One of its main advantages is efficiency.

Instead of unnecessarily transferring everything again, Rsync can synchronize changes between the source and destination.

Conceptually:

```text
SOURCE                    BACKUP

file1.txt                 file1.txt
file2.txt ── changed ──►  file2.txt
file3.txt                 file3.txt

        Only necessary changes
        need to be transferred
```

This makes Rsync useful for:

```text
Backups
Server synchronization
File transfers
Incremental synchronization
```

---

# Duplicity

`Duplicity` provides backup functionality with an emphasis on encryption.

The material describes it as building on Rsync while adding encryption capabilities.

This makes it useful when backups contain sensitive information.

Conceptually:

```text
Data
 │
 ▼
Backup
 │
 ▼
Encryption
 │
 ▼
Remote storage
```

Possible destinations mentioned include:

```text
Remote servers
FTP servers
Cloud storage
Amazon S3
```

---

# Deja Dup

`Deja Dup` provides a more user-friendly graphical backup interface.

Instead of requiring us to work primarily through terminal commands, it provides a GUI for:

```text
Creating backups
Restoring backups
Configuring backup options
```

The material also describes support for encrypted backups.

A useful mental model is:

```text
Rsync
→ Command-line synchronization/backup

Duplicity
→ Backup + encryption

Deja Dup
→ User-friendly graphical backup
```

---

# Backup Encryption

Protecting the backup itself is important.

If an attacker obtains an unencrypted backup, the backup may expose the same sensitive information as the original system.

The material mentions additional Linux encryption technologies such as:

```text
GnuPG
eCryptfs
LUKS
```

So:

```text
Backup
≠ automatically secure

Encrypted backup
→ better protection if storage is compromised
```

---

# Installing Rsync

On Ubuntu, we can install Rsync using APT:

```bash
menali@htb[/htb]$ sudo apt install rsync -y
```

Breaking it down:

```text
sudo
→ elevated privileges

apt install
→ install package

rsync
→ package name

-y
→ automatically confirm
```

---

# Basic Rsync Syntax

The basic mental model is:

```text
rsync [OPTIONS] SOURCE DESTINATION
```

Or simply:

```text
SOURCE
   │
   │ rsync
   ▼
DESTINATION
```

The source is what we want to copy.

The destination is where we want the data to go.

---

# Remote Backup with Rsync

The material gives:

```bash
rsync -av /path/to/mydirectory user@backup_server:/path/to/backup/directory
```

Breaking it down:

```text
rsync
│
└── synchronization program

-av
│
├── -a → archive
└── -v → verbose

/path/to/mydirectory
│
└── local source

user@backup_server:/path/to/backup/directory
│
└── remote destination
```

Conceptually:

```text
LOCAL MACHINE                         BACKUP SERVER

/path/to/mydirectory
        │
        │ rsync
        └──────────────────────────► /path/to/backup/directory
```

---

# `-a` — Archive Mode

The option:

```text
-a
```

means:

```text
archive
```

Archive mode preserves important file attributes.

The material specifically mentions:

```text
Permissions
Timestamps
Other original file attributes
```

This is useful for backups because we generally want the copied files to preserve their metadata.

---

# `-v` — Verbose

The option:

```text
-v
```

means:

```text
verbose
```

It displays more information about what Rsync is doing.

So:

```bash
rsync -av ...
```

can be mentally read as:

> Synchronize the files while preserving their attributes and show us what is happening.

---

# Compression with `-z`

The material introduces:

```text
-z
```

for compression.

Example:

```bash
rsync -avz ...
```

So:

```text
-a → archive

-v → verbose

-z → compression
```

These are commonly combined as:

```text
-avz
```

---

# Advanced Rsync Backup

The material provides:

```bash
rsync -avz --backup --backup-dir=/path/to/backup/folder --delete /path/to/mydirectory user@backup_server:/path/to/backup/directory
```

This looks complicated, but we can read it piece by piece.

```text
rsync
│
├── -a
│   └── archive
│
├── -v
│   └── verbose
│
├── -z
│   └── compression
│
├── --backup
│   └── preserve backup copies
│
├── --backup-dir=...
│   └── location for backup copies
│
├── --delete
│   └── remove destination files no longer in source
│
├── SOURCE
│
└── DESTINATION
```

---

# `--backup`

The option:

```text
--backup
```

keeps backup copies of files affected by the synchronization.

The material combines it with:

```text
--backup-dir=/path/to/backup/folder
```

to specify where those backup copies should be stored.

---

# `--delete`

The option:

```text
--delete
```

is particularly important to understand.

Suppose we have:

```text
SOURCE

a.txt
b.txt
```

But the destination contains:

```text
DESTINATION

a.txt
b.txt
old.txt
```

`old.txt` no longer exists in the source.

With synchronization using:

```text
--delete
```

the destination is adjusted so that the obsolete file is removed.

Result:

```text
DESTINATION

a.txt
b.txt
```

Therefore:

```text
--delete
→ remove destination files that no longer exist in the source
```

This option should be used carefully.

---

# Restoring a Backup

Backup is essentially:

```text
LOCAL → BACKUP SERVER
```

Restore reverses the direction:

```text
BACKUP SERVER → LOCAL
```

The material gives:

```bash
rsync -av user@remote_host:/path/to/backup/directory /path/to/mydirectory
```

Notice the source and destination:

```text
user@remote_host:/path/to/backup/directory
│
└── SOURCE

/path/to/mydirectory
│
└── DESTINATION
```

Conceptually:

```text
REMOTE BACKUP                       LOCAL MACHINE

backup/directory
       │
       │ rsync
       └──────────────────────────► mydirectory
```

This illustrates an important Rsync concept:

> Rsync itself is not inherently "backup" or "restore." The direction of the source and destination determines what we are doing.

---

# Backup vs Restore

### Backup

```bash
rsync -av /local/data user@server:/backup/data
```

```text
LOCAL → REMOTE
```

### Restore

```bash
rsync -av user@server:/backup/data /local/data
```

```text
REMOTE → LOCAL
```

The structure is still:

```text
rsync SOURCE DESTINATION
```

---

# Encrypted Rsync

When transferring backups over a network, we may want the communication to be encrypted.

The material combines Rsync with SSH:

```bash
rsync -avz -e ssh /path/to/mydirectory user@backup_server:/path/to/backup/directory
```

The important addition is:

```text
-e ssh
```

This tells Rsync to use SSH for the remote connection.

Conceptually:

```text
LOCAL MACHINE
      │
      │
      ▼
    Rsync
      │
      │ SSH encrypted connection
      ▼
BACKUP SERVER
```

---

# Why SSH?

Without appropriate transport protection, sensitive data transferred across a network could potentially be exposed.

SSH provides an encrypted communication channel.

Therefore:

```text
Rsync
→ handles synchronization

SSH
→ protects the network connection
```

Together:

```text
Rsync + SSH
→ secure remote synchronization
```

---

# Rsync over SSH

The complete example is:

```bash
rsync -avz -e ssh /path/to/mydirectory user@backup_server:/path/to/backup/directory
```

Breaking it down:

```text
rsync
→ synchronization

-a
→ archive

-v
→ verbose

-z
→ compression

-e ssh
→ use SSH for remote connection

/path/to/mydirectory
→ source

user@backup_server:/path/to/backup/directory
→ destination
```

---

# Automatic Backups

Manually executing:

```bash
rsync ...
```

every hour would be inconvenient.

This is where the previous **Task Scheduling** section becomes useful.

We can combine:

```text
Rsync
+
Cron
```

to automate backups.

Conceptually:

```text
Cron
 │
 │ scheduled time reached
 ▼
Backup script
 │
 ▼
Rsync
 │
 ▼
Backup server
```

---

# Why SSH Authentication Matters

Suppose Cron executes:

```bash
rsync -avz -e ssh ...
```

automatically.

If SSH stops and asks:

```text
Password:
```

there is nobody interacting with the terminal to enter it.

Therefore, the material configures:

```text
SSH key-based authentication
```

This allows the automated process to authenticate without requiring interactive password entry.

---

# Generate an SSH Key Pair

The material uses:

```bash
ssh-keygen -t rsa -b 2048
```

Breaking it down:

```text
ssh-keygen
→ generate SSH keys

-t rsa
→ use RSA

-b 2048
→ use a 2048-bit RSA key
```

The default private key location mentioned is:

```text
~/.ssh/id_rsa
```

Conceptually, key generation creates:

```text
Private Key
+
Public Key
```

The private key remains on our machine.

The public key can be placed on the remote server.

---

# Copy the Public Key

The material uses:

```bash
ssh-copy-id user@backup_server
```

This copies our public SSH key to the remote system for authentication.

Conceptually:

```text
OUR MACHINE                     BACKUP SERVER

Private key
    │
    │
Public key ───────────────────► authorized access
```

After configuration, SSH can authenticate using the key instead of requiring us to manually enter the account password every time.

---

# Creating the Backup Script

The material creates:

```text
RSYNC_Backup.sh
```

containing:

```bash
#!/bin/bash

rsync -avz -e ssh /path/to/mydirectory user@backup_server:/path/to/backup/directory
```

The first line:

```bash
#!/bin/bash
```

is the **shebang**.

It indicates that the script should be interpreted using Bash.

Then:

```bash
rsync -avz -e ssh ...
```

performs the synchronization.

---

# Making the Script Executable

The script needs execute permission.

The material uses:

```bash
chmod +x RSYNC_Backup.sh
```

Recall from Permission Management:

```text
chmod
→ change permissions

+x
→ add execute permission
```

So:

```bash
chmod +x RSYNC_Backup.sh
```

allows the script to be executed.

---

# Automating with Cron

We edit our user's crontab using:

```bash
crontab -e
```

Then add:

```text
0 * * * * /path/to/RSYNC_Backup.sh
```

Recall the Cron format:

```text
MIN HOUR DOM MONTH DOW COMMAND
```

Breaking down:

```text
0
│
└── minute 0

*
│
└── every hour

*
│
└── every day of month

*
│
└── every month

*
│
└── every day of week
```

Therefore:

```text
0 * * * *
```

means:

> Run at minute `0` of every hour.

For example:

```text
10:00
11:00
12:00
13:00
...
```

---

# Complete Automatic Backup Workflow

This section combines several concepts we have already studied:

```text
                 CRON
                   │
                   │ Every hour
                   ▼
          RSYNC_Backup.sh
                   │
                   ▼
                 RSYNC
                   │
                   │ SSH
                   ▼
             BACKUP SERVER
```

The components are:

```text
Cron
→ WHEN

Bash script
→ WHAT TO EXECUTE

Rsync
→ WHAT TO SYNCHRONIZE

SSH
→ SECURE REMOTE CONNECTION
```

---

# Local Rsync Practice

The material suggests practicing with two directories:

```text
to_backup
```

and:

```text
synced_backup
```

Conceptually:

```text
to_backup/
    │
    │ rsync
    ▼
synced_backup/
```

This allows us to understand synchronization without requiring a separate physical server.

The exercise then suggests using:

```text
127.0.0.1
```

as the remote address.

Recall:

```text
127.0.0.1
→ loopback / our own machine
```

So the same computer can act as both sides during testing.

---

# How Previous Topics Connect

This section combines many concepts from earlier Linux Fundamentals sections.

### Package Management

```bash
sudo apt install rsync -y
```

### Permissions

```bash
chmod +x RSYNC_Backup.sh
```

### SSH

```bash
ssh-copy-id user@backup_server
```

### Bash Scripts

```bash
#!/bin/bash
```

### Task Scheduling

```text
0 * * * * /path/to/RSYNC_Backup.sh
```

### Networking

```text
user@backup_server:/path
```

The complete idea becomes:

```text
Install tool
    │
    ▼
Create backup command
    │
    ▼
Secure connection with SSH
    │
    ▼
Put command in script
    │
    ▼
Make script executable
    │
    ▼
Schedule with Cron
    │
    ▼
Automatic remote backups
```

---

# Quick Reference

| Command / Option | Purpose                                     |
| ---------------- | ------------------------------------------- |
| `rsync`          | Synchronize files/directories               |
| `-a`             | Archive mode                                |
| `-v`             | Verbose output                              |
| `-z`             | Compression                                 |
| `-e ssh`         | Use SSH                                     |
| `--backup`       | Keep backup copies                          |
| `--backup-dir`   | Specify backup directory                    |
| `--delete`       | Delete destination files absent from source |
| `ssh-keygen`     | Generate SSH keys                           |
| `ssh-copy-id`    | Copy public key to remote system            |
| `chmod +x`       | Make script executable                      |
| `crontab -e`     | Edit scheduled cron jobs                    |

---

# Commands to Remember First

Basic remote synchronization:

```bash
rsync -av /source user@server:/destination
```

Secure remote synchronization:

```bash
rsync -avz -e ssh /source user@server:/destination
```

Restore:

```bash
rsync -av user@server:/backup /destination
```

Generate SSH keys:

```bash
ssh-keygen -t rsa -b 2048
```

Copy public key:

```bash
ssh-copy-id user@server
```

Make backup script executable:

```bash
chmod +x RSYNC_Backup.sh
```

Edit Cron:

```bash
crontab -e
```

Schedule every hour:

```text
0 * * * * /path/to/RSYNC_Backup.sh
```

---

# Essential Mental Model

Do not try to memorize the long Rsync command immediately.

Remember:

```text
rsync SOURCE DESTINATION
```

Then add options as needed:

```text
-a
→ preserve attributes

-v
→ show details

-z
→ compression

-e ssh
→ use SSH
```

So:

```bash
rsync -avz -e ssh SOURCE DESTINATION
```

can be mentally read as:

> Synchronize SOURCE to DESTINATION, preserving attributes, showing progress, using compression, and communicating through SSH.

---

# Rsync Direction

This is especially important:

```text
rsync SOURCE DESTINATION
```

Backup:

```text
LOCAL → REMOTE
```

Restore:

```text
REMOTE → LOCAL
```

We determine the operation by looking at **which side is the source and which side is the destination**.

---

## Key Takeaway

**Rsync synchronizes files and directories between locations and is particularly useful for backups because it can efficiently transfer changes while preserving file attributes. For remote backups, Rsync can use SSH to protect data in transit. By combining SSH key-based authentication, a Bash script, and Cron, we can create automated backups that run without manual interaction.**
