# Linux Remote Management Protocols

---

## Overview

Linux and Unix systems provide different services for remote administration, command execution, and file transfers.

In this section, we focus on:

- SSH
- Rsync
- R-Services

These services may expose sensitive information or provide remote access when improperly configured.

---

## SSH (Secure Shell)

SSH allows us to establish encrypted remote connections.

### Default Port

- **TCP 22**

### Main Features

- Remote shell access.
- Remote command execution.
- Secure file transfers.
- Port forwarding.
- Public-key authentication.

### Authentication Methods

SSH supports different authentication mechanisms, including:

- Password authentication.
- Public-key authentication.
- Host-based authentication.
- Keyboard-interactive authentication.
- GSSAPI authentication.

### Public-Key Authentication

SSH can use asymmetric cryptography to authenticate users.

- **Private key:** Remains secret on our local machine.
- **Public key:** Can be stored on the remote server.
- **Passphrase:** Can protect the private key at rest.

During authentication, we prove possession of the private key without transmitting it.

SSH host keys are also used to authenticate the remote server.

### SSH Configuration

A common OpenSSH server configuration file is:

```text
/etc/ssh/sshd_config
```

Security-relevant options:

| Setting | Description |
|---|---|
| `PasswordAuthentication` | Controls password authentication |
| `PermitEmptyPasswords` | Controls empty-password authentication |
| `PermitRootLogin` | Controls root login |
| `X11Forwarding` | Controls X11 forwarding |
| `AllowTcpForwarding` | Controls TCP forwarding |
| `PermitTunnel` | Controls tunneling |

The security impact depends on the complete configuration.

### SSH Enumeration

#### Nmap

```bash
nmap -sV -p22 <IP>
```

#### SSH-Audit

```bash
ssh-audit <IP>
```

SSH-Audit can identify:

- SSH banners and versions.
- Key exchange algorithms.
- Host key algorithms.
- Encryption algorithms.
- Potentially insecure cryptographic configurations.

#### Verbose SSH Connection

```bash
ssh -v <user>@<IP>
```

- `-v`: Displays detailed connection information.

We can inspect the authentication methods advertised by the server.

#### Prefer Password Authentication

```bash
ssh -v <user>@<IP> -o PreferredAuthentications=password
```

This tells our SSH client to prioritize password authentication.

---

## Rsync

Rsync is a tool for copying and synchronizing files locally or remotely.

It uses a delta-transfer algorithm to reduce unnecessary data transfers.

### Default Port

- **TCP 873** for Rsync daemon mode.

Rsync can also use SSH as its transport.

### Rsync Enumeration

#### Service Detection

```bash
nmap -sV -p873 <IP>
```

#### Banner Grabbing

```bash
nc -nv <IP> 873
```

Example daemon banner:

```text
@RSYNCD: 31.0
```

#### List Available Modules

```bash
rsync rsync://<IP>/
```

#### List Files in a Module

```bash
rsync -av --list-only rsync://<IP>/<module>
```

- `-a`: Archive mode.
- `-v`: Verbose output.
- `--list-only`: Lists files without downloading them.

#### Download Files from a Module

```bash
rsync -av rsync://<IP>/<module>/ ./downloaded-files/
```

We should only retrieve files that are within our authorized assessment scope.

### Security Considerations

Misconfigured Rsync modules may expose:

- Backup files.
- Configuration files.
- Credentials and secrets.
- SSH-related files.

Rsync daemon connections are not automatically encrypted.

---

## R-Services

R-Services are legacy Unix remote administration protocols.

They were largely replaced by SSH because of their security limitations.

### Default Ports

| Port | Service | Purpose |
|---|---|---|
| TCP 512 | rexec | Remote command execution |
| TCP 513 | rlogin | Remote login |
| TCP 514 | rsh / rcp | Remote shell and file copying |

### Common R-Commands

- `rlogin`: Remote login.
- `rsh`: Remote command execution.
- `rcp`: Remote file copying.
- `rwho`: Lists remote user sessions advertised on the network.
- `rusers`: Queries information about logged-in users.

### Trusted Hosts

R-Services may use trust relationships defined in:

```text
/etc/hosts.equiv
~/.rhosts
```

- `/etc/hosts.equiv`: System-wide trust configuration.
- `.rhosts`: Per-user trust configuration.

Improper trust rules can allow unauthorized remote access.

Wildcard entries using `+` can be especially dangerous.

### R-Services Enumeration

#### Nmap

```bash
nmap -sV -p512,513,514 <IP>
```

#### Rlogin

```bash
rlogin <IP> -l <user>
```

#### Enumerate Logged-In Users

```bash
rwho
```

```bash
rusers -al <IP>
```

These commands depend on the corresponding legacy services being available.

### Security Risks

- Lack of encryption.
- Weak host-based trust mechanisms.
- Credential interception.
- Unauthorized remote access.
- Excessively permissive trust configurations.

---

## Comparison

| Protocol | Port | Main Purpose |
|---|---|---|
| SSH | TCP 22 | Secure remote administration |
| Rsync | TCP 873 | File synchronization |
| rexec | TCP 512 | Legacy remote command execution |
| rlogin | TCP 513 | Legacy remote login |
| rsh / rcp | TCP 514 | Legacy remote commands and file copying |

---

## Enumeration Methodology

### SSH

1. Discover TCP port 22.
2. Identify the SSH banner and software version.
3. Inspect authentication methods and cryptographic algorithms.
4. Investigate potential configuration weaknesses.
5. Authenticate using valid credentials when available.

### Rsync

1. Discover TCP port 873.
2. Identify the Rsync daemon and protocol version.
3. Enumerate available modules.
4. List accessible files.
5. Assess whether sensitive information is exposed.

### R-Services

1. Discover TCP ports 512, 513, and 514.
2. Identify available legacy services.
3. Investigate permitted access and trust relationships.
4. Identify potential information disclosure or insecure access.

---

## Key Takeaways

- SSH provides encrypted remote administration.
- SSH supports password and public-key authentication.
- SSH-Audit helps us inspect SSH security configurations.
- Rsync efficiently synchronizes files and may expose remote modules.
- Rsync daemon communication is not automatically encrypted.
- R-Services are legacy protocols with significant security limitations.
- Misconfigured trust relationships may allow access without normal password authentication.

**Our objective is to understand remote management services, enumerate their exposed capabilities, and identify potentially insecure configurations.**