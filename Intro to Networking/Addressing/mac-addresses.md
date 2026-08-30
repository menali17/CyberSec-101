# MAC Addresses

A **Media Access Control (MAC) address** is the physical address used to identify a network interface at **Layer 2 (Data Link)** of the OSI model.

A MAC address has:

- **48 bits**
- **6 bytes (octets)**
- Hexadecimal representation

Example:

```text
DE:AD:BE:EF:13:37
```

Other common representations are:

```text
DE-AD-BE-EF-13-37
DEAD.BEEF.1337
```

## MAC Address Structure

A MAC address can be divided into two main parts:

```text
DE:AD:BE : EF:13:37
└───────┘   └───────┘
   OUI          NIC
```

### OUI - Organizationally Unique Identifier

The first **24 bits (3 bytes)** represent the **OUI**.

```text
DE:AD:BE
```

The OUI is assigned by the IEEE and identifies the manufacturer.

### NIC - Network Interface Controller

The remaining **24 bits (3 bytes)** are assigned by the manufacturer to the network interface.

```text
EF:13:37
```

Together, these parts form the complete MAC address:

```text
DE:AD:BE:EF:13:37
```

---

## IP Address vs MAC Address

IP and MAC addresses operate at different layers and have different purposes.

| Address | OSI Layer | Purpose |
|---|---|---|
| IP Address | Layer 3 - Network | Logical addressing and routing |
| MAC Address | Layer 2 - Data Link | Local network frame delivery |

A simple way to remember:

```text
IP  → Where is the destination?
MAC → Which local interface receives the frame?
```

---

## Local and Remote Communication

When the destination is inside the **same subnet**, the Ethernet frame can be sent directly to the destination host's MAC address.

```text
Host A
192.168.1.10
     |
     | Frame sent to Host B MAC
     v
Host B
192.168.1.20
```

If the destination belongs to a **different subnet**, the frame is sent to the MAC address of the **default gateway**.

For example:

```text
Computer:    192.168.1.10
Destination: 8.8.8.8
Gateway:     192.168.1.1
```

The packet still contains:

```text
Destination IP → 8.8.8.8
```

But the local Ethernet frame uses:

```text
Destination MAC → MAC address of the default gateway
```

Therefore:

```text
Local destination
→ Destination host MAC

Remote destination
→ Default gateway MAC
```

---

# Address Resolution Protocol (ARP)

**Address Resolution Protocol (ARP)** is used in IPv4 networks to resolve an **IP address into a MAC address**.

```text
IPv4 Address
     ↓
    ARP
     ↓
MAC Address
```

For example, suppose a computer wants to communicate with:

```text
192.168.1.20
```

It knows the IP address but needs the corresponding MAC address for Layer 2 communication.

ARP performs this resolution.

---

## ARP Request

The device sends an **ARP Request** asking which device owns a specific IP address.

Conceptually:

```text
Who has 192.168.1.20?
```

The request is broadcast across the local network so that all devices can receive it.

---

## ARP Reply

The device that owns the requested IP responds with an **ARP Reply** containing its MAC address.

For example:

```text
192.168.1.20 is at AA:BB:CC:DD:EE:FF
```

The mapping becomes:

```text
192.168.1.20
       ↓
AA:BB:CC:DD:EE:FF
```

The host can then use that MAC address to communicate with the destination on the local network.

---

## ARP Resolution

The complete process can be summarized as:

```text
Host A knows:
192.168.1.20

But needs:
MAC address

        ↓

ARP Request:
"Who has 192.168.1.20?"

        ↓

ARP Reply:
"192.168.1.20 is at AA:BB:CC:DD:EE:FF"

        ↓

Host A now knows:

192.168.1.20 → AA:BB:CC:DD:EE:FF
```

---

# MAC Broadcast

The MAC broadcast address is:

```text
FF:FF:FF:FF:FF:FF
```

It represents **all devices on the local network segment**.

Broadcast communication is useful when the sender does not yet know the exact destination MAC address.

ARP Requests are an important example of this behavior.

```text
ARP Request
     ↓
FF:FF:FF:FF:FF:FF
     ↓
All devices on the LAN receive it
```

The device with the requested IP can then respond.

---

# MAC Unicast, Multicast and Broadcast

### Unicast

Communication intended for **one specific device**.

```text
Sender → One Receiver
```

### Multicast

Communication intended for a **specific group of devices**.

```text
Sender → Group of Receivers
```

### Broadcast

Communication intended for **all devices in the local broadcast domain**.

```text
Sender → Everyone
```

The MAC broadcast address is:

```text
FF:FF:FF:FF:FF:FF
```

---

# MAC Address Attack Vectors

MAC addresses can be manipulated or spoofed, so they should not be used as the only mechanism for authentication or security.

Important attack vectors include:

### MAC Spoofing

An attacker changes their MAC address to impersonate another device.

```text
Attacker MAC
     ↓
Changed to
     ↓
Legitimate Device MAC
```

This may be used to bypass weak controls based only on MAC addresses.

### MAC Flooding

An attacker sends traffic using many different MAC addresses.

The goal is to overwhelm the MAC address table of a network switch.

### MAC Filtering Bypass

Some networks allow only specific MAC addresses.

If an attacker discovers an authorized MAC address, they may attempt to spoof it and bypass the filtering mechanism.

---

# ARP Spoofing

**ARP Spoofing**, also known as **ARP Cache Poisoning**, is an attack where falsified ARP messages are sent on a LAN.

Consider:

```text
Victim
192.168.1.10

Gateway
192.168.1.1

Attacker
192.168.1.50
```

Normally, the victim should have a mapping similar to:

```text
192.168.1.1 → MAC_OF_GATEWAY
```

The attacker attempts to convince the victim that:

```text
192.168.1.1 → MAC_OF_ATTACKER
```

The victim may then send traffic intended for the gateway to the attacker instead.

```text
Victim
   |
   v
Attacker
   |
   v
Gateway
```

This can place the attacker between the victim and the gateway.

Such a position can be used to perform a **Man-in-the-Middle (MITM)** attack.

---

# Quick Reference

```text
MAC Address
→ Layer 2
→ 48 bits
→ 6 bytes
→ Hexadecimal

OUI
→ First 24 bits
→ Manufacturer

NIC portion
→ Last 24 bits
→ Interface-specific portion

IP Address
→ Layer 3
→ Logical addressing

ARP
→ Resolves IPv4 addresses to MAC addresses

ARP Request
→ Asks who owns an IP address
→ Broadcast

ARP Reply
→ Returns the corresponding MAC address

MAC Broadcast
→ FF:FF:FF:FF:FF:FF

Same Subnet
→ Send frame toward destination MAC

Different Subnet
→ Send frame toward default gateway MAC

MAC Spoofing
→ Attacker changes their MAC address

ARP Spoofing
→ Attacker creates false IP-to-MAC associations
→ Can enable MITM attacks
```

## Key Concept

The most important relationship to remember is:

```text
IP tells us WHERE the destination is.

MAC tells us WHERE to deliver the frame locally.

ARP connects the two:

IPv4 → ARP → MAC
```