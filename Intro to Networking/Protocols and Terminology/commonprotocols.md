# Common Protocols

Network protocols define standardized rules that allow devices to communicate with each other.

Two fundamental transport protocols are:

- `TCP` — Transmission Control Protocol
- `UDP` — User Datagram Protocol

Understanding protocols is especially important in cybersecurity because identifying an open port can reveal which service may be running on a target.

```text
Open Port
    ↓
Possible Protocol / Service
    ↓
Enumeration
    ↓
Security Analysis
```

---

# TCP — Transmission Control Protocol

TCP is a **connection-oriented** protocol.

Before transmitting application data, TCP establishes a connection between the two devices.

```text
Client ←→ Server
      TCP Connection
```

TCP focuses on **reliable communication**.

Characteristics:

```text
TCP
→ Connection-oriented
→ Reliable
→ Maintains a connection
→ More overhead than UDP
```

A common example is web communication.

```text
Browser
   ↓
HTTP/HTTPS
   ↓
TCP
   ↓
Web Server
```

---

# TCP Three-Way Handshake

TCP establishes a connection using the **Three-Way Handshake**.

```text
Client                      Server

   -------- SYN -------->

   <----- SYN-ACK -------

   -------- ACK -------->

Connection Established
```

The three steps are:

```text
1. SYN
2. SYN-ACK
3. ACK
```

Simplified:

```text
SYN
→ "I want to establish a connection."

SYN-ACK
→ "I received your request and agree."

ACK
→ "Confirmed."
```

After this process, data can be transmitted.

This concept is very important for networking and cybersecurity.

---

# UDP — User Datagram Protocol

UDP is **connectionless**.

Unlike TCP, UDP does not establish a connection before sending data.

```text
Sender
   |
   | Data
   v
Receiver
```

There is no TCP-style handshake before transmission.

Characteristics:

```text
UDP
→ Connectionless
→ Faster
→ Lower overhead
→ Does not guarantee delivery
```

UDP is useful when speed is more important than guaranteeing that every packet arrives.

---

# TCP vs UDP

| TCP | UDP |
|---|---|
| Connection-oriented | Connectionless |
| Reliable | No delivery guarantee |
| Uses a handshake | No handshake |
| More overhead | Less overhead |
| Usually slower | Usually faster |

Easy way to remember:

```text
TCP
→ "Did you receive it?"

UDP
→ "I sent it."
```

---

# Ports and Services

Ports help identify which application or service should receive network traffic.

For cybersecurity, it is important to gradually build the association:

```text
PORT → SERVICE → PURPOSE
```

Some important examples:

| Port | Protocol/Service | Purpose |
|---:|---|---|
| `20/21` | FTP | File transfer |
| `22` | SSH | Secure remote access |
| `23` | Telnet | Remote access |
| `25` | SMTP | Email transfer |
| `53` | DNS | Domain name resolution |
| `67/68` | DHCP | Automatic IP configuration |
| `80` | HTTP | Web |
| `88` | Kerberos | Authentication |
| `110` | POP3 | Retrieve email |
| `111/2049` | NFS | Network file systems |
| `123` | NTP | Time synchronization |
| `135` | RPC | Remote procedure calls |
| `143` | IMAP | Email access |
| `161/162` | SNMP | Network management |
| `389` | LDAP | Directory services |
| `443` | HTTPS | Secure web |
| `445` | SMB | File/resource sharing |
| `1433` | MSSQL | Microsoft SQL Server |
| `3306` | MySQL | MySQL database |
| `3389` | RDP | Remote desktop |
| `5432` | PostgreSQL | PostgreSQL database |
| `5900` | VNC | Remote graphical desktop |

Do not try to memorize every port immediately.

Prioritize:

```text
21   → FTP
22   → SSH
23   → Telnet
25   → SMTP
53   → DNS
80   → HTTP
88   → Kerberos
110  → POP3
143  → IMAP
161  → SNMP
389  → LDAP
443  → HTTPS
445  → SMB
3389 → RDP
```

These associations will become natural through enumeration and practical exercises.

---

# Important Cybersecurity Services

## SSH — Port 22

```text
SSH
→ Secure Shell
→ Secure remote access
→ TCP/22
```

Example:

```bash
ssh user@10.10.10.10
```

---

## FTP — Ports 20/21

```text
FTP
→ File Transfer Protocol
→ File transfer
→ TCP/20-21
```

FTP may become interesting during enumeration because servers can sometimes expose files or allow weak/anonymous authentication.

---

## Telnet — Port 23

```text
Telnet
→ Remote access
→ TCP/23
→ Unencrypted
```

Unlike SSH, Telnet does not provide secure encrypted remote communication.

---

## HTTP / HTTPS

```text
HTTP
→ TCP/80
→ Web traffic

HTTPS
→ TCP/443
→ Secure web traffic
```

Web services are extremely common targets during enumeration.

---

## DNS — Port 53

DNS translates domain names into IP addresses.

```text
example.com
     ↓
    DNS
     ↓
93.184.216.34
```

Remember:

```text
DNS → Port 53
```

DNS can use both TCP and UDP depending on the operation.

---

## SMB — Port 445

SMB is commonly associated with Windows networks and resource sharing.

```text
SMB
→ TCP/445
→ Files
→ Shared folders
→ Network resources
```

SMB is particularly important in Windows/Active Directory environments.

---

## Kerberos — Port 88

Kerberos is used for authentication and authorization.

```text
Kerberos
→ Port 88
→ Authentication
```

It is especially important in Active Directory environments.

---

## LDAP — Port 389

LDAP provides access to directory services.

```text
LDAP
→ Port 389
→ Directory services
```

LDAP is also extremely important when working with Active Directory.

---

## RDP — Port 3389

Remote Desktop Protocol provides graphical remote access to Windows systems.

```text
RDP
→ TCP/3389
→ Windows Remote Desktop
```

---

## SNMP — Ports 161/162

SNMP is used to monitor and manage network devices.

```text
SNMP
→ Network management
→ UDP/161
```

It can be useful during enumeration because network devices may expose valuable system and configuration information.

---

# ICMP

ICMP stands for:

```text
Internet Control Message Protocol
```

It is used for network status information, diagnostics, and error reporting.

One of the most familiar uses is:

```text
ping
```

Conceptually:

```text
Computer A
    |
    | ICMP Echo Request
    v
Computer B
    |
    | ICMP Echo Reply
    v
Computer A
```

Therefore:

```text
ping target
```

essentially tests whether the target responds to ICMP Echo Requests.

---

# Important ICMP Messages

## Echo Request

Tests whether a device is reachable.

```text
Host A → Echo Request → Host B
```

## Echo Reply

Response to an Echo Request.

```text
Host A ← Echo Reply ← Host B
```

## Destination Unreachable

Indicates that a packet could not reach its destination.

## Time Exceeded

Indicates that the packet's lifetime expired before reaching the destination.

This is closely related to **TTL**.

---

# TTL — Time To Live

TTL limits how long an IP packet can travel through a network.

Every router that forwards the packet decreases its TTL.

Example:

```text
Initial TTL = 64

Router 1 → 63
Router 2 → 62
Router 3 → 61
Router 4 → 60
```

When:

```text
TTL = 0
```

the router discards the packet.

This prevents packets from circulating forever because of routing loops.

```text
Packet
  ↓
Router → TTL - 1
  ↓
Router → TTL - 1
  ↓
Router → TTL - 1
```

---

# TTL and OS Fingerprinting

Default TTL values can sometimes provide clues about the operating system.

Common examples:

```text
Windows
→ Typical initial TTL: 128

Linux/macOS
→ Typical initial TTL: 64

Solaris
→ Typical initial TTL: 255
```

For example:

```text
Observed TTL = 122
```

A possible interpretation is:

```text
Initial TTL ≈ 128
128 - 122 = 6 hops

Possible Windows system
```

However, TTL should only be treated as a **clue**, not definitive proof of the operating system.

---

# VoIP and SIP

VoIP means:

```text
Voice over Internet Protocol
```

It allows voice and multimedia communication over IP networks.

SIP stands for:

```text
Session Initiation Protocol
```

SIP is used to establish and manage communication sessions.

Common SIP methods include:

```text
INVITE
→ Initiate a session

ACK
→ Confirm an INVITE

BYE
→ Terminate a session

CANCEL
→ Cancel a pending request

REGISTER
→ Register a user agent

OPTIONS
→ Request information about capabilities
```

From a security perspective, SIP can potentially expose information about users and services during enumeration.

---

# Cybersecurity Perspective

When performing enumeration, discovering an open port gives us an indication of the service running on the target.

Example:

```text
Nmap Scan

22/tcp open
   ↓
SSH
   ↓
Remote access service
   ↓
Enumerate SSH
```

Another example:

```text
445/tcp open
   ↓
SMB
   ↓
File/resource sharing
   ↓
Enumerate SMB
```

Therefore, one of the most useful skills to develop is:

```text
See Port
   ↓
Recognize Service
   ↓
Understand Purpose
   ↓
Know How to Enumerate It
```

---

# Quick Reference

```text
TCP
→ Connection-oriented
→ Reliable
→ Three-Way Handshake

UDP
→ Connectionless
→ Faster
→ No delivery guarantee
```

Three-Way Handshake:

```text
SYN
SYN-ACK
ACK
```

Essential ports:

```text
21   FTP
22   SSH
23   Telnet
25   SMTP
53   DNS
80   HTTP
88   Kerberos
110  POP3
143  IMAP
161  SNMP
389  LDAP
443  HTTPS
445  SMB
3389 RDP
```

ICMP:

```text
ICMP
→ Diagnostics
→ Error reporting
→ ping

Echo Request
→ "Are you there?"

Echo Reply
→ "Yes."

TTL
→ Decreases at every router
→ Packet is discarded when TTL reaches 0
```

Typical initial TTL values:

```text
Windows → 128
Linux   → 64
macOS   → 64
Solaris → 255
```

