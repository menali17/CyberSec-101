# Dynamic Host Configuration Protocol (DHCP)

The **Dynamic Host Configuration Protocol (DHCP)** is a network management protocol used to automatically configure devices on IP networks.

Instead of requiring administrators to manually assign network settings to every device, DHCP can automatically provide parameters such as:

* IP address
* Subnet mask
* Default gateway
* DNS server addresses

This simplifies network administration and reduces configuration errors.

---

## Why DHCP Is Important

Every device connected to an IP network needs a valid IP address in order to communicate.

Without DHCP, network administrators would need to manually configure each device.

This becomes difficult in environments with:

* Many users
* Frequently changing devices
* Mobile devices
* Guest networks
* Large corporate networks

DHCP automates this process by assigning addresses from a managed pool.

It also helps prevent:

* Duplicate IP addresses
* Configuration mistakes
* Address conflicts

Unused addresses can later be returned to the available pool and reassigned to other devices.

---

# DHCP Roles

The DHCP process involves two main components:

| Role            | Description                                                          |
| --------------- | -------------------------------------------------------------------- |
| **DHCP Server** | Manages available IP addresses and network configuration parameters. |
| **DHCP Client** | Requests network configuration from the DHCP server.                 |

A DHCP server may run on:

* A router
* A dedicated server
* Another network device capable of providing DHCP services

The client can be any device that needs network configuration.

Examples include:

* Laptop
* Smartphone
* Desktop
* IoT device

---

# DORA Process

The basic DHCP address allocation process is commonly summarized using the acronym:

**DORA**

```text id="87il8q"
Discover
   ↓
Offer
   ↓
Request
   ↓
Acknowledge
```

These four steps allow a client to obtain valid network configuration from a DHCP server.

---

## 1. Discover

When a new device connects to a network, it may not yet know:

* Its IP address
* The DHCP server's address
* The network configuration

The client therefore sends a:

**DHCP Discover**

The Discover message is broadcast onto the network to locate available DHCP servers.

```text id="qpkv7u"
Client
No IP configuration
     │
     │ DHCP Discover
     ▼
Broadcast Network
```

The purpose is essentially:

> Is there a DHCP server available?

---

## 2. Offer

A DHCP server that receives the Discover message may respond with a:

**DHCP Offer**

The offer proposes an available IP address and associated network configuration.

```text id="4pgmvw"
Client
   │
   │ DHCP Discover
   ▼
DHCP Server
   │
   │ DHCP Offer
   ▼
Client
```

The offer can include parameters such as:

* Proposed IP address
* Subnet mask
* Default gateway
* DNS servers
* Lease duration

---

## 3. Request

After receiving an offer, the client responds with a:

**DHCP Request**

This indicates that the client wants to use the offered configuration.

```text id="i4rbgv"
DHCP Server
    │
    │ DHCP Offer
    ▼
  Client
    │
    │ DHCP Request
    ▼
DHCP Server
```

The client is effectively requesting that the proposed address be officially assigned to it.

---

## 4. Acknowledge

The server completes the process by sending a:

**DHCP Acknowledge (DHCP ACK)**

This confirms the assignment.

```text id="1hl3r0"
Client
   │
   │ DHCP Request
   ▼
DHCP Server
   │
   │ DHCP Acknowledge
   ▼
Client
```

The client can now use the assigned IP configuration to communicate on the network.

---

# Complete DORA Flow

The complete process can be represented as:

```text id="94sl0l"
Client                         DHCP Server
  │                                │
  │──── DHCP Discover ────────────►│
  │                                │
  │◄──── DHCP Offer ───────────────│
  │                                │
  │──── DHCP Request ─────────────►│
  │                                │
  │◄──── DHCP Acknowledge ─────────│
  │                                │
```

A useful way to remember it:

```text id="52aqrj"
D → Discover
O → Offer
R → Request
A → Acknowledge
```

---

# DHCP Lease

An IP address assigned through DHCP is normally not permanent.

Instead, it is provided for a defined period called a:

**Lease Time**

For example:

```text id="jq72t4"
Assigned IP:
192.168.1.10

Lease:
24 hours
```

During this period, the client is allowed to use that address.

The use of leases allows DHCP servers to efficiently manage a limited pool of addresses.

If a device leaves the network and no longer requires its address, that IP can eventually become available for another device.

---

# Lease Renewal

Before the lease expires, the client attempts to renew it.

The client sends another DHCP Request asking the server for permission to continue using the current address.

A simplified renewal process is:

```text id="1z4qq5"
Client                         DHCP Server
  │                                │
  │──── DHCP Request ─────────────►│
  │                                │
  │◄──── DHCP Acknowledge ─────────│
  │                                │
```

If the server approves the renewal, it sends another DHCP Acknowledge.

The device can then continue using its existing IP address for another lease period.

---

# Example Scenario

Imagine a laptop connecting to an office network.

Initially, the laptop has no valid IP configuration.

```text id="56pldk"
Laptop
No IP address
```

The following process occurs:

### Step 1 — Discover

The laptop sends:

```text id="u4x0fn"
DHCP Discover
```

This searches for DHCP servers on the network.

### Step 2 — Offer

The DHCP server responds with an available address.

For example:

```text id="jb603w"
192.168.1.10
```

### Step 3 — Request

The laptop responds that it wants to use the offered address.

```text id="pb1y80"
DHCP Request
192.168.1.10
```

### Step 4 — Acknowledge

The server confirms the assignment.

```text id="tdag4e"
DHCP ACK
192.168.1.10 assigned
```

The laptop can now communicate with other systems on the network.

---

# Example Configuration

After DHCP completes, the client may receive configuration similar to:

```text id="agx84f"
IP Address:      192.168.1.10
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.1.1
DNS Server:      192.168.1.1
```

These parameters allow the device to:

* Identify itself within the network
* Determine which systems are local
* Reach external networks
* Resolve domain names

---

# DHCP and Network Address Management

One of DHCP's main advantages is centralized address management.

Suppose a DHCP server has a pool such as:

```text id="ui4w4q"
192.168.1.100
      ↓
192.168.1.200
```

The DHCP server can dynamically distribute addresses from that pool.

For example:

```text id="820zgd"
Laptop      → 192.168.1.100
Phone       → 192.168.1.101
Tablet      → 192.168.1.102
Printer     → 192.168.1.103
```

When an address is no longer required and its lease expires, it can eventually return to the available pool.

This allows the network to reuse IP addresses efficiently.

---

# Static Configuration vs DHCP

Two common approaches can be used to configure devices.

## Static Configuration

The administrator manually defines parameters such as:

```text id="la2cp2"
IP Address
Subnet Mask
Gateway
DNS
```

Advantages:

* Predictable configuration
* Useful for infrastructure that should retain a known address

Disadvantages:

* Requires manual configuration
* More administrative work
* Greater risk of configuration mistakes

---

## DHCP Configuration

The DHCP server automatically provides the network configuration.

Advantages:

* Automatic configuration
* Reduced administrative effort
* Efficient address reuse
* Fewer manual configuration errors

DHCP is particularly useful for client devices that frequently join and leave the network.

---

# Cybersecurity Perspective

DHCP is important in cybersecurity because it plays a central role in how devices initially obtain network access.

Understanding DHCP can help during:

* Network enumeration
* Packet analysis
* Incident investigation
* Network troubleshooting
* Rogue device detection
* Infrastructure assessment

A DHCP exchange can reveal useful network configuration information, including:

* Address ranges
* Default gateway
* DNS servers
* DHCP server information

Because clients rely on DHCP to obtain important network parameters, the protocol also represents an important trust relationship inside many local networks.

---

# Key Takeaways

* **DHCP** automatically configures devices on IP networks.
* DHCP can provide an IP address, subnet mask, default gateway, and DNS server information.
* A **DHCP server** manages address allocation.
* A **DHCP client** requests configuration from the server.
* The normal DHCP allocation process is called **DORA**.
* DORA stands for **Discover, Offer, Request, Acknowledge**.
* DHCP addresses are normally assigned using a **lease**.
* A lease has a defined duration.
* Clients can request renewal before the lease expires.
* DHCP allows addresses to be returned to the available pool and reused.
* Automatic configuration reduces administrative effort and helps avoid IP address conflicts.
