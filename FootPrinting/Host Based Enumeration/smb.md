# SMB (Server Message Block)

---

## Overview

Server Message Block (SMB) is a client-server protocol used to share files, directories, printers, and other network resources.

SMB is commonly associated with Windows environments, but Linux and Unix systems can also implement it through Samba.

SMB uses TCP to establish communication between clients and servers.

### Important Ports

| Port | Service |
|---|---|
| 445/TCP | SMB directly over TCP |
| 139/TCP | SMB over NetBIOS |
| 137/UDP | NetBIOS Name Service |
| 138/UDP | NetBIOS Datagram Service |

### SMB Versions

- **SMBv1 / CIFS:** Legacy protocol, considered insecure.
- **SMBv2:** Improved performance and efficiency.
- **SMBv3:** Introduced additional security features, including encryption support.

---

## Samba

Samba is an implementation of SMB for Linux and Unix systems.

It allows interoperability between Windows and Linux environments.

The main configuration file is:

`/etc/samba/smb.conf`

### SMB Shares

Shares are network resources made available by an SMB server.

Each share can have specific access permissions.

**Access Control Lists (ACLs)** define permissions for users and groups.

Share permissions and local filesystem permissions may differ.

### Important Samba Settings

| Setting | Description |
|---|---|
| `[sharename]` | Defines a shared resource |
| `path` | Filesystem location of the share |
| `browseable = yes` | Makes the share visible in listings |
| `guest ok = yes` | Allows guest connections |
| `read only = no` | Disables the read-only restriction |
| `writable = yes` | Enables write access |
| `create mask = 0777` | Defines permitted creation bits for new files |

Access also depends on filesystem permissions and other applicable restrictions.

---

## Anonymous SMB Access

Some SMB servers permit guest or anonymous access.

This may allow us to:

- Enumerate available shares.
- Browse directories.
- Download accessible files.
- Obtain information about users or groups.

Anonymous access is not necessarily a vulnerability, but excessive permissions may expose sensitive information.

---

## SMB Enumeration

### Nmap

We can scan the common SMB ports:

```bash
nmap -sV -sC -p139,445 <IP>
```

- `-sV`: Detects service versions.
- `-sC`: Executes default NSE scripts.
- `-p139,445`: Scans TCP ports 139 and 445.

### SMBclient

We use `smbclient` to list and access SMB shares.

**List available shares:**

```bash
smbclient -N -L //<IP>
```

- `-N`: Does not prompt for a password.
- `-L`: Lists available shares.

**Connect to a share:**

```bash
smbclient //<IP>/<share> -N
```

**Basic commands:**

| Command | Description |
|---|---|
| `ls` | Lists files and directories |
| `cd` | Changes directory |
| `get` | Downloads a file |
| `put` | Uploads a file, if permitted |
| `help` | Displays available commands |
| `exit` | Closes the session |

---

## RPCclient

Remote Procedure Call (RPC) allows programs to request operations from remote systems.

We can use `rpcclient` to enumerate information exposed by SMB servers.

### Connect Without Credentials

```bash
rpcclient -U "" -N <IP>
```

### Important Queries

| Query | Description |
|---|---|
| `srvinfo` | Retrieves server information |
| `enumdomains` | Lists available domains |
| `querydominfo` | Retrieves domain information |
| `netshareenumall` | Lists network shares |
| `netsharegetinfo <share>` | Retrieves details about a share |
| `enumdomusers` | Enumerates users |
| `queryuser <RID>` | Retrieves user information |
| `querygroup <RID>` | Retrieves group information |

### RID (Relative Identifier)

A RID identifies a user or group relative to a domain or local machine's SID.

We can use RIDs to retrieve information about specific accounts.

---

## Additional Enumeration Tools

### SMBMap

Used to enumerate SMB shares and identify access permissions.

### CrackMapExec

Used to perform SMB enumeration and other authorized assessment operations.

### Impacket — samrdump.py

Used to retrieve user and account information through the SAMR protocol.

### enum4linux-ng

Automates various SMB, NetBIOS, and RPC enumeration techniques.

---

## Security Considerations

Common SMB security risks include:

- Anonymous access to sensitive shares.
- Excessive read and write permissions.
- Insecure share configurations.
- Exposure of user and group information.
- Outdated SMB versions.
- Misconfigured authentication and security controls.

We should verify findings manually because automated enumeration tools may produce incomplete or inconsistent results.

---

## Key Takeaways

- SMB enables network resource sharing, especially in Windows environments.
- Samba implements SMB functionality on Linux and Unix systems.
- TCP 445 is the main port used by modern SMB.
- ACLs control access to shared resources.
- `smbclient` helps us interact with network shares.
- `rpcclient` allows us to retrieve information about users, groups, and domains.
- Anonymous access can expose information if permissions are misconfigured.
- Different enumeration tools may reveal different information.

**Our objective is to identify accessible SMB resources, understand their permissions, and discover potential security misconfigurations.**