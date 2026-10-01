
# Gathering System Information

---

## Overview

**Host enumeration** is the process of gathering information about a target system, its configuration, users, network connections, and surrounding environment.

It is an essential step for both penetration testers and system administrators.

- **Red Team:** Identifies potential vulnerabilities, misconfigurations, accessible resources, and opportunities for privilege escalation.
- **Blue Team:** Uses system information to troubleshoot issues, secure hosts and services, and monitor the network.

Our goal is to develop a structured enumeration methodology rather than executing commands without a clear objective.

---

## Types of Information

During host enumeration, we can organize the information we collect into four main categories.

| Category | Information |
|---|---|
| General System Information | Hostname, operating system, OS version, build number, installed patches, and system configuration. |
| Networking Information | IP addresses, network interfaces, subnets, DNS servers, known hosts, and network resources. |
| Basic Domain Information | Active Directory information and details about the domain to which the host belongs. |
| User Information | Local users, groups, privileges, environment variables, running tasks, scheduled tasks, and services. |

These categories help us determine what information to prioritize during an assessment.

## Why Is Enumeration Important?

Consider an assumed-breach scenario in which we receive initial access to a Windows host through an unprivileged user account.

Our objective is to understand the environment and identify possible privilege escalation opportunities.

We should investigate several questions:

- Which user account are we currently using?
- Which groups does our user belong to?
- What privileges are available to our account?
- Which network resources can we access?
- Which tasks and services are running under our account?

Enumeration is not limited to identifying outdated software or known CVEs. Incorrect permissions, excessive privileges, and other configuration mistakes can also introduce security vulnerabilities.

A structured methodology helps us identify these weaknesses without overlooking potentially important information.

---

## Gathering General System Information

### Systeminfo

The `systeminfo` command provides a comprehensive overview of the Windows host.

```cmd
systeminfo
```

Example output:

```text
Host Name:                 DESKTOP-htb
OS Name:                   Microsoft Windows 10 Pro
OS Version:                10.0.19042 N/A Build 19042
OS Manufacturer:           Microsoft Corporation
OS Configuration:          Standalone Workstation
OS Build Type:             Multiprocessor Free
```

This command can provide information such as the operating system, build version, installed hotfixes, network configuration, and domain membership.

From a penetration testing perspective, the OS version, build number, and installed patches can help us investigate whether the host may be affected by known vulnerabilities.

### Hostname

We can retrieve the computer's hostname using:

```cmd
hostname
```

Example output:

```text
DESKTOP-htb
```

### Ver

The `ver` command displays the Windows operating system version.

```cmd
ver
```

Example output:

```text
Microsoft Windows [Version 10.0.19042.2006]
```

Together, `hostname` and `ver` provide a quick alternative to `systeminfo` when we only need basic host information.

---

## Gathering Networking Information

Understanding the network configuration of our target allows us to identify its network interfaces, connected subnets, and potentially accessible systems.

### Ipconfig

The `ipconfig` utility displays the current TCP/IP configuration of the Windows host.

```cmd
ipconfig
```

Example output:

```text
Ethernet adapter Ethernet:

   Connection-specific DNS Suffix  . : htb.local
   IPv4 Address. . . . . . . . . . . : 10.0.25.17
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . : 10.0.25.1
```

We can obtain more detailed information about every network adapter using:

```cmd
ipconfig /all
```

This includes additional information such as MAC addresses, DHCP settings, DNS servers, and adapter configurations.

### ARP

The Address Resolution Protocol (ARP) is responsible for resolving IPv4 addresses to MAC addresses on a local network.

Windows maintains an ARP cache containing previously resolved addresses.

We can inspect it using:

```cmd
arp /a
```

Example output:

```text
Interface: 10.0.25.17 --- 0x17

Internet Address    Physical Address      Type
10.0.25.1           00-e0-67-15-cf-43     dynamic
10.0.25.5           54-9f-35-1c-3a-e2     dynamic
10.0.25.10          00-0c-29-62-09-81     dynamic
```

From a penetration testing perspective, the ARP cache can help us identify other devices whose addresses have been resolved by our host.

This information provides additional context for mapping the local network.

---

## Enumerating Our Current User

After gathering basic host and network information, we should investigate our current user account.

### Whoami

The `whoami` command identifies the account associated with our current security context.

```cmd
whoami
```

Example output:

```text
ACADEMY-WIN11\htb-student
```

The output identifies the domain or computer name and our current username.

### Enumerating Privileges

We can inspect the privileges assigned to our current security token using:

```cmd
whoami /priv
```

Example output:

```text
Privilege Name                Description                  State

SeShutdownPrivilege           Shut down the system         Disabled
SeChangeNotifyPrivilege       Bypass traverse checking     Enabled
SeTimeZonePrivilege           Change the time zone         Disabled
```

This information helps us understand which operations our current account may be permitted to perform.

During privilege escalation assessments, we should pay attention to additional privileges or unusual configurations that might expand our current capabilities.

### Enumerating Groups

We can identify our current user's group memberships using:

```cmd
whoami /groups
```

Example output:

```text
Group Name                         SID

Everyone                           S-1-1-0
BUILTIN\Users                      S-1-5-32-545
NT AUTHORITY\INTERACTIVE           S-1-5-4
NT AUTHORITY\Authenticated Users   S-1-5-11
```

Group membership is important because Windows uses groups to assign permissions and provide access to resources.

Custom groups or privileged memberships may reveal additional access available to our account.

### Complete User Enumeration

Instead of executing the previous commands individually, we can retrieve all available information using:

```cmd
whoami /all
```

This combines information about our current identity, group memberships, privileges, and other security-token details.

---

## Enumerating Other Users and Groups

After examining our own account, we can investigate other accounts and groups present on the host or domain.

### Net User

The `net user` command displays local user accounts.

```cmd
net user
```

Example output:

```text
User accounts for \\ACADEMY-WIN11

Administrator     DefaultAccount
Guest             htb-student
WDAGUtilityAccount
```

We can also retrieve information about a specific account:

```cmd
net user htb-student
```

### Net Group

The `net group` command allows us to enumerate domain groups when executed in the appropriate domain context.

```cmd
net group
```

### Net Localgroup

The `net localgroup` command lists the local groups available on a Windows host.

```cmd
net localgroup
```

Example output:

```text
Administrators
Backup Operators
Event Log Readers
Remote Desktop Users
Remote Management Users
Users
```

These groups are relevant because their members may have additional administrative or remote access permissions.

We can investigate the membership of a specific local group using:

```cmd
net localgroup Administrators
```

---

## Exploring Network Resources

Windows networks frequently use shared folders to provide users with access to files and other resources.

Enumerating these shares can help us understand which resources are exposed by a host.

### Net Share

The `net share` command lists shared resources configured on our current host.

```cmd
net share
```

Example output:

```text
Share name    Resource             Remark

C$            C:\                  Default share
IPC$                               Remote IPC
ADMIN$        C:\Windows           Remote Admin
Records       D:\Important-Files   Records storage
```

In this example, `Records` is a manually configured share that may warrant further investigation during an authorized assessment.

We should determine whether our account has permission to access the share and whether it contains information relevant to our assessment.

The presence of a share does not necessarily mean our current account can access its contents.

### Net View

The `net view` command can help us discover network computers and shared resources.

```cmd
net view
```

We can also specify a particular host to display its advertised shares:

```cmd
net view \\HOSTNAME
```

This can help us identify resources such as shared directories and printers in the network environment.

---

## Final Considerations

System enumeration provides the foundation for understanding a Windows host and determining the next steps in a security assessment.

The commands covered in this section allow us to gather information about operating systems, network configurations, user accounts, privileges, groups, and shared resources.

However, command-line enumeration also generates activity that defenders may detect through system logs and security monitoring tools.

**Key takeaway:** Effective enumeration requires a structured methodology. We should understand what information we need, why it matters, and how it relates to our assessment objectives before moving toward more advanced techniques.
