# IP Addresses

IP addresses are used to identify hosts and networks and allow communication beyond the local network.

A **MAC address** is sufficient for communication within a local network segment, but communication between different networks requires logical addressing using:

- IPv4
- IPv6

A simplified analogy is:

```text
IP Address → Building address and district
MAC Address → Exact floor and apartment
```

---

# IPv4 Structure

An **IPv4 address** contains:

**32 bits**

These bits are divided into four groups of 8 bits called **octets**.

```text
8 bits    8 bits    8 bits    8 bits
   │         │         │         │
   ▼         ▼         ▼         ▼
192    .    168   .    10    .   39
```

Each octet can represent values from:

```text
0 - 255
```

Therefore:

```text
IPv4 = 32 bits
     = 4 octets
     = 4 bytes
```

Example:

```text
192.168.10.39
```

---

# Network and Host Portions

An IPv4 address can be divided into two logical portions:

```text
Network Portion | Host Portion
```

The **network portion** identifies the network.

The **host portion** identifies a specific host within that network.

For example, with:

```text
IP:      192.168.10.39
Mask:    255.255.255.0
CIDR:    /24
```

we can visualize:

```text
192.168.10 | 39
──────────   ──
 Network     Host
```

The subnet mask determines where this division occurs.

---

# IPv4 Classes

Historically, IPv4 networks were divided into classes.

| Class | Range | Default Mask | CIDR |
|---|---|---|---|
| A | 1.0.0.0 - 127.255.255.255 | 255.0.0.0 | /8 |
| B | 128.0.0.0 - 191.255.255.255 | 255.255.0.0 | /16 |
| C | 192.0.0.0 - 223.255.255.255 | 255.255.255.0 | /24 |
| D | 224.0.0.0 - 239.255.255.255 | Multicast | — |
| E | 240.0.0.0 - 255.255.255.255 | Reserved | — |

Modern networks use **CIDR** rather than relying on fixed class boundaries.

---

# Subnet Mask

A **subnet mask** determines which bits of an IPv4 address represent:

```text
Network | Host
```

Example:

```text
IP Address:   192.168.10.39
Subnet Mask:  255.255.255.0
```

In binary:

```text
IP Address:

11000000.10101000.00001010.00100111

Subnet Mask:

11111111.11111111.11111111.00000000
```

The `1` bits of the mask represent the **network portion**.

The `0` bits represent the **host portion**.

```text
11111111.11111111.11111111 | 00000000
───────────────────────────   ────────
          Network               Host
```

---

# Network Address

The **network address** identifies the subnet itself.

Consider:

```text
192.168.10.39/24
```

Because `/24` means the first 24 bits represent the network:

```text
Network: 192.168.10.0
```

The network address itself is reserved and is not assigned as a normal host address.

---

# Broadcast Address

The **broadcast address** represents all hosts within a subnet.

It is the last IPv4 address of the subnet.

For:

```text
192.168.10.39/24
```

the network is:

```text
192.168.10.0/24
```

and the broadcast address is:

```text
192.168.10.255
```

Therefore:

```text
Network:     192.168.10.0
First Host:  192.168.10.1
...
Last Host:   192.168.10.254
Broadcast:   192.168.10.255
```

---

# Default Gateway

The **default gateway** is normally the router used by a host to communicate with other networks.

Example:

```text
Laptop
192.168.10.39
      │
      │
      ▼
Default Gateway
192.168.10.1
      │
      ▼
Other Networks
```

It is common for the gateway to use the first or last assignable address of a subnet.

For example:

```text
192.168.10.1
```

However, this is a convention rather than a technical requirement.

---

# Binary System

IPv4 addressing relies heavily on binary.

Each octet contains eight bits with the following values:

```text
128  64  32  16  8  4  2  1
```

Each bit can be:

```text
0 → value not included
1 → value included
```

---

# Binary to Decimal

Consider:

```text
11000000
```

Using the bit values:

```text
128 64 32 16 8 4 2 1
 1   1  0  0 0 0 0 0
```

We add the positions containing `1`:

```text
128 + 64 = 192
```

Therefore:

```text
11000000 = 192
```

---

# IPv4 Binary Example

Consider:

```text
192.168.10.39
```

Its binary representation is:

```text
192         168         10          39

11000000 . 10101000 . 00001010 . 00100111
```

Breaking it down:

```text
192 = 128 + 64

168 = 128 + 32 + 8

10  = 8 + 2

39  = 32 + 4 + 2 + 1
```

---

# Important Binary Values

These values are useful when working with subnet masks:

```text
Binary       Decimal

00000000  →    0
10000000  →  128
11000000  →  192
11100000  →  224
11110000  →  240
11111000  →  248
11111100  →  252
11111110  →  254
11111111  →  255
```

These values appear constantly in subnetting.

---

# CIDR

**CIDR — Classless Inter-Domain Routing**

CIDR represents how many bits of an IPv4 address belong to the network portion.

Example:

```text
192.168.10.39/24
```

The `/24` means:

```text
24 network bits
```

which corresponds to:

```text
11111111.11111111.11111111.00000000
```

or:

```text
255.255.255.0
```

Therefore:

```text
/24 = 255.255.255.0
```

---

# CIDR Examples

```text
/8
11111111.00000000.00000000.00000000
255.0.0.0
```

```text
/16
11111111.11111111.00000000.00000000
255.255.0.0
```

```text
/24
11111111.11111111.11111111.00000000
255.255.255.0
```

CIDR therefore provides a compact representation of the subnet mask.

---

# Example: 192.168.10.39/24

Given:

```text
192.168.10.39/24
```

we know:

```text
IP Address:      192.168.10.39

CIDR:            /24

Subnet Mask:     255.255.255.0

Network Address: 192.168.10.0

Broadcast:       192.168.10.255

First Host:      192.168.10.1

Last Host:       192.168.10.254
```

Visually:

```text
192.168.10.0
     │
     ├── 192.168.10.1
     ├── 192.168.10.2
     ├── ...
     ├── 192.168.10.39  ← Our Host
     ├── ...
     ├── 192.168.10.254
     │
     └── 192.168.10.255
```

---

# Why This Matters in Cybersecurity

When we encounter something like:

```text
10.10.10.0/24
```

we are not looking at a single target.

We are describing a **network range**.

Understanding CIDR allows us to determine:

- Which network a host belongs to
- Which addresses belong to that network
- Which hosts may exist within a subnet
- Network and broadcast addresses
- Which ranges should be enumerated
- How routing between networks works

This becomes fundamental for:

```text
Network Enumeration
       │
       ├── Subnet identification
       ├── Host discovery
       ├── Routing
       ├── Network scanning
       └── Pivoting
```

---

# Quick Reference

```text
IPv4
│
├── 32 bits
├── 4 octets
└── Each octet = 0-255


192.168.10.39/24
│
├── IP       → 192.168.10.39
├── /24      → Network bits
├── Mask     → 255.255.255.0
├── Network  → 192.168.10.0
└── Broadcast→ 192.168.10.255
```

---

# Key Takeaways

- IPv4 addresses contain **32 bits divided into four octets**.
- Each octet ranges from `0` to `255`.
- An IP address contains a **network portion** and a **host portion**.
- The subnet mask determines the division between network and host bits.
- The **network address** identifies the subnet.
- The **broadcast address** is the last address of the subnet.
- The **default gateway** allows communication with other networks.
- CIDR specifies how many bits belong to the network portion.
- `/24` corresponds to `255.255.255.0`.
- Binary knowledge is important for understanding subnetting.
- Modern addressing primarily uses **CIDR** rather than fixed network classes.