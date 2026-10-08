# Windows Remote Management Protocols

---

## Overview

Windows provides several technologies for remote system administration.

The three main technologies covered in this section are:

- RDP (Remote Desktop Protocol)
- WinRM (Windows Remote Management)
- WMI (Windows Management Instrumentation)

These technologies allow us to access, monitor, and manage Windows systems remotely.

---

## RDP (Remote Desktop Protocol)

RDP is a Microsoft protocol that allows us to remotely access and control the graphical interface of a Windows computer.

### Default Ports

- **TCP 3389:** RDP connections.
- **UDP 3389:** Can also be used for RDP transport.

### Network Level Authentication (NLA)

NLA requires users to authenticate before a complete graphical RDP session is established.

It reduces unnecessary exposure of remote desktop resources to unauthenticated clients.

### RDP Enumeration

```bash
nmap -sV -p3389 --script rdp-enum-encryption,rdp-ntlm-info <IP>
```

- `-sV`: Detects service versions.
- `-p3389`: Scans the RDP port.
- `rdp-enum-encryption`: Enumerates supported RDP security methods.
- `rdp-ntlm-info`: Retrieves information exposed during NTLM negotiation.

### Information We May Discover

- Hostname.
- NetBIOS computer name.
- Domain information.
- Windows product version.
- Supported RDP security layers.
- NLA support.

### Detailed Packet Analysis

```bash
nmap -sV -sC -p3389 <IP> --packet-trace
```

- `--packet-trace`: Displays sent and received packets.

Network monitoring tools may detect characteristic RDP scanning traffic.

### RDP Security Assessment

The HTB lesson also introduces `rdp-sec-check.pl`, which analyzes supported RDP security mechanisms.

### Connecting with FreeRDP

```bash
xfreerdp /u:<username> /v:<IP>
```

- `/u`: Specifies the username.
- `/v`: Specifies the target server.

With valid credentials, we can access the Windows graphical desktop.

### Security Considerations

- Weak credentials.
- Missing or improperly configured NLA.
- Insecure protocol configurations.
- Untrusted or self-signed certificates.
- Exposed remote desktop access.

---

## WinRM (Windows Remote Management)

WinRM is a Windows remote management technology based on the WS-Management standard.

It allows us to administer remote Windows systems, including through PowerShell sessions.

### Default Ports

| Port | Description |
|---|---|
| TCP 5985 | WinRM over HTTP |
| TCP 5986 | WinRM over HTTPS |

WinRM over HTTP may still use message-level encryption with supported authentication mechanisms.

### WinRM Enumeration

```bash
nmap -sV -sC -p5985,5986 <IP>
```

This helps us identify potentially accessible WinRM endpoints.

### PowerShell

On Windows, we can test WS-Management connectivity using:

```powershell
Test-WSMan <IP>
```

### Evil-WinRM

Evil-WinRM is a tool used to interact with accessible Windows remote management endpoints.

```bash
evil-winrm -i <IP> -u <username> -p '<password>'
```

- `-i`: Target IP address.
- `-u`: Username.
- `-p`: Password.

Successful authentication provides a remote PowerShell session.

### Security Considerations

- Weak or reused credentials.
- Excessive administrative privileges.
- Unnecessarily exposed WinRM endpoints.
- Improper authentication and access controls.

---

## WMI (Windows Management Instrumentation)

WMI is a Windows management infrastructure based on the Common Information Model (CIM).

It provides access to information and management operations involving Windows components.

We can use WMI to retrieve or manage:

- System information.
- Hardware information.
- Processes and services.
- Operating system configuration.
- Other manageable Windows resources.

### Network Communication

Remote WMI using DCOM/RPC commonly uses:

- **TCP 135:** Initial RPC endpoint communication.
- **Dynamic RPC ports:** Subsequent communication.

Some tools also require SMB access.

### WMI Enumeration and Command Execution

The HTB lesson introduces Impacket's `wmiexec.py`.

```bash
impacket-wmiexec '<username>:<password>@<IP>' 'hostname'
```

This authenticates to a remote Windows system and executes the `hostname` command through the tool's remote execution mechanism.

Example output:

```text
ILF-SQL-01
```

The output reveals the remote computer's hostname.

### Security Considerations

- Excessive WMI permissions.
- Insecure remote access.
- Compromised administrative credentials.
- Unauthorized remote command execution.

---

## Comparison

| Technology | Main Port | Purpose |
|---|---|---|
| RDP | TCP/UDP 3389 | Remote graphical desktop |
| WinRM | TCP 5985/5986 | Remote administration and PowerShell |
| WMI | TCP 135 + dynamic RPC ports | Windows system management |

---

## Enumeration Methodology

### RDP

1. Discover TCP port 3389.
2. Identify RDP security mechanisms.
3. Enumerate available host and domain information.
4. Connect with valid credentials when authorized.

### WinRM

1. Discover TCP ports 5985 and 5986.
2. Identify the exposed remote management endpoint.
3. Test connectivity and authentication.
4. Connect using Evil-WinRM with valid credentials.

### WMI

1. Identify accessible RPC-related services.
2. Verify the required authentication and network access.
3. Connect using an appropriate WMI-capable tool.
4. Query system information or execute authorized commands.

---

## Key Takeaways

- RDP provides remote graphical access.
- WinRM supports remote administration and PowerShell.
- WMI exposes Windows system management capabilities.
- RDP commonly uses TCP/UDP 3389.
- WinRM commonly uses TCP 5985 and 5986.
- WMI using DCOM/RPC commonly begins on TCP 135.
- Nmap can reveal useful service and host information.
- Valid credentials and appropriate permissions are generally required for authenticated remote management.

**Our objective is to identify exposed Windows remote management services, understand their capabilities, and assess potentially insecure configurations.**