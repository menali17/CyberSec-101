# Components of a Network

Computer networks depend on several hardware and software components working together to allow devices to communicate, exchange data, access services, and connect to other networks.

The main components can be divided into:

* **End Devices**
* **Intermediary Devices**
* **Network Media and Software Components**
* **Servers**

---

## End Devices

An **end device**, also known as a **host**, is a device that sends or receives data through a network.

Common examples include:

* Desktop computers
* Laptops
* Smartphones
* Tablets
* Smart TVs
* IoT devices

End devices are generally where network communication originates or terminates.

For example, when accessing a website from a laptop:

```text
Laptop ─────► Network ─────► Web Server
  │                              │
Client                       Destination
```

The laptop generates the request and receives the resulting data.

End devices can connect through both:

* **Wired connections**, such as Ethernet
* **Wireless connections**, such as Wi-Fi

---

# Intermediary Devices

**Intermediary devices** facilitate communication between end devices.

They can operate inside the same LAN or connect completely different networks.

Examples include:

* Routers
* Switches
* Modems
* Access Points

Some of their responsibilities include:

* Packet forwarding
* Traffic management
* Connecting networks
* Selecting communication paths
* Improving network reliability
* Providing security functionality

Different intermediary devices operate at different layers of the OSI model.

---

## Network Interface Card (NIC)

A **Network Interface Card (NIC)** is the hardware component that enables a device to connect to a network.

It provides an interface between the device and the network medium.

NICs can support:

### Wired connections

For example:

* Ethernet NIC

### Wireless connections

For example:

* Wi-Fi adapter

Each NIC has a **MAC address**, which is used to identify the network interface at the **Data Link Layer (Layer 2)**.

```text
Computer
   │
  NIC
   │
Ethernet / Wi-Fi
   │
Network
```

---

## Routers

A **router** connects different networks and forwards packets between them.

Routers primarily operate at:

**OSI Layer 3 — Network Layer**

They examine destination **IP addresses** and use routing information to determine where packets should be forwarded.

```text
Network A
   │
   │
Router
   │
   │
Network B
```

Routers use **routing tables** and may use routing protocols such as:

* **OSPF — Open Shortest Path First**
* **BGP — Border Gateway Protocol**

Their responsibilities include:

* Connecting different networks
* Forwarding packets
* Selecting routes
* Managing network traffic
* Helping prevent congestion

Routers can also provide security functionality through mechanisms such as:

* Firewalls
* Access Control Lists (ACLs)

A common example is a home router connecting the local home network to the ISP and ultimately to the Internet.

---

## Switches

A **switch** primarily connects devices within the **same network**, usually a LAN.

Switches commonly operate at:

**OSI Layer 2 — Data Link Layer**

Instead of using IP addresses for normal Layer 2 forwarding, switches use **MAC addresses** to determine where Ethernet frames should be sent.

```text
              ┌── PC
              │
Printer ── Switch ── Server
              │
              └── Laptop
```

This allows devices within the LAN to communicate efficiently.

Switches help:

* Connect LAN devices
* Forward traffic toward the intended device
* Reduce unnecessary traffic
* Improve network performance

---

## Hubs

A **hub** is an older networking device used to connect multiple devices.

Unlike a switch, a hub does not intelligently determine the destination of incoming traffic.

Instead, it sends received data to **all connected ports**.

```text
             ┌──► PC 1
             │
Data ──► Hub ├──► PC 2
             │
             └──► PC 3
```

Hubs operate at:

**OSI Layer 1 — Physical Layer**

This behavior can generate unnecessary traffic and collisions.

Because switches can intelligently forward traffic, they have largely replaced hubs in modern networks.

---

## Router vs Switch vs Hub

| Device     | OSI Layer | Uses          | Primary Function                    |
| ---------- | --------: | ------------- | ----------------------------------- |
| **Router** |   Layer 3 | IP addresses  | Connect different networks          |
| **Switch** |   Layer 2 | MAC addresses | Connect devices within a LAN        |
| **Hub**    |   Layer 1 | None          | Repeat traffic to connected devices |

This distinction is fundamental when analyzing network infrastructure.

---

# Network Media and Software Components

Network communication also depends on physical transmission media and software components.

These include:

### Network Media

* Ethernet cables
* Fiber-optic cables
* Wi-Fi
* Bluetooth

### Software Components

* Network protocols
* Network management software
* Software firewalls

Together, these components determine both **how data physically travels** and **how communication is controlled**.

---

## Cabling and Connectors

Network cables provide physical communication paths between devices.

Common examples include:

### Ethernet

Ethernet cables are commonly used within LANs for high-speed wired communication.

A commonly associated connector is:

**RJ-45**

For example:

```text
PC ── Ethernet/RJ-45 ── Switch
```

### Fiber Optic

Fiber-optic cables transmit information using light and are suitable for high-speed communication over long distances with minimal signal degradation.

---

# Network Protocols

**Network protocols** define standardized rules for communication between devices.

They determine how data should be:

* Segmented
* Addressed
* Routed
* Checked for errors
* Synchronized
* Transmitted
* Received
* Interpreted

Examples include:

| Protocol       | Purpose                              |
| -------------- | ------------------------------------ |
| **TCP/IP**     | Foundation of Internet communication |
| **HTTP/HTTPS** | Web communication                    |
| **FTP**        | File transfers                       |
| **SMTP**       | Email transmission                   |

Standardized protocols allow different devices, operating systems, and applications to communicate with each other.

---

# Network Management Software

**Network management software** provides administrators with tools to monitor, configure, control, and maintain network infrastructure.

Typical functions include:

* Performance monitoring
* Configuration management
* Fault analysis
* Security management

For example, administrators can use these tools to:

```text
Network Management Software
          │
          ├── Monitor devices
          ├── Analyze traffic
          ├── Detect problems
          ├── Change configurations
          └── Manage security
```

This becomes particularly important in large corporate environments where manually managing every device would be impractical.

---

# Software Firewalls

A **software firewall**, also known as a **host-based firewall**, is installed directly on an individual system.

It monitors and controls incoming and outgoing network traffic according to configured security rules.

```text
Network
   │
   ▼
┌──────────────┐
│   Firewall   │
├──────────────┤
│ Allow / Deny │
└──────────────┘
   │
   ▼
Host
```

Unlike a network firewall that can protect multiple systems, a host-based firewall provides protection specifically for the device on which it is installed.

It can:

* Block unauthorized connections
* Allow trusted traffic
* Restrict applications or services
* Drop suspicious packets

An example on Linux is **iptables**, which can define rules controlling network traffic.

---

# Servers

A **server** is a computer or system that provides resources or services to other systems called **clients**.

Common server types include:

| Server              | Service                       |
| ------------------- | ----------------------------- |
| **Web Server**      | Websites and web applications |
| **File Server**     | File storage and sharing      |
| **Mail Server**     | Email services                |
| **Database Server** | Data storage and queries      |

Servers commonly provide:

* Services
* Resource sharing
* Centralized data management
* Authentication
* Access control

This relationship forms the basis of the **Client-Server Model**.

```text
Client                         Server
  │                              │
  │────────── Request ──────────►│
  │                              │
  │◄──────── Response ───────────│
  │                              │
```

For example, when accessing a website:

1. The browser acts as the **client**.
2. It sends a request to a **web server**.
3. The server processes the request.
4. The server sends the requested data back to the client.

---

# Network Component Overview

A simplified network might look like:

```text
                  Internet
                     │
                  Router
                     │
                  Switch
          ┌──────────┼──────────┐
          │          │          │
         PC       Server     Access Point
                                │
                           Smartphone
```

Each component performs a different role:

* **End devices** generate and consume data.
* **NICs** provide network connectivity.
* **Switches** connect devices within a LAN.
* **Routers** connect different networks.
* **Media** carries network signals.
* **Protocols** establish communication rules.
* **Firewalls** control network traffic.
* **Servers** provide services to clients.

---

# Key Takeaways

* **End devices** are the source or destination of network communication.
* A **NIC** provides a device with network connectivity and has an associated MAC address.
* **Routers** operate primarily at Layer 3 and connect different networks using IP addressing and routing information.
* **Switches** operate primarily at Layer 2 and forward frames within a LAN using MAC addresses.
* **Hubs** operate at Layer 1 and repeat traffic to connected ports.
* Ethernet and fiber optics are important wired network media.
* **Protocols** establish standardized rules for network communication.
* **Network management software** helps administrators monitor and maintain networks.
* **Software firewalls** provide host-level network protection.
* **Servers** provide resources and services to clients.
