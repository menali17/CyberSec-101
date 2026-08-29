# Network Types

Networks can be classified according to their size, purpose, connectivity, and geographical scope.

For cybersecurity, the most common terms are:

- **WAN** — Wide Area Network
- **LAN** — Local Area Network
- **WLAN** — Wireless Local Area Network
- **VPN** — Virtual Private Network

---

# Common Network Types

| Type | Meaning | Common Example |
|---|---|---|
| `WAN` | Wide Area Network | Internet |
| `LAN` | Local Area Network | Home or office network |
| `WLAN` | Wireless Local Area Network | Wi-Fi network |
| `VPN` | Virtual Private Network | Logical connection to another network |

---

# WAN

A **Wide Area Network (WAN)** connects networks across large geographical areas.

The Internet is the most common example of a WAN.

However, a WAN does not necessarily mean the Internet. Large organizations can maintain their own internal WANs connecting several geographically separated networks.

Conceptually:

```text
LAN A ─────┐
           │
LAN B ─────┼──── WAN
           │
LAN C ─────┘
```

A WAN can therefore be understood as multiple networks connected across larger distances.

---

# LAN

A **Local Area Network (LAN)** connects devices within a relatively limited area.

Examples include:

- Home networks
- Office networks
- Internal company networks

LANs commonly use private IPv4 addresses defined by RFC 1918.

## Private IPv4 Ranges

```text
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16
```

These addresses are commonly used internally rather than being directly routed across the Internet.

Example:

```text
Home LAN

192.168.1.0/24
      │
      ├── 192.168.1.10  Laptop
      ├── 192.168.1.20  Smartphone
      └── 192.168.1.30  Desktop
```

---

# WLAN

A **Wireless Local Area Network (WLAN)** is essentially a LAN that provides wireless connectivity.

The main distinction is the transmission medium:

```text
LAN
 │
 └── Wired communication

WLAN
 │
 └── Wireless communication
```

A typical Wi-Fi network is therefore a WLAN.

From a security perspective, this distinction is important because wireless communication introduces additional security considerations.

---

# VPN

A **Virtual Private Network (VPN)** allows a device or network to communicate as though it were connected to another network.

Conceptually:

```text
Your Computer
     │
     │ Internet
     ▼
 VPN Tunnel
     │
     ▼
Remote Network
```

The three VPN types discussed in this section are:

1. Site-to-Site VPN
2. Remote Access VPN
3. SSL VPN

---

# Site-to-Site VPN

A **Site-to-Site VPN** connects entire networks together.

The VPN endpoints are typically networking devices such as:

- Routers
- Firewalls

Example:

```text
Office A LAN
     │
   Router
     │
     │
===== VPN Tunnel =====
     │
   Router
     │
Office B LAN
```

Devices in both locations can communicate across the VPN as though the networks were connected together.

This is commonly used by organizations with multiple offices.

---

# Remote Access VPN

A **Remote Access VPN** connects an individual computer to a remote network.

The client creates a virtual network interface.

Example:

```text
Laptop
  │
  │ Virtual Interface
  ▼
VPN Tunnel
  │
  ▼
Company / Lab Network
```

Hack The Box uses this model with **OpenVPN**.

When connected to HTB labs, a virtual interface such as:

```text
tun0
```

is created.

This is the same `tun0` interface encountered during the Network Foundations Skills Assessment.

```text
Pwnbox
   │
  tun0
   │
   ▼
HTB VPN
   │
   ▼
Lab Target
```

---

# Split-Tunnel VPN

A VPN does not necessarily send **all** network traffic through the tunnel.

A **Split-Tunnel VPN** only routes specific networks through the VPN.

For example:

```text
                    ┌── HTB Network → VPN
Computer ───────────┤
                    └── Internet → Normal Connection
```

If the VPN creates a route only for:

```text
10.10.10.0/24
```

then traffic destined for that network goes through the VPN.

Normal Internet traffic continues through the user's regular Internet connection.

---

## Why HTB Uses Split Tunneling

For Hack The Box, split tunneling allows:

```text
HTB Traffic
     │
     ▼
tun0 → VPN

Normal Internet Traffic
     │
     ▼
Regular Internet Connection
```

This allows access to lab machines without routing unrelated Internet traffic through the HTB VPN.

---

## Security Consideration

In corporate environments, split tunneling can introduce security concerns.

For example, an infected endpoint could communicate with:

```text
Corporate Network
       │
       └── VPN

Internet / Malware Infrastructure
       │
       └── Regular Internet Connection
```

Traffic that bypasses the corporate network may also bypass network-based security monitoring deployed there.

---

# SSL VPN

An **SSL VPN** provides remote access through a web browser.

Instead of requiring the user to interact directly with a traditional VPN client, applications or entire desktop environments can be delivered through the browser.

The HTB **Pwnbox** is given as an example.

Conceptually:

```text
Web Browser
     │
     ▼
SSL VPN / Web Access
     │
     ▼
Remote Environment
```

---

# VPN Types Comparison

| VPN Type | Connects |
|---|---|
| **Site-to-Site** | Network ↔ Network |
| **Remote Access** | Device ↔ Network |
| **SSL VPN** | Browser ↔ Remote resources/environment |

A simple way to remember them:

```text
Site-to-Site
Office ───────── Office

Remote Access
Laptop ───────── Network

SSL VPN
Browser ──────── Remote Environment
```

---

# Book Terms

Some additional network classifications are useful to recognize, although they are less commonly used in everyday networking discussions.

---

# GAN

**GAN — Global Area Network**

A GAN spans extremely large geographical areas, potentially worldwide.

Example:

```text
Global Network
      │
      └── Internet
```

International organizations may also maintain networks spanning multiple WANs.

---

# MAN

**MAN — Metropolitan Area Network**

A MAN connects multiple LANs within a geographical region.

Conceptually:

```text
LAN A ───┐
         │
LAN B ───┼── MAN
         │
LAN C ───┘
```

It may connect locations such as different company branches within the same metropolitan area.

---

# PAN

**PAN — Personal Area Network**

A PAN connects devices over a very small geographical area around an individual.

For example:

```text
Laptop ─── Smartphone
   │
Headphones
```

---

# WPAN

**WPAN — Wireless Personal Area Network**

A WPAN is the wireless version of a PAN.

Technologies can include:

- Bluetooth
- Wireless USB
- ZigBee
- Z-Wave

Example:

```text
Smartphone
    │
 Bluetooth
    │
 Headphones
```

A Bluetooth WPAN can also be called a **Piconet**.

PANs and WPANs generally cover only a few meters.

---

# Network Types Overview

```text
Smallest / Personal
        │
        ▼
   PAN / WPAN
        │
        ▼
    LAN / WLAN
        │
        ▼
       MAN
        │
        ▼
       WAN
        │
        ▼
       GAN
        │
        ▼
Largest / Global
```

---

# Cybersecurity Perspective

For cybersecurity, the most important concepts from this section are:

```text
LAN / WLAN
    │
    └── Internal network

WAN
    │
    └── Large interconnected networks

VPN
    │
    └── Access to remote networks

tun0
    │
    └── Virtual VPN interface

Split Tunnel
    │
    └── Only selected traffic uses VPN
```

Understanding VPN routing is especially important when working with security labs.

When connected to HTB:

```text
Target IP
    │
    ▼
Routing Table
    │
    ▼
tun0
    │
    ▼
HTB Lab Network
```

This explains why commands such as:

```bash
ip route get <TARGET_IP>
```

are useful for determining whether traffic toward a target is actually using the VPN.

---

# Key Takeaways

- **LAN** represents a local network.
- **WLAN** is a LAN using wireless communication.
- **WAN** connects networks across larger geographical areas.
- The Internet is the most common example of a WAN.
- LANs commonly use RFC 1918 private IPv4 addresses.
- **VPNs** logically connect devices or networks to remote networks.
- **Site-to-Site VPNs** connect entire networks.
- **Remote Access VPNs** connect individual devices to remote networks.
- HTB uses a remote-access VPN and creates a `tun0` interface.
- **Split tunneling** sends only selected traffic through the VPN.
- **SSL VPNs** provide remote access through web browsers.
- `GAN`, `MAN`, `PAN`, and `WPAN` are useful additional network classifications.