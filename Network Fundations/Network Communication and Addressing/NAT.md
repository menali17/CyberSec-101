# Network Address Translation (NAT)

**Network Address Translation (NAT)** allows devices using private IP addresses to communicate with external networks by translating those addresses into public IP addresses.

NAT became especially important because IPv4 provides a limited address space. Instead of requiring every internal device to have its own public IPv4 address, NAT allows multiple devices inside a private network to share one or more public addresses.

---

## Why NAT Exists

IPv4 provides roughly:

```text
2^32 ≈ 4.3 billion addresses
```

As the Internet expanded, the number of available public IPv4 addresses became insufficient for every connected device.

NAT helps reduce the demand for public IPv4 addresses by allowing networks to use:

* **Private IP addresses internally**
* **Public IP addresses externally**

A typical home network may contain many devices while using only one public IPv4 address to access the Internet.

---

# Public vs Private IP Addresses

## Public IP Addresses

A **public IP address** is globally unique and can be routed across the Internet.

These addresses are usually assigned by an:

**Internet Service Provider (ISP)**

Examples include:

```text
8.8.8.8
142.251.46.174
```

Public IP addresses allow systems to communicate across the global Internet.

---

## Private IP Addresses

**Private IP addresses** are intended for use inside local networks.

They are not routed across the public Internet.

Common environments include:

* Homes
* Offices
* Schools
* Laboratories
* Internal corporate networks

RFC 1918 defines three major private IPv4 ranges:

| Range                           | CIDR             |
| ------------------------------- | ---------------- |
| `10.0.0.0 - 10.255.255.255`     | `10.0.0.0/8`     |
| `172.16.0.0 - 172.31.255.255`   | `172.16.0.0/12`  |
| `192.168.0.0 - 192.168.255.255` | `192.168.0.0/16` |

A common home network might therefore use addresses such as:

```text
192.168.1.10
192.168.1.11
192.168.1.12
```

These addresses can be reused by many different private networks because they do not need to be globally unique.

---

# Private vs Public Addressing

A simplified network might look like:

```text
Private Network                     Internet

192.168.1.10 ─┐
192.168.1.11 ─┤
192.168.1.12 ─┼── Router / NAT ─── 203.0.113.50
192.168.1.13 ─┘
```

Internally, devices communicate using their private addresses.

Externally, Internet systems see the router's public address instead.

---

# What is NAT?

**Network Address Translation** is a process typically performed by a router or similar network device.

NAT modifies information inside IP packet headers as traffic passes between networks.

The translation can involve:

* Source IP addresses
* Destination IP addresses
* Port numbers, depending on the NAT type

A common use is translating a device's private source IP into the router's public IP before sending traffic to the Internet.

---

# How NAT Works

Consider the following home network:

```text
Laptop        192.168.1.10
Smartphone    192.168.1.11
Console       192.168.1.12

Router LAN:   192.168.1.1
Router WAN:   203.0.113.50
```

The router connects two sides:

```text
LAN                                WAN

192.168.1.0/24
      │
      ▼
┌─────────────────────┐
│       Router        │
│                     │
│ LAN: 192.168.1.1    │
│ WAN: 203.0.113.50   │
└─────────────────────┘
      │
      ▼
   Internet
```

---

## Outbound Connection

Suppose the laptop sends a request to an Internet server.

Initially, the packet contains the laptop's private source address:

```text
Source IP:      192.168.1.10
Destination IP: Internet Server
```

The router receives the packet and performs NAT.

The source address is translated:

```text
Before NAT

Source:
192.168.1.10

       ↓

After NAT

Source:
203.0.113.50
```

The external server therefore communicates with the router's public address rather than directly with the laptop's private address.

---

# NAT Table

The router needs a way to remember which internal device initiated each connection.

It therefore maintains a **NAT table**.

For example:

```text
Internal                   External

192.168.1.10:5555  ↔  203.0.113.50:4444
```

This mapping allows the router to identify which internal host should receive returning traffic.

---

# Return Traffic

Suppose the Internet server responds to:

```text
203.0.113.50:4444
```

The router checks its NAT table and discovers:

```text
203.0.113.50:4444
        │
        ▼
192.168.1.10:5555
```

The router translates the destination information and forwards the response to the correct internal device.

The entire process can therefore be simplified as:

```text
Laptop
192.168.1.10:5555
        │
        │ NAT
        ▼
Router
203.0.113.50:4444
        │
        ▼
Internet Server
```

And for the response:

```text
Internet Server
        │
        ▼
203.0.113.50:4444
        │
        │ NAT Table
        ▼
192.168.1.10:5555
        │
        ▼
Laptop
```

---

# Types of NAT

Different NAT mechanisms can be used depending on the network requirements.

The three main types introduced here are:

* Static NAT
* Dynamic NAT
* Port Address Translation (PAT)

---

## Static NAT

**Static NAT** creates a fixed **one-to-one mapping** between a private IP address and a public IP address.

Example:

```text
192.168.1.10  ↔  203.0.113.10
```

The same internal host always maps to the same public address.

Conceptually:

```text
Private IP          Public IP

192.168.1.10  ───► 203.0.113.10
192.168.1.11  ───► 203.0.113.11
192.168.1.12  ───► 203.0.113.12
```

This requires one public address for every configured private mapping.

---

# Dynamic NAT

**Dynamic NAT** uses a pool of available public IP addresses.

When an internal host needs Internet access, NAT assigns one of the available public addresses.

For example:

```text
Private Network

192.168.1.10
192.168.1.11
192.168.1.12

        │
        ▼

Public Address Pool

203.0.113.50
203.0.113.51
203.0.113.52
```

Unlike Static NAT, the mapping does not necessarily remain permanently associated with the same device.

---

# Port Address Translation (PAT)

**Port Address Translation (PAT)** allows multiple private hosts to share a **single public IP address**.

PAT is also commonly called:

**NAT Overload**

This is the form commonly used in home and small office networks.

For example:

```text
192.168.1.10:51000 ─┐
192.168.1.11:52000 ─┼──► 203.0.113.50
192.168.1.12:53000 ─┘
```

The router distinguishes the different connections using port numbers.

A NAT table might contain:

```text
Private Address          Public Mapping

192.168.1.10:51000  →  203.0.113.50:40001
192.168.1.11:52000  →  203.0.113.50:40002
192.168.1.12:53000  →  203.0.113.50:40003
```

Therefore, many devices can simultaneously access external networks using the same public IPv4 address.

---

# Comparing NAT Types

| NAT Type        | Mapping                                              | Public Addresses          |
| --------------- | ---------------------------------------------------- | ------------------------- |
| **Static NAT**  | One private IP ↔ one public IP                       | One per mapping           |
| **Dynamic NAT** | Private IP ↔ address from a pool                     | Multiple public addresses |
| **PAT**         | Many private connections ↔ one public IP using ports | Usually one               |

PAT is especially important because it allows a large number of internal devices to operate behind a single public address.

---

# NAT Example

Consider:

```text
Laptop:
192.168.1.10:5555

Router:
192.168.1.1
203.0.113.50

Remote Server:
203.0.135.60
```

The communication might look like:

```text
192.168.1.10:5555
        │
        ▼
      NAT
        │
        ▼
203.0.113.50:4444
        │
        ▼
203.0.135.60
```

The remote server sees:

```text
203.0.113.50:4444
```

instead of:

```text
192.168.1.10:5555
```

When the response returns, the NAT table allows the router to restore the correct internal destination.

---

# NAT and Ports

The previous section introduced ports as identifiers for specific applications and connections.

With PAT, ports become additionally useful because they allow a router to distinguish connections made by multiple internal systems.

For example:

```text
Laptop       192.168.1.10:50001
Phone        192.168.1.11:50002
Console      192.168.1.12:50003
```

All three can share:

```text
203.0.113.50
```

while the NAT device maintains separate mappings for each connection.

---

# Benefits of NAT

## IPv4 Address Conservation

NAT reduces the number of public IPv4 addresses required by a network.

Instead of:

```text
Device 1 → Public IP
Device 2 → Public IP
Device 3 → Public IP
Device 4 → Public IP
```

PAT allows:

```text
Device 1 ─┐
Device 2 ─┤
Device 3 ─┼──► One Public IP
Device 4 ─┘
```

This significantly extends the usefulness of the IPv4 address space.

---

## Internal Addressing Flexibility

Organizations can design their private addressing scheme independently of their public Internet addresses.

For example:

```text
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16
```

can be used internally without requiring globally unique public allocations for every device.

---

## Reduced Direct Exposure

Internal private addresses are not directly routed across the public Internet.

External systems normally communicate with the NAT device's public address rather than directly addressing internal private hosts.

This reduces direct exposure of the internal addressing structure.

However, NAT should **not** be treated as a replacement for a firewall or other security controls.

---

# NAT Trade-Offs

NAT also introduces additional complexity.

---

## Hosting Internal Services

If a server behind NAT needs to be accessible from the Internet, additional configuration may be required.

One common mechanism is:

**Port Forwarding**

Conceptually:

```text
Internet
   │
203.0.113.50:443
   │
   ▼
Router / NAT
   │
   ▼
192.168.1.20:443
Web Server
```

The NAT device must know that traffic arriving at the public address and selected port should be forwarded to a specific internal system.

---

## End-to-End Connectivity

Some protocols expect direct end-to-end connectivity between hosts.

NAT modifies packet addressing information, which can interfere with protocols that depend on the original addresses or ports.

Such protocols may require additional handling.

---

## Troubleshooting Complexity

NAT introduces another translation layer into network communication.

When investigating connectivity problems, administrators may need to consider:

```text
Internal Address
       │
       ▼
NAT Mapping
       │
       ▼
Public Address
       │
       ▼
Remote Destination
```

A problem can exist at any stage.

---

# Cybersecurity Perspective

NAT appears frequently during network enumeration and security assessments.

For example, discovering:

```text
192.168.1.25
```

indicates an address that belongs to an internal private network.

Meanwhile, external services may only see something such as:

```text
203.0.113.50
```

representing the public-facing NAT device.

Understanding NAT is useful when analyzing:

* Internal vs external addressing
* Network topology
* Firewall rules
* Port forwarding
* Packet captures
* Network reconnaissance
* Exposed services
* Incident traffic
* Pivoting between network segments

It is particularly important to understand that a single public address does **not necessarily represent a single device**.

With PAT, many internal hosts can communicate through the same public IP address.

---

# Key Takeaways

* **NAT** translates network addressing information as packets move between networks.
* NAT helps conserve the limited IPv4 public address space.
* **Public IP addresses** are globally routable.
* **Private IPv4 addresses** are designed for internal networks and are not routed across the public Internet.
* RFC 1918 defines the private ranges `10.0.0.0/8`, `172.16.0.0/12`, and `192.168.0.0/16`.
* **Static NAT** creates a one-to-one private-to-public mapping.
* **Dynamic NAT** assigns public addresses from a pool.
* **PAT**, or NAT Overload, allows many private devices to share one public IP using different port mappings.
* NAT devices maintain translation information so response traffic can reach the correct internal host.
* Hosting services behind NAT may require mechanisms such as port forwarding.
* NAT can reduce direct exposure of internal addressing, but it is not a substitute for a firewall.
