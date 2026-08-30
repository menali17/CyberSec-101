# Networking Key Terminology

Networking contains a large number of protocols and technologies. At this stage, it is not necessary to memorize every protocol. The main goal is to recognize the most common terms and understand their basic purposes.

---

## Essential Protocols

These protocols are especially important because they frequently appear in networking, system administration, enumeration, and cybersecurity.

| Protocol | Name | Main Purpose |
|---|---|---|
| `SSH` | Secure Shell | Secure remote access and command execution |
| `FTP` | File Transfer Protocol | File transfer between systems |
| `SMTP` | Simple Mail Transfer Protocol | Email transmission |
| `HTTP` | Hypertext Transfer Protocol | Web communication |
| `SMB` | Server Message Block | File, printer, and resource sharing |
| `NFS` | Network File System | Accessing files over a network |
| `SNMP` | Simple Network Management Protocol | Monitoring and managing network devices |
| `NTP` | Network Time Protocol | Synchronizing system clocks |
| `VPN` | Virtual Private Network | Connecting securely to another network |
| `IPsec` | Internet Protocol Security | Protecting IP communication using authentication and encryption |
| `NAT` | Network Address Translation | Translating addresses between networks |
| `VLAN` | Virtual Local Area Network | Logically segmenting a network |

---

# Common Services

For cybersecurity, it is useful to associate protocols with the services they provide.

```text
SSH
→ Remote access

FTP
→ File transfer

SMTP
→ Email

HTTP
→ Web

SMB
→ Windows file/resource sharing

NFS
→ Network file sharing

SNMP
→ Network device management

NTP
→ Time synchronization
```

Later, these protocols can also be associated with their common ports.

For example:

```text
21  → FTP
22  → SSH
25  → SMTP
80  → HTTP
443 → HTTPS
445 → SMB
```

---

# Network Segmentation

## VLAN

A **Virtual Local Area Network (VLAN)** logically divides a physical network into separate networks.

For example:

```text
Physical Network
      |
      +---- VLAN 10 → Employees
      |
      +---- VLAN 20 → Servers
      |
      +---- VLAN 30 → Guests
```

Devices can therefore use the same physical network infrastructure while belonging to different logical networks.

From a security perspective, VLANs are commonly used for **network segmentation**.

---

# NAT

**Network Address Translation (NAT)** translates IP addresses between networks.

A common example is a home network:

```text
192.168.1.10 ─┐
192.168.1.20 ─┼── Router/NAT ──→ Public IP ──→ Internet
192.168.1.30 ─┘
```

Multiple devices using private IPv4 addresses can access the Internet through a public IP address.

Remember:

```text
Private Network
      ↓
     NAT
      ↓
Public Network
```

---

# VPN

A **Virtual Private Network (VPN)** allows a device to communicate with another network through a tunnel.

Conceptually:

```text
Computer
    |
    | VPN Tunnel
    v
Remote Network
```

This can make the computer behave as if it were connected to the remote network.

Hack The Box uses VPN technology to provide access to lab networks.

---

# IPsec

**IPsec (Internet Protocol Security)** is a collection of protocols used to secure IP communication.

It can provide:

```text
Authentication
Encryption
Integrity
```

IPsec is commonly associated with VPN implementations.

---

# Wireless Security

Some protocols in the list are related to wireless network security.

## WEP

**Wired Equivalent Privacy (WEP)** is an old wireless security protocol.

```text
WEP
→ Old
→ Weak
→ Considered insecure
```

---

## WPA

**Wi-Fi Protected Access (WPA)** was introduced as an improvement over WEP.

It is part of the evolution of Wi-Fi security.

```text
WEP
 ↓
WPA
 ↓
Newer WPA generations
```

---

## TKIP

**Temporal Key Integrity Protocol (TKIP)** is an older security mechanism associated with WPA.

It is now considered outdated compared with newer wireless security mechanisms.

---

## EAP

**Extensible Authentication Protocol (EAP)** is an authentication framework that supports different authentication methods.

It can be used with:

```text
Passwords
Certificates
Tokens
Other authentication mechanisms
```

Related technologies include:

```text
EAP
├── LEAP
└── PEAP
```

At this stage, recognizing these names is more important than understanding their internal operation.

---

# Routing Protocols

Several protocols in the list are responsible for exchanging routing information.

Important examples include:

```text
RIP
OSPF
IGRP
EIGRP
```

Their general purpose is:

```text
Routers
   |
   | Exchange routing information
   v
Determine paths through networks
```

### RIP

**Routing Information Protocol**

A distance-vector routing protocol.

### OSPF

**Open Shortest Path First**

A routing protocol used to determine paths within an Autonomous System.

### IGRP

**Interior Gateway Routing Protocol**

A Cisco proprietary routing protocol.

### EIGRP

**Enhanced Interior Gateway Routing Protocol**

An advanced routing protocol associated with Cisco networks.

For now, remember:

```text
RIP / OSPF / IGRP / EIGRP
→ Routing protocols
```

---

# Layer 2 Technologies

## STP

**Spanning Tree Protocol (STP)** prevents Layer 2 switching loops.

```text
STP
→ Layer 2
→ Prevents network loops
```

---

## VTP

**VLAN Trunking Protocol (VTP)** is associated with managing VLAN information across switches.

```text
VTP
→ VLAN management
→ Multiple switches
```

---

# Router Redundancy

## HSRP

**Hot Standby Router Protocol (HSRP)** provides router redundancy, particularly in Cisco environments.

## VRRP

**Virtual Router Redundancy Protocol (VRRP)** also provides router redundancy.

The general idea is:

```text
Router A ─┐
          ├── Virtual Gateway
Router B ─┘
```

If one router becomes unavailable, another can continue providing connectivity.

For now:

```text
HSRP / VRRP
→ Router redundancy
```

---

# Authentication

## TACACS

**Terminal Access Controller Access-Control System (TACACS)** provides centralized:

```text
Authentication
Authorization
Accounting
```

These are commonly grouped as:

```text
AAA
```

---

# Voice Communication

## SIP

**Session Initiation Protocol (SIP)** is used to establish and manage voice, video, and multimedia communication sessions.

## VoIP

**Voice over IP (VoIP)** allows voice communication over IP networks.

Conceptually:

```text
VoIP
→ Voice over networks

SIP
→ Helps establish/manage communication sessions
```

---

# Industrial Networks

## SCADA

**Supervisory Control and Data Acquisition (SCADA)** refers to systems used to monitor and control industrial processes.

Examples include:

```text
Power generation
Manufacturing
Water treatment
Industrial infrastructure
```

SCADA is particularly relevant to **ICS/OT security**.

---

# Web Terminology

## URI

**Uniform Resource Identifier (URI)** identifies a resource.

## URL

**Uniform Resource Locator (URL)** is a type of URI that specifies where a resource is located and how it can be accessed.

Simplified:

```text
URI
└── identifies a resource

URL
└── identifies + provides its location/access method
```

---

# Other Terms Worth Recognizing

These terms do not need to be deeply studied at this stage, but their general purpose is useful to recognize.

```text
PGP
→ Encryption for files, email, and other data

IKE
→ Establishes security associations, commonly associated with IPsec/VPNs

GRE
→ Network tunneling/encapsulation

CDP
→ Discovery of Cisco network devices

PPTP
→ Older VPN tunneling protocol

RSH
→ Remote command execution on Unix systems

CRLF
→ Carriage Return + Line Feed

AJAX
→ Web technique for asynchronous communication

NNTP
→ Newsgroup communication

ISAPI
→ Microsoft web server extension interface

LEAP / PEAP
→ Authentication technologies related to EAP
```

---

# Cybersecurity Perspective

For cybersecurity, protocols often become important during **enumeration**.

A discovered network service may reveal what protocol is being used.

For example:

```text
Port 21
   ↓
FTP
   ↓
File transfer service

Port 22
   ↓
SSH
   ↓
Remote access

Port 80 / 443
   ↓
HTTP / HTTPS
   ↓
Web application

Port 445
   ↓
SMB
   ↓
Windows file/resource sharing
```

This relationship becomes increasingly important:

```text
PORT
  ↓
PROTOCOL / SERVICE
  ↓
ENUMERATION
  ↓
SECURITY ANALYSIS
```

---

# Quick Reference

```text
SSH   → Secure remote access
FTP   → File transfer
SMTP  → Email
HTTP  → Web
SMB   → File/resource sharing
NFS   → Network file sharing
SNMP  → Network management
NTP   → Time synchronization

VLAN  → Network segmentation
NAT   → Address translation
VPN   → Network tunnel
IPsec → Secure IP communication

WEP   → Old/insecure Wi-Fi security
WPA   → Wi-Fi security
EAP   → Authentication framework

RIP
OSPF
IGRP
EIGRP
      → Routing protocols

STP   → Prevents Layer 2 loops
VTP   → VLAN management

HSRP
VRRP
      → Router redundancy

SIP   → Communication session signaling
VoIP  → Voice over IP

TACACS → Centralized AAA
SCADA  → Industrial control/monitoring systems

URI → Resource identifier
URL → Resource location

IKE → IPsec/VPN negotiation
GRE → Tunneling/encapsulation
```

# What to Focus On

At this stage, prioritize understanding:

```text
SSH
FTP
SMTP
HTTP
SMB
NFS
SNMP
NTP
VLAN
VPN
IPsec
NAT
```

Recognize these groups:

```text
RIP / OSPF / IGRP / EIGRP
→ Routing

WEP / WPA / TKIP
→ Wireless security

HSRP / VRRP
→ Router redundancy

SIP / VoIP
→ Voice communication
```

The remaining terminology can be learned gradually when it appears in practical networking and cybersecurity modules.

The goal is not to memorize the entire list now.

The goal is to build the association:

Protocol → Purpose → Service → Security relevance
```