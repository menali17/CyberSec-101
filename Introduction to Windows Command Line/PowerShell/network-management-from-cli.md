
# Networking Management from the CLI

---

## Overview

PowerShell provides tools for inspecting, configuring, and troubleshooting network connections on Windows hosts. It also allows us to enable and manage remote access through technologies such as **SSH** and **Windows Remote Management (WinRM)**.

In this section, we will learn how to:

- Identify common networking protocols used in Windows environments.
- Inspect IP addresses, network interfaces, DNS settings, and routing information.
- Discover neighboring hosts and active network connections.
- Manage network adapters using PowerShell cmdlets.
- Test network connectivity.
- Configure and access Windows hosts remotely through SSH and WinRM.

The HTB scenario involves examining Mr. Tanaka's workstation, verifying its network configuration, and preparing it for remote administration.

---

## 1. Common Windows Networking Protocols

Windows relies on standard networking protocols alongside several technologies commonly associated with enterprise and Active Directory environments.

| Protocol | Description |
|---|---|
| **SMB** | Provides file, printer, and resource sharing between computers. |
| **NetBIOS** | Provides legacy naming and communication functionality in Windows networks. |
| **LDAP** | A directory access protocol commonly used to interact with Active Directory. |
| **LLMNR** | Resolves hostnames on the local network when conventional DNS resolution is unavailable. |
| **DNS** | Translates domain names and hostnames into IP addresses. |
| **HTTP/HTTPS** | Supports communication with web applications and services. |
| **Kerberos** | Provides ticket-based authentication, particularly in Active Directory environments. |
| **WinRM** | Enables remote Windows administration through the WS-Management protocol. |
| **RDP** | Provides graphical remote desktop access. |
| **SSH** | Provides encrypted remote shell access and supports secure file transfers. |

### SMB

Server Message Block (SMB) allows computers to share files, printers, and other network resources.

Windows uses SMB extensively in enterprise environments. Linux systems can provide compatible SMB functionality through Samba.

### LDAP and Kerberos

LDAP and Kerberos are particularly important when working with Active Directory.

LDAP allows us to query and interact with directory objects such as users, groups, and computers.

Kerberos provides ticket-based authentication and is widely used when users access domain resources.

### WinRM, RDP, and SSH

These three technologies provide different methods of accessing Windows systems remotely.

| Technology | Primary purpose | Common default ports |
|---|---|---|
| SSH | Encrypted command-line access | TCP 22 |
| RDP | Graphical remote desktop | TCP 3389 |
| WinRM | Remote management and PowerShell Remoting | TCP 5985 / 5986 |

---

## 2. Local Network Enumeration

Before changing network settings, we should understand the host's current configuration.

The HTB module begins with several traditional Windows networking commands that also work inside PowerShell.

### IPConfig

We can display the current network configuration using:

```powershell
ipconfig
```

The output includes information such as:

- IPv4 and IPv6 addresses.
- Subnet masks.
- Default gateways.
- Connection-specific DNS suffixes.

For more detailed information:

```powershell
ipconfig /all
```

This additionally displays network adapter descriptions, physical addresses, DHCP settings, lease information, and configured DNS servers.

### Identifying Multiple Network Interfaces

In the HTB scenario, Mr. Tanaka's workstation has two network interfaces:

| Interface | IPv4 address | Network |
|---|---|---|
| Ethernet0 | `10.129.203.105` | HTB-accessible network |
| Ethernet2 | `172.16.5.100` | Internal network |

A computer connected to multiple networks is commonly described as **dual-homed**.

This is relevant during penetration testing because a compromised dual-homed host may have connectivity to network segments that are otherwise inaccessible from our original position.

However, having multiple interfaces does not automatically mean that the computer forwards traffic between those networks.

---

## 3. Inspecting the ARP Cache

Address Resolution Protocol (ARP) resolves IPv4 addresses to MAC addresses on a local network.

Windows maintains an ARP cache containing recently learned address mappings.

We can inspect it using:

```powershell
arp -a
```

Example:

```text
Interface: 172.16.5.100

Internet Address    Physical Address      Type
172.16.5.155        00-50-56-b9-e2-30     dynamic
172.16.5.255        ff-ff-ff-ff-ff-ff     static
```

In the HTB scenario, `172.16.5.155` is the domain controller for `greenhorn.corp`.

The ARP cache helps us identify neighboring IPv4 hosts with which the system has recently communicated or whose addresses it has resolved.

It is not a complete inventory of every device connected to the network.

---

## 4. DNS Enumeration

DNS allows us to resolve hostnames into IP addresses.

Windows includes the `nslookup` utility, which can be used from CMD or PowerShell.

For example:

```powershell
nslookup ACADEMY-ICL-DC
```

In the HTB environment, the domain controller resolves to:

```text
Name:    ACADEMY-ICL-DC.greenhorn.corp
Address: 172.16.5.155
```

This helps us verify that DNS resolution is functioning and identify the IP addresses associated with particular hosts.

DNS is particularly important in Active Directory environments because clients rely on it to locate domain controllers and other domain services.

---

## 5. Inspecting Network Connections with Netstat

The `netstat` utility displays network connections and listening ports.

We can execute:

```powershell
netstat -an
```

The parameters mean:

| Parameter | Description |
|---|---|
| `-a` | Displays active connections and listening ports. |
| `-n` | Displays addresses and ports numerically instead of resolving their names. |

Example output:

```text
Proto  Local Address       Foreign Address       State
TCP    0.0.0.0:22          0.0.0.0:0             LISTENING
TCP    0.0.0.0:445         0.0.0.0:0             LISTENING
TCP    0.0.0.0:3389        0.0.0.0:0             LISTENING
TCP    0.0.0.0:5985        0.0.0.0:0             LISTENING
TCP    10.129.203.105:22   10.10.14.19:32557     ESTABLISHED
```

### Understanding Connection States

`LISTENING` means that a process is waiting for incoming connections on the specified TCP port.

`ESTABLISHED` means that a TCP connection has been established between two endpoints.

In this example, the final entry represents an established SSH connection to the workstation.

### Common Windows Ports

| Port | Service |
|---|---|
| TCP 22 | SSH |
| TCP 135 | Microsoft RPC |
| TCP 139 | NetBIOS Session Service |
| TCP 445 | SMB |
| TCP 3389 | RDP |
| TCP 5985 | WinRM over HTTP |
| TCP 5986 | WinRM over HTTPS |

A listening port indicates that a service is accepting connections on an interface. It does not necessarily mean the service is reachable from every network, because firewalls and routing restrictions may apply.

---

## 6. PowerShell Networking Cmdlets

Although traditional executables such as `ipconfig` and `netstat` are useful, PowerShell also provides dedicated networking cmdlets.

These allow us to retrieve structured network information and manipulate adapter configurations.

| Cmdlet | Purpose |
|---|---|
| `Get-NetIPInterface` | Retrieves IP interface configuration and properties. |
| `Get-NetIPAddress` | Retrieves configured IPv4 and IPv6 addresses. |
| `Get-NetNeighbor` | Retrieves neighboring host entries, similar to `arp -a`. |
| `Get-NetRoute` | Displays routing information. |
| `Set-NetAdapter` | Modifies supported network adapter properties. |
| `Set-NetIPInterface` | Modifies interface settings such as DHCP and MTU. |
| `New-NetIPAddress` | Creates a new IP address configuration. |
| `Set-NetIPAddress` | Modifies properties of an existing IP address. |
| `Disable-NetAdapter` | Disables a network adapter. |
| `Enable-NetAdapter` | Enables a network adapter. |
| `Restart-NetAdapter` | Restarts a network adapter. |
| `Test-NetConnection` | Performs network connectivity diagnostics. |

### Get-NetIPInterface

We can inspect network interfaces using:

```powershell
Get-NetIPInterface
```

The output includes:

- Interface index (`ifIndex`).
- Interface name (`InterfaceAlias`).
- IPv4 or IPv6 address family.
- MTU.
- Interface metric.
- DHCP status.
- Connection state.

The interface index is particularly useful because other networking cmdlets accept it as a parameter.

### Get-NetIPAddress

Once we identify an interface, we can inspect its configured addresses.

For example:

```powershell
Get-NetIPAddress -InterfaceIndex 25
```

The HTB example uses interface index `25` to retrieve the configuration of a Wi-Fi adapter.

Its output includes:

```text
IPAddress      : 192.168.86.211
InterfaceIndex : 25
InterfaceAlias : Wi-Fi
AddressFamily  : IPv4
PrefixLength   : 24
PrefixOrigin   : Dhcp
```

A prefix length of `/24` corresponds to the IPv4 subnet mask `255.255.255.0`.

We can also identify whether an address was assigned automatically through DHCP.

---

## 7. Modifying Network Settings

PowerShell allows administrators to modify IP interface properties and assign addresses.

These operations generally require elevated privileges.

### Disabling DHCP

We can disable DHCP on a particular interface using:

```powershell
Set-NetIPInterface -InterfaceIndex 25 -Dhcp Disabled
```

This changes how the interface obtains its IP configuration.

### Configuring an IP Address

The HTB scenario then demonstrates assigning a static address.

For a new static IP address, `New-NetIPAddress` is the appropriate cmdlet:

```powershell
New-NetIPAddress `
    -InterfaceIndex 25 `
    -IPAddress 10.10.100.54 `
    -PrefixLength 24
```

`Set-NetIPAddress` is used when modifying properties of an IP address that already exists.

We can verify the resulting configuration using:

```powershell
Get-NetIPAddress -InterfaceIndex 25
```

**Important:** Modifying the configuration of a network adapter used for remote access can disconnect our current session. We should avoid changing the active interface without an alternative method of recovering access.

### Restarting an Adapter

After making network configuration changes, we may need to restart the relevant adapter.

For example:

```powershell
Restart-NetAdapter -Name 'Ethernet 3'
```

This temporarily interrupts connectivity through the selected adapter.

---

## 8. Testing Network Connectivity

The `Test-NetConnection` cmdlet provides network diagnostic capabilities.

For example:

```powershell
Test-NetConnection
```

The HTB example displays information such as:

```text
RemoteAddress          : 13.107.4.52
InterfaceAlias         : Ethernet 3
SourceAddress          : 10.10.100.54
PingSucceeded          : True
PingReplyDetails (RTT) : 44 ms
```

We can also specify a particular destination:

```powershell
Test-NetConnection -ComputerName 172.16.5.155
```

The cmdlet supports additional diagnostics, including testing specific TCP ports and performing route-related checks.

It can help us determine whether a host is reachable after changing network configurations.

---

## 9. Remote Access Through SSH

SSH provides encrypted remote command-line access to a host.

Windows supports OpenSSH through optional client and server components.

### Checking OpenSSH Installation

We can inspect the installed components using:

```powershell
Get-WindowsCapability -Online |
    Where-Object Name -like 'OpenSSH*'
```

The output identifies the OpenSSH client and server components and indicates whether they are installed.

### Installing the OpenSSH Server

On a supported Windows system, we can install the SSH server using:

```powershell
Add-WindowsCapability `
    -Online `
    -Name OpenSSH.Server~~~~0.0.1.0
```

### Starting the SSH Service

After installation, we can start the SSH server:

```powershell
Start-Service sshd
```

To configure automatic startup:

```powershell
Set-Service -Name sshd -StartupType Automatic
```

The `sshd` service accepts incoming SSH connections, subject to its configuration and applicable firewall rules.

### Connecting to a Windows Host

From Windows or Linux, we can initiate an SSH connection using:

```bash
ssh htb-student@10.129.224.248
```

After successful authentication, the Windows host may provide a CMD session.

We can enter PowerShell by executing:

```cmd
powershell
```

This allows us to use PowerShell commands over the existing SSH connection.

---

## 10. Windows Remote Management (WinRM)

**Windows Remote Management (WinRM)** is Microsoft's implementation of the WS-Management protocol.

It allows administrators to remotely execute commands, inspect system configuration, and automate administrative operations.

WinRM commonly listens on:

| Port | Transport |
|---|---|
| TCP 5985 | HTTP |
| TCP 5986 | HTTPS |

### Configuring WinRM

The HTB module demonstrates configuring WinRM using:

```powershell
winrm quickconfig
```

Depending on the host's existing configuration, the command may start the WinRM service, configure a listener, and create firewall exceptions.

Additional authorization settings may be necessary for remote administrative access.

**Security consideration:** WinRM should only be accessible to authorized management systems. HTTPS, appropriate authentication, firewall restrictions, and least-privilege permissions help reduce its exposure.

---

## 11. Testing WinRM Connectivity

PowerShell provides the `Test-WSMan` cmdlet for checking whether a Windows host responds to WS-Management requests.

For example:

```powershell
Test-WSMan -ComputerName "10.129.224.248"
```

This sends a WS-Management identification request to the specified host.

If the service responds, PowerShell displays protocol and service information.

The HTB module also demonstrates authenticated testing:

```powershell
Test-WSMan `
    -ComputerName "10.129.224.248" `
    -Authentication Negotiate
```

Authenticated requests may provide more detailed information about the remote Windows system.

A successful `Test-WSMan` result establishes that the service responds, but it does not automatically mean our account is authorized to establish an administrative session.

---

## 12. Establishing PowerShell Remote Sessions

We can use `Enter-PSSession` to establish an interactive PowerShell session with a remote host.

The HTB module demonstrates:

```powershell
Enter-PSSession `
    -ComputerName 10.129.224.248 `
    -Credential htb-student `
    -Authentication Negotiate
```

### Understanding the Parameters

| Parameter | Description |
|---|---|
| `-ComputerName` | Specifies the remote computer. |
| `-Credential` | Provides the account used for authentication. |
| `-Authentication` | Specifies the authentication mechanism. |

When authentication and authorization succeed, we can execute PowerShell commands on the remote host.

For example:

```powershell
$PSVersionTable
```

This displays information about the PowerShell version running in the remote session.

We can leave an interactive session using:

```powershell
Exit-PSSession
```

**Platform note:** The HTB material also discusses using PowerShell from Linux. PowerShell is cross-platform, but WinRM-based remoting support depends on the PowerShell version, operating system, and installed remoting components.

---

## Command Summary

| Command | Purpose |
|---|---|
| `ipconfig` | Displays basic network configuration. |
| `ipconfig /all` | Displays detailed adapter and network settings. |
| `arp -a` | Displays the ARP cache. |
| `nslookup` | Performs DNS queries. |
| `netstat -an` | Displays network connections and listening ports numerically. |
| `Get-NetIPInterface` | Retrieves interface configuration. |
| `Get-NetIPAddress` | Retrieves configured IP addresses. |
| `Get-NetNeighbor` | Retrieves neighboring host entries. |
| `Get-NetRoute` | Displays the routing table. |
| `Set-NetIPInterface` | Modifies IP interface settings. |
| `New-NetIPAddress` | Creates an IP address configuration. |
| `Restart-NetAdapter` | Restarts a network adapter. |
| `Test-NetConnection` | Performs connectivity diagnostics. |
| `Get-WindowsCapability` | Lists available or installed Windows capabilities. |
| `Add-WindowsCapability` | Installs an optional Windows capability. |
| `Start-Service sshd` | Starts the SSH server service. |
| `winrm quickconfig` | Configures basic WinRM functionality. |
| `Test-WSMan` | Tests WS-Management connectivity. |
| `Enter-PSSession` | Establishes an interactive remote PowerShell session. |

---

## Key Takeaways

- Windows supports traditional networking utilities alongside dedicated PowerShell networking cmdlets.
- `ipconfig /all` provides detailed information about IP addresses, DHCP, DNS, and network adapters.
- `arp -a` helps us identify neighboring IPv4 hosts whose addresses have been resolved.
- `nslookup` verifies DNS resolution, while `netstat -an` identifies network connections and listening ports.
- PowerShell's networking cmdlets allow us to inspect and modify network interfaces using structured objects.
- `Test-NetConnection` provides connectivity and network diagnostic capabilities.
- OpenSSH allows encrypted command-line access to Windows hosts.
- WinRM enables remote Windows administration and PowerShell Remoting.
- `Test-WSMan` checks WinRM connectivity, while `Enter-PSSession` establishes an interactive remote session.
- Dual-homed hosts may provide connectivity to multiple network segments, making their network configuration particularly relevant during security assessments.
