# Network Communication

For network communication to work correctly, devices need mechanisms to identify:

* The **network interface** receiving the data
* The **device or network destination**
* The **specific application or service** that should process the traffic

Three fundamental elements make this possible:

* **MAC addresses**
* **IP addresses**
* **Ports**

Together, they allow data to move from one application on a device to the correct application on another device.

---

# MAC Addresses

## What is a MAC Address?

A **Media Access Control (MAC) address** is an identifier assigned to a device's Network Interface Card (NIC).

It operates at:

**OSI Layer 2 — Data Link Layer**

MAC addresses are mainly used for communication within a **local network segment**.

A typical MAC address looks like:

```text
00:1A:2B:3C:4D:5E
```

A MAC address is:

* **48 bits long**
* Usually represented in hexadecimal
* Commonly divided into six groups
* Associated with a specific network interface

---

## MAC Address Structure

A MAC address can be divided into two main parts:

```text
00:1A:2B : 3C:4D:5E
────────   ────────
   OUI      Device-specific
```

### OUI

The first 24 bits form the:

**Organizationally Unique Identifier (OUI)**

This portion identifies the manufacturer.

### Device-Specific Portion

The remaining 24 bits identify the individual network interface.

---

## Viewing MAC Addresses on Windows

Windows provides the `getmac` command to display MAC addresses associated with network interfaces.

```powershell
getmac
```

This can be useful when identifying interfaces during network troubleshooting or enumeration.

---

# MAC Addresses in Local Communication

MAC addresses are used to deliver Ethernet frames to the correct device within a local network.

For example:

```text
Computer A
IP: 192.168.1.2
MAC: 00:1A:2B:3C:4D:5E

        │
        ▼

      Switch

        │
        ▼

Computer B
IP: 192.168.1.5
MAC: 00:1A:2B:3C:4D:5F
```

If Computer A wants to communicate with Computer B, it needs to know Computer B's MAC address before sending the local frame.

This is where **ARP** becomes important.

---

# ARP

**Address Resolution Protocol (ARP)** maps an IPv4 address to its corresponding MAC address inside the local network.

Conceptually:

```text
Who has 192.168.1.5?
        │
        ▼
       ARP
        │
        ▼
192.168.1.5 is at
00:1A:2B:3C:4D:5F
```

Once the sending device knows the destination MAC address, it can construct an Ethernet frame containing that address.

The switch then uses the destination MAC address to forward the frame toward the appropriate device.

ARP therefore helps bridge the relationship between:

```text
Layer 3
IP Address
     │
     ▼
    ARP
     │
     ▼
MAC Address
Layer 2
```

---

# IP Addresses

## What is an IP Address?

An **Internet Protocol (IP) address** is a logical address assigned to a device participating in an IP network.

It operates at:

**OSI Layer 3 — Network Layer**

IP addresses allow devices to be identified and reached across different networks.

Unlike MAC addresses, IP addresses are based on network configuration and may change.

---

## IPv4

**IPv4** uses a **32-bit address space**.

It is commonly represented using four decimal numbers separated by dots.

Example:

```text
192.168.1.1
```

---

## IPv6

**IPv6** was developed partly to address the limited IPv4 address space.

IPv6 uses **128-bit addresses**.

Example:

```text
2001:0db8:85a3:0000:0000:8a2e:0370:7334
```

This provides a vastly larger address space than IPv4.

---

# MAC Address vs IP Address

Although both identify aspects of network communication, they serve different purposes.

| Characteristic | MAC Address                    | IP Address                    |
| -------------- | ------------------------------ | ----------------------------- |
| OSI Layer      | Layer 2                        | Layer 3                       |
| Purpose        | Local interface identification | Logical network addressing    |
| Scope          | Local network segment          | Communication across networks |
| Example        | `00:1A:2B:3C:4D:5E`            | `192.168.1.5`                 |
| Used By        | Switches                       | Routers                       |

A useful way to remember the distinction is:

```text
MAC → Which local interface?

IP → Which network/device destination?
```

---

# Ports

An IP address identifies a host, but a host can run many different network services simultaneously.

**Ports** allow the operating system to determine which application should receive specific network traffic.

Ports operate at:

**OSI Layer 4 — Transport Layer**

They are used by transport protocols such as:

* TCP
* UDP

---

## Why Ports Are Needed

Consider a server running multiple services:

```text
             Server
        192.168.1.100
              │
     ┌────────┼────────┐
     │        │        │
   Port 22  Port 80  Port 443
     │        │        │
    SSH      HTTP     HTTPS
```

All services use the same server IP address.

The **port number** distinguishes which application should process incoming traffic.

---

# Port Numbers

Port numbers range from:

```text
0 - 65535
```

They are commonly divided into three categories.

---

## Well-Known Ports

Range:

```text
0 - 1023
```

These ports are associated with commonly recognized protocols and services.

Examples include:

|      Port | Service |
| --------: | ------- |
| **20/21** | FTP     |
|    **80** | HTTP    |
|   **443** | HTTPS   |

These assignments are standardized through the **Internet Assigned Numbers Authority (IANA)**.

---

## Registered Ports

Range:

```text
1024 - 49151
```

Registered ports are commonly associated with specific applications or services.

For example:

|     Port | Service              |
| -------: | -------------------- |
| **1433** | Microsoft SQL Server |

Software vendors may register ports to provide consistent service assignments.

---

## Dynamic / Private Ports

Range:

```text
49152 - 65535
```

These ports are commonly used temporarily by client applications.

They are also known as:

* Dynamic ports
* Private ports
* Ephemeral ports

For example, when a browser connects to an HTTPS server:

```text
Client
192.168.1.10:53021
        │
        │ HTTPS
        ▼
Server
93.184.216.34:443
```

The server uses the known destination port `443`, while the client uses a temporary port selected by the operating system.

Once the communication session ends, the temporary port can be reused.

---

# Viewing Network Connections and Ports

Windows provides the `netstat` command to inspect active connections and listening ports.

```powershell
netstat
```

Depending on the options used, `netstat` can help identify:

* Active network connections
* Listening ports
* Local addresses
* Remote addresses
* Connection states

This becomes particularly useful during network troubleshooting and security investigations.

---

# Browsing the Internet Example

Accessing a website combines several of the concepts introduced so far.

Suppose a user accesses:

```text
example.com
```

A simplified communication process follows.

---

## 1. DNS Lookup

The system first resolves the domain name into an IP address.

For example:

```text
example.com
     │
     ▼
93.184.216.34
```

The IP address identifies the destination server on the network.

---

## 2. Application Request

The web browser generates an HTTP or HTTPS request.

For example:

```text
Browser
   │
   ▼
HTTPS Request
```

If HTTPS is being used, the destination service will normally be:

```text
Port 443
```

---

## 3. Transport Layer

TCP encapsulates the application data and adds port information.

Conceptually:

```text
Source Port:      53021
Destination Port: 443
```

This tells the destination operating system which service should receive the traffic and also identifies the client's temporary communication endpoint.

---

## 4. Network Layer

The destination IP address is added.

```text
Destination IP:
93.184.216.34
```

Routers will use this IP information to forward the packet across networks.

---

## 5. Local Delivery

Before the packet can leave the local network, the computer must determine where to send the local Ethernet frame.

The computer therefore needs the MAC address of its **default gateway**.

ARP can be used to discover it.

```text
Destination IP
93.184.216.34
      │
      │ outside LAN
      ▼
Default Gateway
      │
      ▼
Gateway MAC Address
```

The local Ethernet frame is then addressed to the gateway's MAC address.

---

# Data Transmission

The communication can be simplified as:

```text
Client
│
│ Destination MAC = Gateway
│ Destination IP  = Web Server
│ Destination Port = 443
│
▼
Router
│
▼
Router
│
▼
Router
│
▼
Web Server
```

An important distinction appears here:

**MAC addresses are used for local frame delivery, while IP addresses are used to route traffic toward the final destination across networks.**

As packets move between networks, the Layer 2 information used on each local link can change, while the IP destination remains associated with the remote host.

---

# Server Processing

Once the packet reaches the destination server:

```text
Destination IP
        │
        ▼
     Server
        │
Destination Port
        │
        ▼
     HTTPS
```

The operating system examines the destination port and delivers the data to the application listening on that port.

For HTTPS:

```text
TCP 443
```

The server processes the request and generates a response.

---

# Response Transmission

The server sends the response back toward the client.

The destination now includes the client's temporary port.

Example:

```text
Server
93.184.216.34:443
        │
        ▼
Client
192.168.1.10:53021
```

The client operating system recognizes the temporary port and sends the response to the correct application.

---

# Putting Everything Together

A useful mental model is:

```text
MAC Address
    │
    └── Identifies the network interface on the local link
         Layer 2

IP Address
    │
    └── Identifies the logical network destination
         Layer 3

Port
    │
    └── Identifies the application/service
         Layer 4
```

Therefore, communication can conceptually be viewed as:

```text
Device
  ↓
MAC Address
  ↓
IP Address
  ↓
Port
  ↓
Application
```

Each value answers a different question:

| Element         | Question                                                          |
| --------------- | ----------------------------------------------------------------- |
| **MAC Address** | Which local network interface should receive the frame?           |
| **IP Address**  | Which logical host/network destination should receive the packet? |
| **Port**        | Which service or application should process the data?             |

---

# Cybersecurity Perspective

MAC addresses, IP addresses, and ports are fundamental to both offensive and defensive security.

They appear constantly during:

* Network enumeration
* Packet analysis
* Firewall configuration
* Port scanning
* Service discovery
* Intrusion detection
* Incident investigation
* Network access control

For example, when performing a network scan, discovering:

```text
10.10.10.5:22
10.10.10.5:80
10.10.10.5:443
```

immediately reveals that one host has multiple network services exposed through different ports.

Understanding how addresses and ports interact is therefore essential before learning more advanced enumeration and network security techniques.

---

# Key Takeaways

* **MAC addresses** operate at Layer 2 and identify network interfaces within local communication.
* MAC addresses are 48 bits long and are commonly written in hexadecimal.
* The first portion of a MAC address contains the manufacturer's **OUI**.
* **ARP** maps IPv4 addresses to MAC addresses within a local network.
* **IP addresses** operate at Layer 3 and provide logical addressing across networks.
* IPv4 uses 32-bit addresses.
* IPv6 uses 128-bit addresses.
* **Ports** operate at Layer 4 and identify network applications and services.
* Port numbers range from `0` to `65535`.
* Ports are divided into well-known, registered, and dynamic/private ranges.
* Clients commonly use temporary or ephemeral ports.
* Routers primarily use IP information to forward traffic between networks.
* Switches primarily use MAC addresses for local Layer 2 forwarding.
* A complete network connection depends on multiple addressing mechanisms working together.
