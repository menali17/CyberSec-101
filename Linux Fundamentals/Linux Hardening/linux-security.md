# Linux Security

Every computer system has some risk of intrusion.

The level of risk depends on factors such as:

```text
Internet exposure
Running services
Installed applications
System configuration
User permissions
Software vulnerabilities
```

For example, an internet-facing server running several complex web applications generally has a larger attack surface than a minimally configured internal system.

Linux security therefore requires several layers of protection rather than relying on a single security mechanism.

---

# Defense in Depth

A useful way to understand Linux security is through:

```text
Defense in Depth
```

Instead of depending on one control:

```text
System
  │
  ├── Updates
  ├── Firewall
  ├── SSH hardening
  ├── Least privilege
  ├── Login protection
  ├── Auditing
  ├── SELinux / AppArmor
  └── Monitoring
```

If one layer fails, another layer may still protect the system.

---

# Keep the System Updated

One of the most important security practices is keeping:

```text
Operating system
+
Installed packages
```

up to date.

The material uses:

```bash
menali@htb[/htb]$ apt update && apt dist-upgrade
```

Breaking it down:

```text
apt update
→ update package information

&&
→ execute the next command only if the first succeeds

apt dist-upgrade
→ upgrade installed packages while handling dependency changes
```

Conceptually:

```text
Known vulnerability
       │
       ▼
Security patch released
       │
       ▼
System updated
       │
       ▼
Vulnerability patched
```

An outdated system may continue running software containing publicly known vulnerabilities.

---

# Firewall

If network-level firewall rules are insufficient, Linux can restrict incoming and outgoing traffic locally.

The material mentions:

```text
Linux firewall
iptables
```

The basic purpose is:

```text
Network traffic
      │
      ▼
Firewall rules
      │
      ├── allowed → system
      │
      └── denied  → blocked
```

Firewalls therefore help reduce unnecessary network exposure.

---

# SSH Hardening

SSH is extremely useful for remote administration, but an exposed SSH service must be configured securely.

The material recommends:

```text
Disable password-based SSH login
Disable direct root SSH login
Use safer authentication mechanisms
```

The idea is to reduce opportunities for attackers to obtain remote access.

Instead of:

```text
Internet
   │
   ▼
SSH
   │
   ▼
Password authentication
```

we should prefer stronger authentication and restrict privileged access.

---

# Avoid Direct Root Administration

The material recommends avoiding routine administration directly as:

```text
root
```

Instead, normal user accounts should be used with controlled privilege escalation when necessary.

Conceptually:

```text
Normal User
     │
     │ sudo when required
     ▼
Privileged Operation
```

rather than:

```text
root
 │
 └── everything runs with maximum privileges
```

This reduces the consequences of mistakes or compromised accounts.

---

# Principle of Least Privilege

One of the most important security concepts in this section is:

```text
Principle of Least Privilege
```

A user should receive:

> Only the permissions necessary to perform the required task.

Suppose a user only needs permission to restart one service.

Instead of granting:

```text
Full sudo access
```

we should configure access specifically for:

```text
Required command
```

Conceptually:

```text
BAD

User
 └── unrestricted sudo
          │
          ▼
      root access
```

versus:

```text
BETTER

User
 └── sudo
      │
      └── only required command
```

The material specifically recommends defining required commands in the `sudoers` configuration instead of automatically granting full sudo privileges.

---

# `sudoers`

The:

```text
sudoers
```

configuration determines which users can execute privileged commands through `sudo`.

The security goal is:

```text
Give users exactly the privileged operations they need
```

rather than:

```text
Give everyone complete administrative privileges
```

This is a practical implementation of least privilege.

---

# Fail2ban

The material introduces:

```text
fail2ban
```

as another protection mechanism.

Fail2ban monitors failed authentication attempts.

Conceptually:

```text
Login attempt
     │
     ├── failure #1
     ├── failure #2
     ├── failure #3
     ├── ...
     │
     ▼
Configured threshold reached
     │
     ▼
Source handled according to policy
```

This is useful against repeated login attempts.

---

# System Auditing

Securing a Linux system is not something we perform once and forget.

Systems should be periodically audited for weaknesses that could enable:

```text
Privilege escalation
Unauthorized access
System compromise
```

The material specifically mentions checking for:

```text
Outdated kernels
Incorrect user permissions
World-writable files
Misconfigured cron jobs
Misconfigured services
```

---

# Outdated Kernel

The Linux kernel itself may contain vulnerabilities.

Therefore:

```text
Updated applications
≠
necessarily updated kernel
```

The material notes that administrators may forget that some kernel updates need additional attention or manual intervention.

An outdated kernel can potentially expose vulnerabilities useful for local privilege escalation.

---

# World-Writable Files

A:

```text
world-writable file
```

can be modified by any user.

Conceptually:

```text
File
 │
 ├── Owner → write
 ├── Group → write
 └── Others → write
```

This can become dangerous when the file influences privileged applications, scripts, or services.

---

# Misconfigured Cron Jobs

Recall:

```text
Cron
→ automatically executes scheduled commands
```

Now imagine:

```text
Root Cron Job
     │
     ▼
Runs script.sh
     │
     ▼
script.sh writable by normal user
```

A normal user could potentially modify the script, and later Cron could execute the modified content with elevated privileges.

Therefore, scheduled tasks must be audited carefully.

---

# Misconfigured Services

Services may create security risks through:

```text
Incorrect permissions
Unnecessary privileges
Weak authentication
Unsafe configuration
Unnecessary network exposure
```

This connects with the previous Service and Process Management section.

---

# SELinux

`SELinux` stands for:

```text
Security-Enhanced Linux
```

It provides security access-control policies enforced by the kernel.

SELinux assigns labels to system objects such as:

```text
Processes
Files
Directories
Other system objects
```

Then policies determine how those labeled objects may interact.

---

# SELinux Mental Model

```text
Process
  │
  │ label
  ▼
SELinux Policy
  │
  ├── permitted → access resource
  │
  └── denied    → block access
```

The important idea is that normal Unix permissions are not necessarily the only security control.

We can have:

```text
Traditional permissions
        +
SELinux policy
```

---

# Granular Access Control

SELinux can define very specific actions.

The material gives examples such as controlling who may:

```text
Append to a file
Move a file
Access a resource
```

This provides more granular controls than basic `rwx` permissions alone.

---

# AppArmor

The material also mentions:

```text
AppArmor
```

as another Linux kernel security mechanism.

Like SELinux, its purpose is to restrict what applications and processes are allowed to access.

A simplified mental model is:

```text
Application
     │
     ▼
Security Policy
     │
     ├── allowed action
     └── blocked action
```

---

# Additional Security Tools

The material mentions several applications that can contribute to Linux security:

| Tool       | General Role                |
| ---------- | --------------------------- |
| Snort      | Network security monitoring |
| chkrootkit | Rootkit checking            |
| rkhunter   | Rootkit checking            |
| Lynis      | Linux security auditing     |

The important point for this section is not memorizing every tool.

Instead, recognize that Linux security also involves:

```text
Monitoring
Auditing
Detection
System inspection
```

---

# Linux Hardening Checklist

The material recommends several additional security practices.

## Remove Unnecessary Services

Every unnecessary service can increase the system's attack surface.

Conceptually:

```text
10 running services
→ 10 potential areas to secure

3 necessary services
→ smaller attack surface
```

Therefore:

```text
If we do not need it
→ disable/remove it
```

---

# Avoid Unencrypted Authentication

Services relying on unencrypted authentication mechanisms should be removed or replaced.

The problem is:

```text
Username + Password
       │
       ▼
Unencrypted network
       │
       ▼
Credentials may be exposed
```

This connects directly with protocols such as Telnet or traditional plaintext FTP authentication.

---

# NTP

The material recommends ensuring:

```text
NTP is enabled
```

`NTP` keeps system time synchronized.

Accurate time is important for security because logs from multiple systems need consistent timestamps.

For example:

```text
Server A → 10:35 attack detected
Firewall → 10:35 connection detected
Server B → 10:35 authentication attempt
```

Synchronized clocks make event correlation much easier.

---

# Syslog

The material also recommends ensuring:

```text
Syslog is running
```

Logging allows us to investigate:

```text
Authentication events
Service activity
Errors
Security incidents
System changes
```

Conceptually:

```text
System Events
     │
     ▼
Logs
     │
     ▼
Monitoring / Investigation
```

---

# Individual User Accounts

Each user should have:

```text
their own account
```

instead of sharing credentials.

This improves:

```text
Accountability
Auditing
Access control
Incident investigation
```

If multiple administrators share the same account, determining who performed an action becomes much more difficult.

---

# Password Security

The material recommends:

```text
Strong passwords
Password aging
Password history
Account locking after failures
```

These controls reduce risks associated with weak or repeatedly reused passwords.

---

# SUID and SGID

Recall from Permission Management:

```text
SUID
SGID
```

are special permission mechanisms.

The material recommends disabling:

```text
unwanted SUID/SGID binaries
```

because incorrectly configured privileged binaries can become privilege-escalation vectors.

Conceptually:

```text
Normal User
     │
     ▼
SUID Program
     │
     ▼
Executes with elevated owner's privileges
```

If the program is vulnerable or unnecessary, this can create significant risk.

---

# Security Is a Process

One of the central ideas of this section is:

```text
Security is not a one-time configuration.
```

Systems change.

New vulnerabilities appear.

Packages become outdated.

New users are created.

Services are installed.

Configurations change.

Therefore:

```text
Secure
  │
  ▼
Monitor
  │
  ▼
Audit
  │
  ▼
Update
  │
  ▼
Harden
  │
  ▼
Monitor again
```

Linux security is a continuous process.

---

# TCP Wrappers

TCP Wrappers provide access control for compatible network services based on information such as:

```text
Client hostname
Client IP address
```

The two configuration files introduced are:

```text
/etc/hosts.allow
/etc/hosts.deny
```

---

# `/etc/hosts.allow`

This file defines allowed clients/services.

The material provides:

```bash
menali@htb[/htb]$ cat /etc/hosts.allow

# Allow access to SSH from the local network
sshd : 10.129.14.0/24

# Allow access to FTP from a specific host
ftpd : 10.129.14.10

# Allow access to Telnet from any host in the inlanefreight.local domain
telnetd : .inlanefreight.local
```

---

# Reading TCP Wrapper Rules

The basic structure is:

```text
SERVICE : CLIENT
```

For example:

```text
sshd : 10.129.14.0/24
```

means:

```text
Service
→ sshd

Allowed client network
→ 10.129.14.0/24
```

Conceptually:

```text
10.129.14.25
      │
      │ wants SSH
      ▼
TCP Wrapper
      │
      │ matches allowed network
      ▼
SSH allowed
```

---

# Allow Specific Host

The material uses:

```text
ftpd : 10.129.14.10
```

meaning:

```text
FTP service
     │
     └── allow 10.129.14.10
```

---

# Allow Domain

Another example is:

```text
telnetd : .inlanefreight.local
```

which permits matching hosts from the specified domain to access the Telnet service.

---

# `/etc/hosts.deny`

This file defines denied clients/services.

The material gives:

```bash
menali@htb[/htb]$ cat /etc/hosts.deny

# Deny access to all services from any host in the inlanefreight.com domain
ALL : .inlanefreight.com

# Deny access to SSH from a specific host
sshd : 10.129.22.22

# Deny access to FTP from hosts with IP addresses in the range of 10.129.22.0 to 10.129.22.255
ftpd : 10.129.22.0/24
```

---

# `ALL`

The rule:

```text
ALL : .inlanefreight.com
```

means:

```text
ALL
→ all compatible services

.inlanefreight.com
→ matching clients from that domain
```

Therefore, matching clients are denied access to the services covered by TCP Wrappers.

---

# Deny Specific Host

```text
sshd : 10.129.22.22
```

means:

```text
10.129.22.22
      │
      │ SSH request
      ▼
Denied
```

---

# Deny Network

```text
ftpd : 10.129.22.0/24
```

means hosts belonging to:

```text
10.129.22.0/24
```

are denied access to the specified FTP service.

---

# TCP Wrapper Decision

A simplified mental model is:

```text
Client
   │
   │ requests service
   ▼
TCP Wrapper rules
   │
   ├── allowed → service
   │
   └── denied  → reject connection
```

The material also emphasizes that rule ordering matters because matching rules determine the resulting behavior.

---

# TCP Wrappers vs Firewall

This distinction is important.

TCP Wrappers are:

```text
service-oriented access control
```

A firewall operates at the network level and can control:

```text
Ports
Protocols
Addresses
Traffic direction
```

So:

```text
TCP Wrappers
→ Who may access compatible services?

Firewall
→ What network traffic may enter/leave?
```

TCP Wrappers therefore do **not** replace a firewall.

---

# Linux Security Mental Model

A secure Linux host can be viewed as several layers:

```text
                 INTERNET
                    │
                    ▼
                FIREWALL
                    │
                    ▼
              NETWORK SERVICE
                    │
                    ▼
             AUTHENTICATION
                    │
                    ▼
              USER ACCOUNT
                    │
                    ▼
            LEAST PRIVILEGE
                    │
                    ▼
          SELinux / AppArmor
                    │
                    ▼
                 FILES
                    │
                    ▼
          LOGGING / MONITORING
```

At the same time:

```text
Updates
Auditing
Hardening
Monitoring
```

support all of these layers.

---

# Security Audit Mental Model

When auditing a Linux machine, some questions we should ask are:

```text
Is the system updated?

Is the kernel updated?

Which services are running?

Which ports are exposed?

Can root log in remotely?

Are passwords being used for SSH?

Who has sudo access?

Are permissions correctly configured?

Are there world-writable files?

Are dangerous SUID/SGID binaries present?

Are cron jobs secure?

Are unnecessary services installed?

Are logs being generated?

Are security policies active?
```

These questions connect directly with many Linux privilege-escalation and enumeration techniques we will encounter later.

---

# Quick Reference

| Concept          | Purpose                                 |
| ---------------- | --------------------------------------- |
| System updates   | Patch known vulnerabilities             |
| Firewall         | Restrict network traffic                |
| SSH hardening    | Reduce remote-access risk               |
| Least privilege  | Minimize user permissions               |
| `sudoers`        | Control privileged commands             |
| fail2ban         | Respond to repeated failed logins       |
| Auditing         | Discover weaknesses/misconfigurations   |
| SELinux          | Granular mandatory access control       |
| AppArmor         | Application access restrictions         |
| NTP              | Synchronize system time                 |
| Syslog           | Record system events                    |
| SUID/SGID review | Reduce privilege-escalation risk        |
| TCP Wrappers     | Service access control based on clients |

---

# Files to Remember

```text
/etc/hosts.allow
→ TCP Wrapper allow rules
```

```text
/etc/hosts.deny
→ TCP Wrapper deny rules
```

And from earlier sections:

```text
/etc/ssh/sshd_config
→ SSH server configuration
```

```text
/etc/sudoers
→ sudo privileges
```

---

# Commands to Remember First

Update the system:

```bash
apt update && apt dist-upgrade
```

Inspect TCP Wrapper allow rules:

```bash
cat /etc/hosts.allow
```

Inspect deny rules:

```bash
cat /etc/hosts.deny
```

The individual security tools and advanced configurations can be learned as we encounter them rather than memorized immediately.

---

# What to Remember First

The most important principle is:

```text
Least Privilege
→ Give only the permissions that are actually necessary.
```

For remote access:

```text
SSH
→ avoid direct root login
→ avoid weak password authentication
```

For the system:

```text
Update
→ patch vulnerabilities

Firewall
→ reduce network exposure

Audit
→ find dangerous configurations

SELinux / AppArmor
→ restrict what processes may access

Logs
→ know what happened
```

For privilege escalation:

```text
Outdated kernel
World-writable files
Misconfigured cron jobs
Misconfigured services
Dangerous SUID/SGID binaries
Excessive sudo privileges
```

should immediately stand out as potentially important.

---

## Key Takeaway

**Linux security is a continuous hardening process rather than a single configuration. We should keep the system updated, minimize exposed services, harden SSH, enforce least privilege, audit permissions and privileged mechanisms, monitor authentication attempts, and use additional controls such as SELinux or AppArmor when appropriate. Misconfigurations involving sudo, SUID/SGID, cron jobs, services, permissions, and outdated software are particularly important because they may allow attackers to escalate privileges after gaining initial access.**
