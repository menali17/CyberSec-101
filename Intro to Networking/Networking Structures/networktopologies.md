# Networking Topologies

A **network topology** describes how devices and network components are arranged and connected within a network.

A topology can describe both the physical arrangement of devices and the logical way in which data flows between them.

---

# Physical vs Logical Topology

## Physical Topology

The **physical topology** represents how devices are physically connected.

It includes elements such as:

- Cables
- Network devices
- Nodes
- Physical connections between devices

Example:

```text
PC ───── Switch ───── Router
```

It answers the question:

> How are the devices physically connected?

---

## Logical Topology

The **logical topology** describes how data is transmitted between devices, regardless of their physical arrangement.

It answers:

> How does data actually flow through the network?

Because of this, the physical and logical topology of a network do not necessarily have to be the same.

---

# Network Topology Components

A topology can be analyzed through three main elements:

## Connections

Connections represent the transmission medium.

### Wired

- Coaxial cable
- Fiber optic
- Twisted-pair cable

### Wireless

- Wi-Fi
- Cellular
- Satellite

---

## Nodes

Nodes are connection points within the network.

Examples include:

- Repeaters
- Hubs
- Bridges
- Switches
- Routers
- Modems
- Gateways
- Firewalls

These devices can transmit, receive, or forward network traffic.

---

# Basic Network Topologies

The main network topologies are:

| Topology | Basic Structure |
|---|---|
| Point-to-Point | Two directly connected devices |
| Bus | All devices share one transmission medium |
| Star | Devices connect to a central device |
| Ring | Devices form a circular path |
| Mesh | Devices have multiple connections between them |
| Tree | Hierarchical structure |
| Hybrid | Combination of different topologies |
| Daisy Chain | Devices connected sequentially |

---

# Point-to-Point

The **Point-to-Point topology** is the simplest network topology.

It consists of a direct connection between exactly two hosts.

```text
Host A ───────── Host B
```

Communication occurs directly between the two devices.

Point-to-Point topology should not be confused with **Peer-to-Peer (P2P)** architecture.

```text
Point-to-Point → Network topology
Peer-to-Peer   → Network architecture
```

---

# Bus

In a **Bus topology**, all hosts share the same transmission medium.

```text
       Host A
          │
══════════╪══════════╪══════════
          │          │
       Host B     Host C
```

There is no central network device controlling communication.

Because the medium is shared, transmitted signals are available to all connected hosts, which determine whether the data is intended for them.

A traditional example is a network using coaxial cable.

---

# Star

In a **Star topology**, every host connects to a central network component.

The central device can be:

- Switch
- Hub
- Router

```text
        Host A
          │
          │
Host B ─ Switch ─ Host C
          │
          │
        Host D
```

The central device handles the forwarding of network traffic.

This means that traffic passes through the central component before reaching another host.

---

# Ring

In a **Ring topology**, devices form a circular communication path.

```text
Host A ───── Host B
  │             │
  │             │
Host D ───── Host C
```

Each host is connected to two neighboring devices.

Traffic travels through the ring in a predetermined direction.

Some ring networks use a **token** to determine which device is allowed to transmit.

```text
Host A → Host B → Host C → Host D
  ↑                         │
  └─────────────────────────┘
```

A logical ring can also exist over a different physical topology.

---

# Mesh

A **Mesh topology** provides multiple connections between nodes.

There are two main forms:

- Fully Mesh
- Partially Mesh

---

## Fully Mesh

In a **Fully Meshed topology**, every node is directly connected to every other node.

```text
A ───────── B
|\         /|
| \       / |
|  \     /  |
|   \   /   |
|    \ /    |
C ───────── D
```

This provides high redundancy.

If one connection or router fails, traffic may still reach its destination through another path.

Because of this, mesh structures can provide high reliability.

---

## Partially Mesh

In a **Partially Meshed topology**, only some nodes have multiple connections.

```text
A ───── B
        │\
        │ \
        C ─ D
```

This reduces the number of required connections while still providing redundancy in selected parts of the network.

---

# Tree

A **Tree topology** creates a hierarchical network structure.

It can be considered an extension of the star topology.

```text
             Core
            Switch
           /      \
          /        \
      Switch A    Switch B
       /   \       /   \
      A     B     C     D
```

Tree topologies are useful for larger networks where multiple network segments need to be organized hierarchically.

They are commonly associated with larger organizational networks.

---

# Hybrid

A **Hybrid topology** combines two or more different basic network topologies.

For example:

```text
     Star Network
          │
          │
     Bus Network
          │
          │
     Star Network
```

A hybrid topology exists when different topology types are interconnected.

This allows network designs to combine characteristics from multiple structures.

---

# Daisy Chain

In a **Daisy Chain topology**, devices are connected sequentially.

```text
Host A ─ Host B ─ Host C ─ Host D ─ Host E
```

Communication may need to pass through previous nodes before reaching another device.

This type of arrangement can be found in automation technologies such as **CAN**.

---

# Topology Comparison

| Topology | Main Characteristic |
|---|---|
| **Point-to-Point** | Direct connection between two hosts |
| **Bus** | Shared transmission medium |
| **Star** | Central network component |
| **Ring** | Circular communication path |
| **Mesh** | Multiple paths between nodes |
| **Tree** | Hierarchical structure |
| **Hybrid** | Combination of different topologies |
| **Daisy Chain** | Sequential connections |

---

# Quick Recognition

A useful way to recognize each topology:

```text
POINT-TO-POINT

A ───── B


BUS

══════════════════
 │      │      │
 A      B      C


STAR

       A
       │
B ── Switch ── C
       │
       D


RING

A ─── B
│     │
D ─── C


MESH

A ─── B
|\   /|
| \ / |
| / \ |
|/   \|
C ─── D


TREE

       A
      / \
     B   C
    / \ / \
   D  E F  G


DAISY CHAIN

A ─ B ─ C ─ D
```

---

# Key Takeaways

- A **network topology** describes the structure and connections of a network.
- **Physical topology** represents how devices are physically connected.
- **Logical topology** represents how data flows through the network.
- **Point-to-Point** directly connects two hosts.
- **Bus** uses a shared transmission medium.
- **Star** connects devices through a central component.
- **Ring** creates a circular communication path.
- **Mesh** provides multiple paths and increased redundancy.
- **Tree** organizes the network hierarchically.
- **Hybrid** combines different topology types.
- **Daisy Chain** connects devices sequentially.