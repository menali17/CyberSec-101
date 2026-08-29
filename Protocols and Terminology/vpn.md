# Virtual Private Networks

A **Virtual Private Network (VPN)** creates a secure and encrypted connection between a remote device and a private network.

The main idea is:

```text
Remote Device
     |
     | Encrypted Tunnel
     v
VPN Server
     |
     v
Private Network
```

This allows a remote device to access internal resources as if it were connected to the private network.

---

## Why VPNs Are Used

VPNs are commonly used for:

- Secure remote access
- Protecting data in transit
- Connecting remote employees to internal resources
- Connecting multiple company locations
- Reducing the need for dedicated private lines

A remote user typically connects over the public Internet and authenticates to a VPN server.

After authentication, the VPN client may receive an internal IP address and gain access to private resources.

---

# VPN Components

A VPN connection generally requires:

| Component | Purpose |
|---|---|
| `VPN Client` | Software used by the remote device to establish the VPN connection |
| `VPN Server` | Accepts VPN connections and routes traffic to the private network |
| `Encryption` | Protects transmitted data |
| `Authentication` | Verifies the VPN client and/or server |

Authentication may use:

```text
Shared Secrets
Certificates
Other Authentication Methods
```

Encryption may use technologies such as:

```text
AES
IPsec
```

---

# VPN Communication

The basic communication flow is:

```text
Client
   |
   | Internet
   v
Encrypted VPN Tunnel
   |
   v
VPN Server
   |
   v
Internal Network
```

The public Internet transports the encrypted VPN traffic, while the VPN tunnel protects the data being transmitted.

---

# IPsec

`IPsec` stands for:

```text
Internet Protocol Security
```

IPsec provides security for IP communication.

Its main goals are:

```text
Confidentiality
Integrity
Authentication
```

IPsec commonly uses two protocols:

- `AH` — Authentication Header
- `ESP` — Encapsulating Security Payload

---

## AH - Authentication Header

`AH` provides:

```text
Integrity
Authentication
```

It helps verify that a packet:

- Came from the expected source
- Was not modified during transmission

However:

```text
AH does not encrypt the packet payload.
```

---

## ESP - Encapsulating Security Payload

`ESP` provides:

```text
Encryption
Optional Authentication
Integrity Protection
```

ESP encrypts the data carried inside IP packets.

Simplified:

```text
Original Data
     |
     v
ESP Encryption
     |
     v
Protected Traffic
```

---

# AH vs ESP

| Protocol | Encryption | Integrity / Authentication |
|---|---|---|
| `AH` | No | Yes |
| `ESP` | Yes | Yes / Optional depending on configuration |

A simple way to remember:

```text
AH
→ Authentication

ESP
→ Encryption + Protection
```

---

# IPsec Modes

IPsec can operate in two important modes:

- `Transport Mode`
- `Tunnel Mode`

---

## Transport Mode

In **Transport Mode**, IPsec protects the payload of the original IP packet.

The original IP header remains visible.

```text
Original IP Header
        +
Encrypted Payload
```

This mode is commonly associated with secure communication between two hosts.

Conceptually:

```text
Host A
   |
   | Protected Communication
   v
Host B
```

---

## Tunnel Mode

In **Tunnel Mode**, the original IP packet is protected and encapsulated inside a new packet.

Conceptually:

```text
Original IP Packet
       |
       v
Encrypted / Encapsulated
       |
       v
New IP Packet
```

This is commonly used for VPN connections between networks.

Example:

```text
Network A
    |
 Gateway
    |
==== IPsec Tunnel ====
    |
 Gateway
    |
Network B
```

A useful distinction is:

```text
Transport Mode
→ Host-to-Host

Tunnel Mode
→ Network-to-Network / VPN
```

---

# IKE

`IKE` stands for:

```text
Internet Key Exchange
```

IKE is used to establish and maintain secure IPsec communication.

Its main role is negotiating:

- Cryptographic algorithms
- Security parameters
- Shared keys

Conceptually:

```text
VPN Client
    |
    | IKE Negotiation
    v
VPN Server
    |
    v
Encryption Keys Established
```

After the security parameters and keys are agreed upon, protected VPN traffic can be exchanged.

A common IKE port is:

```text
UDP/500
```

---

# ESP and NAT Traversal

ESP normally uses:

```text
IP Protocol 50
```

When NAT is involved, IPsec may use **NAT Traversal (NAT-T)**.

NAT-T commonly encapsulates ESP traffic inside:

```text
UDP/4500
```

Quick reference:

```text
IKE
→ UDP/500

ESP
→ IP Protocol 50

IPsec NAT-T
→ UDP/4500
```

---

# IPsec Traffic

A firewall may need to allow the following traffic for an IPsec VPN:

| Protocol | Identifier |
|---|---|
| `IKE` | `UDP/500` |
| `ESP` | IP Protocol `50` |
| `AH` | IP Protocol `51` |
| `NAT-T` | `UDP/4500` |

These protocols provide negotiation, encryption, authentication, and transport for the VPN connection.

---

# PPTP

`PPTP` stands for:

```text
Point-to-Point Tunneling Protocol
```

PPTP is an older protocol used to create VPN tunnels.

A commonly associated port is:

```text
TCP/1723
```

PPTP was widely supported because of its simplicity and compatibility.

However, it contains known security weaknesses.

Its authentication mechanisms, especially those based on `MSCHAPv2`, are considered weak by modern standards.

Therefore:

```text
PPTP
→ Legacy VPN Protocol
→ Known Security Weaknesses
→ Not Recommended for Modern Secure VPNs
```

Modern alternatives include:

```text
IPsec / IKEv2
OpenVPN
L2TP / IPsec
```

---

# VPN Example

Suppose an administrator is working remotely and needs access to an internal server.

Without a VPN:

```text
Administrator
      |
      v
Internet
      |
      X
Internal Server
```

The server may not accept connections directly from the Internet.

With a VPN:

```text
Administrator
      |
      | VPN
      v
VPN Server
      |
      v
Internal Network
      |
      v
Internal Server
```

The administrator can now communicate with internal resources through the VPN tunnel.

---

# Relation to HTB

Hack The Box uses VPN connectivity to provide access to private lab networks.

Conceptually:

```text
Pwnbox / Local Machine
        |
       tun0
        |
        v
     HTB VPN
        |
        v
   Lab Network
        |
        v
     Target
```

The `tun0` interface represents the VPN tunnel interface used to route traffic toward the HTB lab network.

This connects directly with:

```bash
ip route get <TARGET_IP>
```

which can show that traffic toward a target uses:

```text
dev tun0
```

---

# Quick Reference

```text
VPN
→ Secure tunnel to another network

VPN Client
→ Starts the VPN connection

VPN Server
→ Accepts the connection

Encryption
→ Protects the data

Authentication
→ Verifies identity
```

IPsec:

```text
AH
→ Integrity + Authentication
→ No encryption

ESP
→ Encryption + Protection

IKE
→ Negotiates keys/security parameters
→ UDP/500

ESP
→ IP Protocol 50

NAT-T
→ UDP/4500
```

Modes:

```text
Transport Mode
→ Protects packet payload
→ Commonly Host-to-Host

Tunnel Mode
→ Encapsulates original packet
→ Commonly used for VPN tunnels
```

Legacy:

```text
PPTP
→ TCP/1723
→ Old
→ Insecure
```

---


The most important mental model is:

```text
VPN
→ Creates access to a remote private network

IKE
→ Negotiates the secure connection

ESP
→ Protects/encrypts VPN traffic

Tunnel
→ Carries the protected traffic across another network
```