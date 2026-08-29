# Network Concepts

Modern computer networks are largely built around the **TCP/IP protocol suite**. Understanding how network communication is organized helps us analyze how devices exchange information and, from a cybersecurity perspective, where different security mechanisms and attacks operate.

Two models are particularly useful for understanding network communication:

* **OSI Model**
* **TCP/IP Model**

This section also introduces common network protocols and the different ways data can be transmitted.

---

## OSI Model

The **Open Systems Interconnection (OSI) Model** is a conceptual framework that divides network communication into **seven layers**.

Each layer is responsible for a specific part of the communication process.

```text
┌─────────────────────────────┐
│ 7 - Application             │
├─────────────────────────────┤
│ 6 - Presentation            │
├─────────────────────────────┤
│ 5 - Session                 │
├─────────────────────────────┤
│ 4 - Transport               │
├─────────────────────────────┤
│ 3 - Network                 │
├─────────────────────────────┤
│ 2 - Data Link               │
├─────────────────────────────┤
│ 1 - Physical                │
└─────────────────────────────┘
```

### Layer 1 — Physical

The **Physical Layer** handles the transmission of raw bits through a physical medium.

It deals with the physical infrastructure used to establish network communication.

Examples include:

* Ethernet cables
* Hubs
* Repeaters
* Physical connectors
* Electrical or optical signals

**Data unit:** Bits

---

### Layer 2 — Data Link

The **Data Link Layer** provides communication between devices that are directly connected on the same network segment.

It organizes data into **frames** and deals with functions such as error detection and local device identification.

Devices are commonly identified at this layer through **MAC addresses**.

Common Layer 2 devices include:

* Switches
* Bridges

**Data unit:** Frames

**Addressing:** MAC Address

---

### Layer 3 — Network

The **Network Layer** is responsible for logical addressing and routing traffic between different networks.

Routers use information such as **IP addresses** to determine where packets should be forwarded.

Typical responsibilities include:

* Logical addressing
* Routing
* Path determination
* Packet forwarding

Common device:

* Router

**Data unit:** Packets

**Addressing:** IP Address

---

### Layer 4 — Transport

The **Transport Layer** provides end-to-end communication between applications.

It can handle:

* Segmentation
* Reassembly
* Flow control
* Error checking
* Reliable or unreliable delivery

Two fundamental protocols operate at this layer:

#### TCP

**Transmission Control Protocol (TCP)** provides reliable, connection-oriented communication.

It prioritizes:

* Reliable delivery
* Correct ordering
* Error recovery

#### UDP

**User Datagram Protocol (UDP)** provides connectionless communication without guaranteed delivery.

It reduces communication overhead and is useful when speed is more important than guaranteed delivery.

**Data unit:** Segments (TCP) / Datagrams (UDP)

---

### Layer 5 — Session

The **Session Layer** manages communication sessions between applications.

Its responsibilities include:

* Establishing sessions
* Maintaining sessions
* Terminating sessions
* Coordinating ongoing communication

It can also help applications resume communication after interruptions.

---

### Layer 6 — Presentation

The **Presentation Layer** determines how information is represented between systems.

Typical responsibilities include:

* Data formatting
* Encoding
* Encryption and decryption
* Compression and decompression

It essentially ensures that data produced by one system can be correctly interpreted by another.

---

### Layer 7 — Application

The **Application Layer** provides network services that applications can use.

Common protocols associated with this layer include:

| Protocol | Purpose                |
| -------- | ---------------------- |
| **HTTP** | Web communication      |
| **FTP**  | File transfer          |
| **SMTP** | Email transmission     |
| **DNS**  | Domain name resolution |

This layer acts as the interface between application software and network communication.

---

## Data Flow Through the OSI Model

When information is sent through a network, it moves down through the OSI layers on the sender and back up the layers on the receiver.

A simplified example:

```text
Sender

Application
    ↓
Presentation
    ↓
Session
    ↓
Transport
    ↓
Network
    ↓
Data Link
    ↓
Physical
    │
    │ Network
    ↓
Physical
    ↓
Data Link
    ↓
Network
    ↓
Transport
    ↓
Session
    ↓
Presentation
    ↓
Application

Receiver
```

For example, when transferring a file:

1. The **Application Layer** initiates the transfer.
2. The **Presentation Layer** handles representation and potentially encryption.
3. The **Session Layer** manages the communication session.
4. The **Transport Layer** segments the data.
5. The **Network Layer** determines how packets reach the destination network.
6. The **Data Link Layer** creates frames for local delivery.
7. The **Physical Layer** transmits the resulting bits.

---

# TCP/IP Model

The **TCP/IP Model** provides a more practical representation of network communication and is the foundation of modern Internet networking.

Unlike the seven-layer OSI model, TCP/IP is commonly represented using **four layers**:

```text
┌─────────────────────────────┐
│ Application                 │
├─────────────────────────────┤
│ Transport                   │
├─────────────────────────────┤
│ Internet                    │
├─────────────────────────────┤
│ Link                        │
└─────────────────────────────┘
```

---

## Link Layer

The **Link Layer** handles communication with the physical network and local network technologies.

Examples include:

* Ethernet
* Wi-Fi

It roughly combines the responsibilities of the OSI:

* Physical Layer
* Data Link Layer

---

## Internet Layer

The **Internet Layer** handles logical addressing and routing packets between networks.

Important protocols include:

* **IP — Internet Protocol**
* **ICMP — Internet Control Message Protocol**

It corresponds approximately to the **Network Layer** of the OSI model.

---

## Transport Layer

The TCP/IP **Transport Layer** provides end-to-end communication between applications.

Its main protocols are:

* **TCP**
* **UDP**

It corresponds to the **Transport Layer** of the OSI model.

---

## Application Layer

The TCP/IP **Application Layer** provides network services directly to applications.

Examples include:

* HTTP
* FTP
* SMTP

It combines responsibilities associated with the top three OSI layers:

```text
OSI                         TCP/IP

Application ─────┐
Presentation ────┼──────► Application
Session ─────────┘

Transport ───────────────► Transport

Network ─────────────────► Internet

Data Link ───────┐
Physical ────────┴───────► Link
```

---

## OSI vs TCP/IP

| OSI          | TCP/IP      | Main Function                  |
| ------------ | ----------- | ------------------------------ |
| Application  | Application | Application network services   |
| Presentation | Application | Data representation            |
| Session      | Application | Session management             |
| Transport    | Transport   | End-to-end communication       |
| Network      | Internet    | Logical addressing and routing |
| Data Link    | Link        | Local network communication    |
| Physical     | Link        | Physical transmission          |

The main difference is their purpose:

**OSI** is primarily a conceptual model used to understand and describe network functions.

**TCP/IP** represents the protocol architecture actually used by modern networks and the Internet.

---

# Network Protocols

A **protocol** is a standardized set of rules that determines how systems communicate and how data should be formatted and processed.

Different protocols perform different functions at different layers of the network stack.

| Protocol | Layer              | Main Purpose                                |
| -------- | ------------------ | ------------------------------------------- |
| **HTTP** | Application        | Web communication                           |
| **FTP**  | Application        | File transfer                               |
| **SMTP** | Application        | Email transmission                          |
| **TCP**  | Transport          | Reliable, connection-oriented communication |
| **UDP**  | Transport          | Fast, connectionless communication          |
| **IP**   | Network / Internet | Addressing and routing packets              |

Protocols allow systems created by different manufacturers and running different software to communicate using standardized rules.

---

# Transmission

**Transmission** is the process of sending information from one device to another through some communication medium.

It can be analyzed according to:

* Transmission type
* Transmission mode
* Transmission medium

---

## Transmission Types

### Analog

Analog transmission represents information using **continuous signals**.

Example:

* Traditional radio communication

### Digital

Digital transmission represents information using discrete values, typically **bits (0 and 1)**.

It is the primary form used by modern computer networks.

---

## Transmission Modes

Transmission modes describe the direction in which information can flow between devices.

### Simplex

Communication occurs in **only one direction**.

```text
A ─────────► B
```

The receiver cannot send information back through the same communication channel.

---

### Half-Duplex

Communication can occur in **both directions**, but only one side can transmit at a time.

```text
A ◄────────► B

one direction at a time
```

A common example is a walkie-talkie.

---

### Full-Duplex

Both devices can transmit and receive **simultaneously**.

```text
A ◄════════► B

both directions simultaneously
```

A telephone call is a common example.

---

# Transmission Media

The **transmission medium** is the mechanism through which network signals travel.

It can be divided into **wired** and **wireless** media.

## Wired Media

### Twisted Pair

Commonly used in Ethernet LANs.

### Coaxial Cable

Historically used by early Ethernet networks and still commonly associated with cable communication systems.

### Fiber Optic

Uses pulses of light to transmit information.

It is widely used for high-speed and long-distance network infrastructure.

---

## Wireless Media

### Radio Waves

Used by technologies such as:

* Wi-Fi
* Cellular networks

### Microwaves

Used in applications including:

* Point-to-point wireless communication
* Satellite communication

### Infrared

Typically used for short-range communication.

---

# Cybersecurity Perspective

The OSI and TCP/IP models are especially useful in cybersecurity because they provide a structured way to understand where communication occurs and where security controls or attacks may operate.

For example:

```text
Application      → Web attacks, DNS, HTTP
Transport        → TCP/UDP, ports, connection analysis
Network          → IP addressing, routing, ICMP
Data Link        → MAC addresses, switching, local network attacks
Physical         → Physical infrastructure
```

When analyzing network traffic, performing enumeration, configuring firewalls, or investigating an attack, identifying the relevant network layer helps determine **what is happening and where to investigate**.

---

# Key Takeaways

* The **OSI Model** divides network communication into seven conceptual layers.
* The **TCP/IP Model** provides a practical four-layer architecture used by modern networks.
* OSI is especially useful for understanding and troubleshooting network communication.
* TCP/IP forms the foundation of Internet communication.
* **TCP** prioritizes reliable delivery, while **UDP** provides connectionless communication with less overhead.
* **IP** handles logical addressing and routing between networks.
* Protocols define standardized rules that allow systems to communicate.
* Network transmission can be **analog or digital**.
* Communication can operate in **simplex, half-duplex, or full-duplex** modes.
* Transmission media can be **wired or wireless**.
* Understanding network layers is fundamental for traffic analysis, enumeration, network defense, and offensive security.
