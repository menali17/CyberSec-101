# The TCP/IP Model

The **TCP/IP Model**, also known as the **Internet Protocol Suite**, is the layered model used to describe communication across modern networks and the Internet.

The name comes from two of its fundamental protocols:

- **TCP — Transmission Control Protocol**
- **IP — Internet Protocol**

Compared to the seven-layer OSI model, TCP/IP combines several functions into **four layers**.

```text
4 ─ Application
3 ─ Transport
2 ─ Internet
1 ─ Link
```

---

# TCP/IP Layers

## Layer 4 — Application

The **Application Layer** provides network services directly to applications and defines the protocols applications use to exchange data.

```text
Applications
     │
     ▼
Application Protocols
     │
     ▼
Transport Layer
```

This layer combines functions represented by the Application, Presentation, and Session layers of the OSI model.

---

## Layer 3 — Transport

The **Transport Layer** provides communication services for applications.

The two important protocols are:

```text
TCP → Session-oriented communication
UDP → Datagram-oriented communication
```

TCP and UDP also use **ports** to distinguish between applications and services.

For example:

```text
Application
    │
    ▼
TCP / UDP
    │
    ▼
Port
```

---

## Layer 2 — Internet

The **Internet Layer** is responsible for:

- Host addressing
- Packet packaging
- Routing

The main protocol associated with this layer is:

**IP — Internet Protocol**

Its purpose is to allow packets to reach their intended destination across interconnected networks.

```text
Source
   │
   ▼
IP Packet
   │
   ▼
Router
   │
   ▼
Router
   │
   ▼
Destination
```

---

## Layer 1 — Link

The **Link Layer** handles communication with the actual network medium.

It is responsible for placing TCP/IP packets onto the network and receiving packets from the network.

TCP/IP is designed to work independently of the specific:

- Network access method
- Frame format
- Transmission medium

---

# OSI vs TCP/IP

The TCP/IP model combines several OSI layers.

```text
OSI                         TCP/IP

7 Application ──────┐
6 Presentation ─────┼────► Application
5 Session ──────────┘

4 Transport ─────────────► Transport

3 Network ───────────────► Internet

2 Data Link ────────┐
1 Physical ─────────┴────► Link
```

Therefore:

| OSI | TCP/IP |
|---|---|
| Application | Application |
| Presentation | Application |
| Session | Application |
| Transport | Transport |
| Network | Internet |
| Data Link | Link |
| Physical | Link |

---

# Main TCP/IP Tasks

The TCP/IP protocol family provides several fundamental networking functions.

---

## Logical Addressing

**Protocol: IP**

IP provides logical addressing for networks and hosts.

```text
Host
 │
 └── IP Address
```

Logical addressing allows packets to be directed toward the correct network and destination.

Concepts related to logical addressing include:

- Network classes
- Subnetting
- CIDR

---

# Routing

**Protocol: IP**

Routing determines the next node a packet should use while traveling from sender to receiver.

```text
Sender
  │
  ▼
Router A
  │
  ▼
Router B
  │
  ▼
Receiver
```

Each intermediate node determines where the packet should go next.

The sender does not need to know the complete physical path to the destination.

---

# Error and Flow Control

**Protocol: TCP**

TCP maintains communication between sender and receiver through a virtual connection.

Control messages are exchanged to help manage the connection and data transfer.

```text
Sender
   │
   │ TCP Connection
   │
   ▼
Receiver
```

This allows TCP to control the flow of transferred data.

---

# Application Support

**Protocols: TCP / UDP**

TCP and UDP use **ports** to distinguish between different applications and communication channels.

For example:

```text
IP Address
    │
    ├── Port A → Application A
    ├── Port B → Application B
    └── Port C → Application C
```

This allows multiple network applications to communicate through the same host.

---

# Name Resolution

**Protocol: DNS**

DNS translates names into IP addresses.

For example:

```text
www.example.com
       │
       ▼
      DNS
       │
       ▼
93.184.216.34
```

This allows users and applications to locate hosts using names rather than manually remembering IP addresses.

---

# TCP and IP

Although the model is called **TCP/IP**, the two protocols perform different functions.

## IP

IP is responsible for:

```text
Addressing
    +
Routing
    │
    ▼
Getting the packet toward its destination
```

## TCP

TCP is responsible for controlling the data transfer between applications.

```text
Application A
     │
     │ TCP
     ▼
Application B
```

A simplified way to distinguish them:

```text
IP  → Where should the packet go?

TCP → How is the data transfer controlled?
```

---

# Quick Reference

| Task | Protocol | Purpose |
|---|---|---|
| Logical Addressing | `IP` | Identifies networks and hosts |
| Routing | `IP` | Determines how packets reach destinations |
| Error & Flow Control | `TCP` | Controls data transfer |
| Application Support | `TCP / UDP` | Uses ports to distinguish applications |
| Name Resolution | `DNS` | Resolves names into IP addresses |

---

# OSI Relationship

It is also useful to remember where TCP and IP appear when using OSI terminology:

```text
OSI Layer 4 — Transport
        │
        ├── TCP
        └── UDP

OSI Layer 3 — Network
        │
        └── IP
```

This explains terminology such as:

```text
Layer 3 → IP / Routing

Layer 4 → TCP / UDP / Ports
```

---

# Key Takeaways

- TCP/IP is also known as the **Internet Protocol Suite**.
- The TCP/IP model contains four layers: Application, Transport, Internet, and Link.
- TCP/IP combines several layers that are separated in the OSI model.
- **IP** handles logical addressing and routing.
- **TCP** handles connection and data-transfer control.
- **TCP and UDP ports** distinguish different applications and communication channels.
- **DNS** resolves names into IP addresses.
- TCP belongs to the OSI Transport Layer (Layer 4).
- IP belongs to the OSI Network Layer (Layer 3).