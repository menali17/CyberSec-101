# The OSI Model

The **OSI (Open Systems Interconnection) Model** is a reference model designed to describe how different systems communicate over a network.

It divides network communication into **seven layers**, with each layer responsible for specific tasks.

```text
7 ─ Application
6 ─ Presentation
5 ─ Session
4 ─ Transport
3 ─ Network
2 ─ Data Link
1 ─ Physical
```

When sending data, communication moves from **Layer 7 down to Layer 1**.

When receiving data, the process happens in reverse:

```text
SENDER                         RECEIVER

7 Application                 Application 7
      ↓                            ↑
6 Presentation                Presentation 6
      ↓                            ↑
5 Session                     Session 5
      ↓                            ↑
4 Transport                   Transport 4
      ↓                            ↑
3 Network                     Network 3
      ↓                            ↑
2 Data Link                   Data Link 2
      ↓                            ↑
1 Physical ─── transmission ─ Physical 1
```

---

# Layer 7 — Application

The **Application Layer** provides network functions used by applications and handles the input and output of application data.

```text
Application
     │
     └── Network services used by applications
```

This is the layer closest to the end-user applications.

---

# Layer 6 — Presentation

The **Presentation Layer** handles how data is represented between different systems.

Its purpose is to transform system-dependent representations into a form that can be understood independently by the application.

Think of it as:

```text
System A representation
          │
          ▼
   Presentation Layer
          │
          ▼
Common representation
          │
          ▼
System B
```

---

# Layer 5 — Session

The **Session Layer** manages the logical connection between communicating systems.

It helps control and maintain communication sessions between them.

```text
Host A
   │
   │ Communication Session
   │
Host B
```

Its role includes preventing or handling problems that could disrupt the logical connection.

---

# Layer 4 — Transport

The **Transport Layer** provides end-to-end control of transferred data.

Its responsibilities include:

- Segmenting data streams
- End-to-end communication control
- Detecting and avoiding congestion situations

```text
Application Data
      │
      ▼
Transport Layer
      │
      ▼
 Segmented Data
```

Layer 4 is especially important when analyzing communication between applications on different hosts.

---

# Layer 3 — Network

The **Network Layer** is responsible for transmitting data across networks from the sender toward the receiver.

In packet-switched networks, it handles the forwarding of packets.

```text
Host A
  │
  ▼
Network
  │
  ├──► Network
  │
  └──► Network
          │
          ▼
        Host B
```

Its main concern is getting data across the network toward its destination.

---

# Layer 2 — Data Link

The **Data Link Layer** handles communication over the respective transmission medium.

It takes the bitstream from Layer 1 and organizes it into:

**Frames**

```text
Bitstream
    │
    ▼
Data Link
    │
    ▼
 Frames
```

Its central task is enabling reliable and error-free transmission over the respective medium.

---

# Layer 1 — Physical

The **Physical Layer** represents the actual transmission of information through a physical medium.

Transmission can use:

- Electrical signals
- Optical signals
- Electromagnetic waves

These signals can travel through wired or wireless transmission media.

```text
Bits
 │
 ▼
Electrical / Optical / Radio Signals
 │
 ▼
Transmission Medium
```

This is the lowest layer of the OSI model.

---

# Layer Orientation

The OSI layers can also be grouped according to their general orientation.

## Application-Oriented Layers

```text
7 ─ Application
6 ─ Presentation
5 ─ Session
```

These are considered **application-oriented layers**.

## Transport-Oriented Layers

```text
4 ─ Transport
3 ─ Network
2 ─ Data Link
```

These are considered **transport-oriented layers**.

Layer 1 provides the physical transmission medium.

---

# How the Layers Work Together

Each layer provides services to the layer directly above it.

At the same time, it uses services provided by the layer below it.

```text
Layer 7
   │
   ▼
Layer 6
   │
   ▼
Layer 5
   │
   ▼
Layer 4
   │
   ▼
Layer 3
   │
   ▼
Layer 2
   │
   ▼
Layer 1
```

This separation allows each layer to focus on a specific part of network communication.

---

# Communication Between Two Hosts

When two systems communicate, both systems process the OSI layers.

The sender processes:

```text
Layer 7
   ↓
Layer 6
   ↓
Layer 5
   ↓
Layer 4
   ↓
Layer 3
   ↓
Layer 2
   ↓
Layer 1
```

The receiver processes them in reverse:

```text
Layer 1
   ↑
Layer 2
   ↑
Layer 3
   ↑
Layer 4
   ↑
Layer 5
   ↑
Layer 6
   ↑
Layer 7
```

Therefore, during communication, the seven layers are processed by both the sending and receiving systems.

---

# Quick Reference

| Layer | Name | Main Function |
|---:|---|---|
| **7** | Application | Application network functions and data I/O |
| **6** | Presentation | Data representation |
| **5** | Session | Logical connection management |
| **4** | Transport | End-to-end control and segmentation |
| **3** | Network | Packet forwarding across networks |
| **2** | Data Link | Frames and reliable transmission over the medium |
| **1** | Physical | Transmission using physical signals |

---

# Remembering the Model

```text
7  Application     → Application functions
6  Presentation    → Data representation
5  Session         → Logical sessions
4  Transport       → End-to-end transport
3  Network         → Network forwarding
2  Data Link       → Frames
1  Physical        → Signals
```

For network and security analysis, Layers **2, 3, 4, and 7** are especially useful to recognize quickly:

```text
L2 → Data Link
L3 → Network
L4 → Transport
L7 → Application
```

---

# Key Takeaways

- The OSI model divides network communication into **seven layers**.
- Each layer has a clearly defined responsibility.
- Layers depend on the services provided by neighboring layers.
- Layers 5–7 are application-oriented.
- Layers 2–4 are transport-oriented.
- The sender processes communication from Layer 7 down to Layer 1.
- The receiver processes communication from Layer 1 back to Layer 7.
- The model provides a structured way to understand how communication between systems occurs.