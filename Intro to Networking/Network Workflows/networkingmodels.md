# Networking Models

Two fundamental models are used to describe how data moves between systems:

- **OSI Model**
- **TCP/IP Model**

Both divide network communication into layers, with each layer performing a specific set of functions.

---

# OSI Model

The **OSI (Open Systems Interconnection) Model** is a reference model used to describe communication between networked systems.

It contains **seven layers**:

```text
7 - Application
6 - Presentation
5 - Session
4 - Transport
3 - Network
2 - Data Link
1 - Physical
```

Each layer has a specific responsibility.

The OSI model is especially useful for understanding, troubleshooting, and analyzing network communication step by step.

---

# TCP/IP Model

The **TCP/IP Model** describes the protocol architecture used by modern networks and the Internet.

It is commonly represented using four layers:

```text
Application
Transport
Internet
Link
```

TCP/IP is not limited to TCP and IP alone. It refers to an entire family of protocols that includes protocols such as:

- TCP
- UDP
- IP
- ICMP

---

# OSI vs TCP/IP

The two models organize similar networking functions differently.

```text
OSI                       TCP/IP

Application ───────┐
Presentation ──────┼────► Application
Session ───────────┘

Transport ──────────────► Transport

Network ────────────────► Internet

Data Link ────────┐
Physical ─────────┴─────► Link
```

The main difference is their purpose:

```text
OSI
│
└── Reference model used to understand
    networking in detail

TCP/IP
│
└── Practical protocol model used by
    real networks and the Internet
```

---

# Protocol Data Units

At different layers, transmitted information is represented using different **Protocol Data Units (PDUs)**.

A simplified mapping is:

| Layer | PDU |
|---|---|
| Application | Data |
| Transport | Segment / Datagram |
| Network | Packet |
| Data Link | Frame |
| Physical | Bits |

For example:

```text
Application
    │
    ▼
   Data
    │
Transport
    ▼
 Segment
    │
Network
    ▼
 Packet
    │
Data Link
    ▼
 Frame
    │
Physical
    ▼
  Bits
```

---

# Encapsulation

When data is sent through a network, it moves down the networking stack.

Each layer adds information required for its function.

This process is called:

**Encapsulation**

Example:

```text
Application Data
      │
      ▼
┌──────────────────────┐
│ Data                 │
└──────────────────────┘

      ↓ Transport

┌──────────────────────┐
│ TCP Header           │
│ Data                 │
└──────────────────────┘

      ↓ Network

┌──────────────────────┐
│ IP Header            │
│ TCP Header           │
│ Data                 │
└──────────────────────┘

      ↓ Data Link

┌──────────────────────┐
│ MAC / Frame Header   │
│ IP Header            │
│ TCP Header           │
│ Data                 │
└──────────────────────┘
```

Each layer receives the PDU from the layer above and adds its own header.

---

# Decapsulation

When the data reaches the destination, the process is reversed.

This is called:

**Decapsulation**

```text
Bits
 │
 ▼
Frame
 │
 ▼
Packet
 │
 ▼
Segment
 │
 ▼
Application Data
```

Each layer removes and processes its corresponding information before passing the remaining data upward.

---

# Example Data Flow

Suppose a client accesses a website.

The application generates data:

```text
HTTP Request
```

At the Transport Layer:

```text
TCP Header
+
HTTP Data
```

At the Network Layer:

```text
IP Header
+
TCP Segment
```

At the Data Link Layer:

```text
Frame Header
+
IP Packet
```

Finally, the Physical Layer transmits the information as bits.

The destination host performs the reverse process.

---

# Why Both Models Matter

For cybersecurity, both models are useful.

The **TCP/IP model** provides a practical overview of how communication occurs.

The **OSI model** provides a more detailed way to isolate and analyze specific parts of network communication.

For example:

```text
Layer 2 → MAC addresses
Layer 3 → IP addresses
Layer 4 → TCP/UDP and ports
Layer 7 → HTTP, DNS, FTP
```

This becomes particularly useful when analyzing captured network traffic.

---

# Cybersecurity Perspective

When troubleshooting or investigating network activity, thinking in layers helps answer questions such as:

```text
Is the host reachable?
        │
        └── Layer 3

Is the port open?
        │
        └── Layer 4

Is the web server responding correctly?
        │
        └── Layer 7
```

The models provide a structured approach for breaking communication problems into smaller parts.

This is especially useful for:

- Packet analysis
- Network enumeration
- Troubleshooting
- Firewall analysis
- IDS/IPS monitoring
- Pentesting
- Incident investigation

---

# Key Takeaways

- The **OSI Model** contains seven layers.
- The **TCP/IP Model** commonly contains four layers.
- OSI is primarily a reference model.
- TCP/IP represents the protocol architecture used by the Internet.
- TCP/IP refers to an entire protocol family, not only TCP and IP.
- Data changes representation as it moves through the networking layers.
- **Encapsulation** adds layer-specific information before transmission.
- **Decapsulation** removes this information at the destination.
- PDUs include data, segments/datagrams, packets, frames, and bits.
- Both models are useful for analyzing network communication in cybersecurity.