# Network Layer

The **Network Layer** is **Layer 3 (L3)** of the OSI model.

Its main responsibility is moving packets from a **source to a destination**, including communication across different networks.

Its two fundamental functions are:

- **Logical Addressing**
- **Routing**

```text
Layer 3
   │
   ├── Logical Addressing
   │       └── Where is the destination?
   │
   └── Routing
           └── How do we reach it?
```

---

# Logical Addressing

Layer 3 uses logical addresses to identify network nodes.

The main example is the:

**IP Address**

```text
Source IP
192.168.1.10

Destination IP
10.10.10.25
```

These addresses allow network devices to determine where packets originate and where they need to go.

---

# Routing

When the destination is not directly reachable, packets must travel through intermediate network nodes.

Routers perform this forwarding.

```text
Source
   │
   ▼
Router A
   │
   ▼
Router B
   │
   ▼
Router C
   │
   ▼
Destination
```

Each router examines addressing information and determines the next node toward the destination.

The packet is therefore transferred:

```text
Node → Node → Node → Destination
```

---

# Routing Tables

Routers use **routing tables** to determine where packets should be forwarded.

Conceptually:

```text
Destination Network     Next Hop
10.10.10.0/24       →   Router A
172.16.0.0/16       →   Router B
0.0.0.0/0           →   Default Gateway
```

Based on the destination address, the device determines the appropriate route.

This is the same concept behind commands such as:

```bash
ip route
```

and:

```bash
ip route get <TARGET_IP>
```

These allow us to inspect how the operating system intends to reach a destination.

---

# Communication Across Networks

If two hosts belong to different networks, they may not be able to communicate directly.

For example:

```text
192.168.1.10
     │
     │ Network A
     ▼
   Router
     │
     │ Network B
     ▼
10.10.10.20
```

The router acts as an intermediate node and forwards the packet toward the destination network.

This is one of the central responsibilities of Layer 3.

---

# Packet Forwarding

Intermediate routers generally do not need to process the application data contained in the packet.

Their concern is primarily the Layer 3 information required to forward it.

```text
Packet arrives
      │
      ▼
Examine destination
      │
      ▼
Check routing information
      │
      ▼
Determine next node
      │
      ▼
Forward packet
```

The process continues until the packet reaches its destination.

---

# Layer 3 Protocols

Protocols mentioned for the Network Layer include:

- `IPv4`
- `IPv6`
- `IPsec`
- `ICMP`
- `IGMP`
- `RIP`
- `OSPF`

---

## IPv4 / IPv6

IPv4 and IPv6 provide logical addressing used to identify hosts and networks.

```text
IPv4
192.168.1.10

IPv6
2001:db8::10
```

---

## ICMP

**ICMP — Internet Control Message Protocol**

ICMP is used for network control and diagnostic communication.

A tool we have already encountered that uses ICMP is:

```bash
ping
```

Conceptually:

```text
Host A
  │
  │ ICMP
  ▼
Host B
```

---

## RIP / OSPF

`RIP` and `OSPF` are routing protocols.

They help network devices exchange information used for routing decisions.

```text
Router A ←──── routing information ────→ Router B
```

We do not need to deeply understand these protocols yet, but it is useful to recognize that they are associated with routing.

---

# Relation to What We Already Used

During the HTB lab environment, we encountered:

```bash
ip route
```

For example, when connected to the HTB VPN, a route may direct certain destination networks through:

```text
tun0
```

Conceptually:

```text
Target IP
   │
   ▼
Routing Table
   │
   ▼
Route through tun0
   │
   ▼
HTB VPN
   │
   ▼
Target
```

This is Layer 3 in practice.

The operating system examines the **destination IP**, checks its **routing table**, selects the appropriate route/interface, and sends the packet toward the next node.

---

# Layer 3 in the OSI Model

```text
7  Application
6  Presentation
5  Session
4  Transport
3  Network       ← IP / Routing
2  Data Link
1  Physical
```

A useful association is:

```text
Layer 3
   │
   ├── IP Addresses
   ├── Packets
   └── Routing
```

---

# Cybersecurity Perspective

Layer 3 is extremely important in cybersecurity because it determines **which hosts and networks we can reach and through which route**.

When working with a target such as:

```text
10.129.25.30
```

questions we may ask include:

```text
What network is this IP part of?

Is there a route to this network?

Which interface will be used?

Does the traffic go through tun0?

Is the host reachable?
```

Commands such as:

```bash
ip route
ip route get <TARGET_IP>
ping <TARGET_IP>
```

help answer these questions.

Later, this becomes particularly important for:

- Network enumeration
- VPNs
- Subnetting
- Internal networks
- Pivoting
- Network segmentation

---

# Key Takeaways

- The Network Layer is **Layer 3** of the OSI model.
- Its two primary responsibilities are **logical addressing** and **routing**.
- IP addresses provide logical addressing.
- Routers forward packets between networks.
- Routing tables determine how destinations should be reached.
- Packets may pass through several intermediate routers before reaching the destination.
- IPv4, IPv6, ICMP, IPsec, IGMP, RIP, and OSPF are protocols associated with this layer.
- `ip route` allows us to inspect routing information on Linux.
- Layer 3 becomes especially important when working with VPNs, subnets, routing, and pivoting.