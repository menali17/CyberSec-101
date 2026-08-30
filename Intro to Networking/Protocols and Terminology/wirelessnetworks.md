# Wireless Networks

Wireless networks transmit data using **Radio Frequency (RF)** instead of physical cables.

Common examples include:

```text
WiFi → Local wireless networks
3G / 4G / 5G → Mobile wireless networks
```

WiFi commonly operates in the:

```text
2.4 GHz
5 GHz
```

The quality and range of a wireless connection can be affected by:

- Distance
- Obstacles
- Transmitter power
- RF interference/noise

---

# Wireless Access Point (WAP)

A `Wireless Access Point (WAP)` allows wireless devices to connect to a network.

Simplified:

```text
Laptop
   ↓
   WiFi
   ↓
  WAP
   ↓
Wired Network
   ↓
Internet
```

The WAP acts as the central point of communication between wireless clients and the network.

---

# SSID

`SSID` stands for:

```text
Service Set Identifier
```

It is essentially the **name of a WiFi network**.

Example:

```text
SSID: HomeNetwork
Password: ********
```

A device needs information such as the SSID and authentication credentials to connect to the wireless network.

---

# IEEE 802.11

WiFi communication is based on the:

```text
IEEE 802.11
```

standard.

When a device attempts to connect to a wireless network, it communicates with the WAP using 802.11 frames.

An association request can contain information such as:

```text
MAC Address
SSID
Supported Data Rates
Supported Channels
Supported Security Protocols
```

Example:

```text
Client
   |
   | Association Request
   v
WAP
```

---

# Hidden SSID

A WAP can be configured to stop broadcasting its SSID.

However:

```text
Hidden SSID ≠ Invisible Network
```

The SSID may still be discovered through wireless network traffic, including authentication-related packets.

Therefore, hiding the SSID should not be considered strong security by itself.

---

# WiFi Security

Three important security mechanisms are:

```text
Encryption
Access Control
Firewall
```

Encryption protects transmitted data.

Access control determines which devices or users can connect.

A firewall controls incoming and outgoing network traffic according to security rules.

---

# Wireless Encryption Protocols

Important WiFi security protocols include:

```text
WEP
WPA
WPA2
WPA3
```

A simplified evolution is:

```text
WEP
 ↓
WPA
 ↓
WPA2
 ↓
WPA3

Older/Weaker → Newer/Stronger
```

---

# WEP

`WEP` stands for:

```text
Wired Equivalent Privacy
```

WEP is an old wireless security protocol.

It uses:

```text
RC4
```

for encryption.

WEP uses an `Initialization Vector (IV)` combined with a secret key.

Simplified:

```text
IV + Secret Key
      ↓
Encryption
      ↓
Encrypted Wireless Traffic
```

One of WEP's major problems is its small IV space and weaknesses in its design.

Because of these weaknesses:

```text
WEP = Insecure / Deprecated
```

WEP should not be used to secure modern wireless networks.

---

# WPA

`WPA` stands for:

```text
WiFi Protected Access
```

WPA was introduced as a more secure replacement for WEP.

Modern wireless networks should generally use:

```text
WPA2
or
WPA3
```

rather than WEP.

---

# WPA-Personal vs WPA-Enterprise

There are two important deployment models:

## WPA-Personal

Common in:

```text
Homes
Small networks
```

Usually uses a:

```text
Pre-Shared Key (PSK)
```

Example:

```text
WiFi Password
     ↓
Shared by authorized users
```

---

## WPA-Enterprise

Common in:

```text
Companies
Universities
Large organizations
```

Instead of relying only on one shared WiFi password, authentication can be handled by a centralized authentication infrastructure.

Conceptually:

```text
Client
   ↓
WAP
   ↓
Authentication Server
```

This provides more centralized control over user authentication.

---

# EAP

`EAP` stands for:

```text
Extensible Authentication Protocol
```

EAP is an authentication framework that supports different authentication methods.

Two protocols mentioned in this section are:

```text
LEAP
PEAP
```

---

# LEAP

LEAP uses shared-key based authentication.

A compromised shared key can weaken the security of the authentication process.

---

# PEAP

PEAP provides stronger protection by creating an encrypted TLS tunnel for authentication.

Simplified:

```text
Client
   ↓
Encrypted TLS Tunnel
   ↓
Authentication
   ↓
Network Access
```

---

# EAP-TLS

`EAP-TLS` uses digital certificates and PKI for authentication.

Conceptually:

```text
Client Certificate
       +
Server Certificate
       ↓
Authentication
       ↓
Secure Network Access
```

It provides strong authentication for enterprise wireless networks.

---

# TACACS+

`TACACS+` is used for centralized:

```text
Authentication
Authorization
```

of users accessing network devices such as:

```text
Routers
Switches
Wireless infrastructure
```

Conceptually:

```text
User
  ↓
Network Device
  ↓
TACACS+ Server
  ↓
Authentication / Authorization
```

---

# Disassociation Attack

A disassociation attack attempts to disrupt communication between wireless clients and a WAP.

Normally:

```text
Client ←────────→ WAP
```

During the attack:

```text
Client ←── Disassociation Frame
                ↑
             Attacker

Client ✕ WAP
```

The client disconnects and must reconnect to the wireless network.

This disruption may also be used as a precursor to other attacks.

---

# Wireless Hardening

Important wireless security measures include:

```text
WPA2 / WPA3
EAP-TLS
Strong Authentication
Network Access Control
Firewall
```

Other mechanisms mentioned include:

```text
Hidden SSID
MAC Filtering
```

However, these should not be treated as the primary security mechanisms.

For example:

```text
MAC Filtering
```

only allows configured MAC addresses to connect.

But MAC addresses can potentially be spoofed.

Therefore:

```text
MAC Filtering ≠ Strong Authentication
```

---

# Cybersecurity Perspective

Wireless networks are especially interesting from a security perspective because communication travels through the air.

Instead of requiring physical access to a cable:

```text
Wireless Client
       ↓
    RF Signal
       ↓
      WAP
```

an attacker within wireless range may potentially observe wireless traffic.

Therefore, encryption and authentication are essential.

The basic security model is:

```text
Wireless Traffic
       ↓
Encryption
       ↓
Authentication
       ↓
Access Control
       ↓
Protected Network
```

---

# Quick Reference

```text
WiFi
→ Wireless networking using RF

2.4 GHz / 5 GHz
→ Common WiFi frequency bands

WAP
→ Wireless Access Point

SSID
→ WiFi network name

802.11
→ WiFi networking standard
```

Security:

```text
WEP
→ Old
→ RC4
→ Vulnerable
→ Do not use

WPA2
→ Modern security

WPA3
→ Newer security
```

Authentication:

```text
EAP
→ Authentication framework

LEAP
→ Shared-key based

PEAP
→ TLS tunnel

EAP-TLS
→ Certificates + PKI

TACACS+
→ Centralized authentication/authorization
```

The most important mental model is:

```text
SSID
→ identifies the wireless network

WAP
→ provides wireless access

802.11
→ defines WiFi communication

WPA2/WPA3
→ protects wireless communication

Authentication
→ determines who can connect

Encryption
→ protects transmitted data
```