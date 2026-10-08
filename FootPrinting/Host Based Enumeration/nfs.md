# NFS (Network File System)

---

## Overview

Network File System (NFS) is a protocol that allows us to access remote filesystems over a network as if they were stored locally.

NFS is primarily used in Linux and Unix environments and serves a similar purpose to SMB.

### NFS vs. SMB

| SMB | NFS |
|---|---|
| Commonly used in Windows | Commonly used in Linux/Unix |
| TCP port 445 | Primarily TCP port 2049 |
| Uses network shares | Uses exported directories |
| Accessed with tools like smbclient | Mounted as a local directory |

---

## NFS Versions

- **NFSv2:** Older version, initially using UDP.
- **NFSv3:** Improved file transfer capabilities and error reporting.
- **NFSv4:** Supports Kerberos, ACLs, stateful operations, and improved security.
- **NFSv4.1:** Adds features for scalable and parallel file access.

NFS commonly uses ONC-RPC for communication.

Important ports:

- **111 TCP/UDP:** rpcbind, commonly used with NFSv3.
- **2049 TCP:** Main NFS service port.

---

## Authentication and Permissions

Traditional NFS configurations rely on Unix user and group identifiers.

- **UID (User ID):** Identifies a user.
- **GID (Group ID):** Identifies a group.

With AUTH_SYS, the server relies on user and group identifiers supplied by the client.

Incorrect trust relationships or permission configurations may expose sensitive files.

NFSv4 can also support stronger authentication mechanisms, such as Kerberos.

---

## NFS Configuration

The main configuration file is:

`/etc/exports`

This file defines which directories are exported and which clients can access them.

Example:

```bash
/mnt/nfs 10.129.14.0/24(sync,no_subtree_check)
```

- `/mnt/nfs`: Exported directory.
- `10.129.14.0/24`: Authorized client subnet.
- `sync`: Uses synchronous writes.
- `no_subtree_check`: Disables subtree checking.

### Important Options

| Option | Description |
|---|---|
| `rw` | Read and write access |
| `ro` | Read-only access |
| `sync` | Synchronous writes |
| `async` | Asynchronous writes |
| `secure` | Requires privileged client source ports |
| `insecure` | Allows non-privileged client source ports |
| `root_squash` | Maps remote root requests to an anonymous identity |
| `no_root_squash` | Preserves remote root identity |

### Dangerous Configurations

**no_root_squash** can be particularly dangerous.

When enabled, a client operating as root may retain UID 0 privileges when accessing exported files.

This can create security risks when combined with writable exports and insufficient access restrictions.

---

## NFS Enumeration

### Nmap

We can identify NFS services using:

```bash
nmap -sV -sC -p111,2049 <IP>
```

We can also execute NFS-specific NSE scripts:

```bash
nmap --script nfs* -sV -p111,2049 <IP>
```

Useful scripts include:

- `nfs-ls`: Lists accessible files.
- `nfs-showmount`: Displays exported directories.
- `nfs-statfs`: Retrieves filesystem statistics.

### Show Available Exports

```bash
showmount -e <IP>
```

This command lists the exported directories advertised by the NFS server, when supported.

---

## Mounting NFS Shares

We can mount an accessible NFS export to a local directory.

### Create a Mount Point

```bash
mkdir target-NFS
```

### Mount the Share

```bash
sudo mount -t nfs <IP>:/mnt/nfs ./target-NFS
```

### Inspect Files

```bash
ls -la target-NFS
```

We can also display numerical user and group identifiers:

```bash
ls -n target-NFS
```

- `ls -l`: Displays permissions, owners, and groups.
- `ls -n`: Displays numerical UIDs and GIDs.

### Unmount the Share

```bash
sudo umount ./target-NFS
```

---

## Security Considerations

Potential NFS security issues include:

- Excessive read/write permissions.
- Exports accessible to untrusted clients.
- Insecure UID/GID trust relationships.
- Misconfigured `no_root_squash`.
- Sensitive information exposed through shared directories.

For example, an improperly configured NFS export may expose SSH private keys or other sensitive files.

---

## Key Takeaways

- NFS allows remote directories to be mounted as local filesystems.
- NFS is primarily used in Linux and Unix environments.
- TCP 2049 is the main NFS port.
- `/etc/exports` defines exported directories and access options.
- `showmount -e` helps discover NFS exports.
- `mount` allows us to access remote files through a local directory.
- UID/GID mapping plays an important role in NFS permissions.
- `no_root_squash` can introduce serious security risks.

**Our objective is to discover exposed NFS shares, understand their access permissions, and identify potential misconfigurations.**