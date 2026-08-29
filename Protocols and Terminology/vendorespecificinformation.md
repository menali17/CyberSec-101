# Vendor Specific Information

This section introduces vendor-specific networking concepts, mainly focused on **Cisco IOS, VLANs, VLAN tagging, VLAN attacks, VXLAN, CDP, and STP**.

The most important concepts to understand are:

- Cisco IOS
- VLAN
- Access Port
- Trunk Port
- IEEE 802.1Q
- VLAN Tagging
- VLAN Hopping
- Double Tagging
- VXLAN
- CDP
- STP

---

## Cisco IOS

`Cisco IOS` is the operating system used by many Cisco routers and switches.

It provides features such as:

- IPv6 support
- Routing
- Switching
- DHCP
- Access Control Lists
- Encryption and authentication
- Quality of Service

Cisco IOS devices are commonly managed using a **Command Line Interface (CLI)**.

Cisco devices may also allow remote administration using protocols such as:

```text
SSH
Telnet
```

A Cisco IOS Telnet service may display a message such as:

```text
User Access Verification

Password:
```

---

## VLAN

`VLAN` stands for:

```text
Virtual Local Area Network
```

A VLAN logically divides a physical network into separate network segments.

A useful way to think about VLANs is:

```text
One Physical Switch
        |
        +-- VLAN 10
        |
        +-- VLAN 20
        |
        +-- VLAN 30
```

Each VLAN acts as its own **broadcast domain**.

A broadcast sent inside one VLAN does not normally reach devices belonging to another VLAN.

---

## VLANs and Subnets

Each VLAN normally has its own subnet.

Example:

```text
VLAN 10 → 192.168.1.0/24
VLAN 20 → 192.168.2.0/24
VLAN 30 → 192.168.3.0/24
```

Therefore:

```text
VLAN
  |
  v
Logical Network
  |
  v
Broadcast Domain
  |
  v
Usually its own Subnet
```

---

## Why VLANs Are Used

VLANs provide several benefits:

- Better organization
- Increased security
- Easier administration
- Reduced broadcast traffic
- Better network performance

From a security perspective, VLANs are especially useful for **network segmentation**.

For example:

```text
Finance VLAN
      X
      |
      X
Guest VLAN
```

Devices in different VLANs cannot normally communicate directly without routing.

---

## VLAN IDs

Cisco switches support VLAN IDs from:

```text
1 - 4094
```

The values:

```text
0
4095
```

are reserved.

`VLAN 1` is the default VLAN on Cisco switches.

---

## VLAN Membership

Switch ports can be assigned to VLANs either:

- Statically
- Dynamically

### Static VLAN

A network administrator manually assigns a switch port to a VLAN.

Example:

```text
Port 1 → VLAN 10
Port 2 → VLAN 20
Port 3 → VLAN 30
```

Static VLAN assignment is the most common approach.

### Dynamic VLAN

A VLAN may be selected based on information such as:

```text
MAC Address
Protocol
```

A potential security concern is that MAC-based VLAN assignment can be abused through MAC spoofing.

---

## Access Ports

An **Access Port** normally carries traffic belonging to only one VLAN.

Example:

```text
Computer
   |
   v
Access Port
   |
   v
VLAN 10
```

Traffic arriving on the access port is treated as belonging to the VLAN assigned to that port.

Remember:

```text
Access Port
→ One VLAN
→ Commonly connected to endpoints
```

---

## Trunk Ports

A **Trunk Port** can carry traffic from multiple VLANs.

Example:

```text
Switch A
   |
   | VLAN 10
   | VLAN 20
   | VLAN 30
   |
   v
Trunk Link
   |
   v
Switch B
```

Trunks are commonly used between:

```text
Switch ↔ Switch
Switch ↔ Router
```

The key distinction is:

```text
Access Port → One VLAN

Trunk Port → Multiple VLANs
```

---

## VLAN Tagging

Standard Ethernet frames do not contain VLAN information.

To identify which VLAN a frame belongs to while crossing trunk links, VLAN information can be added to the Ethernet frame.

This process is called:

```text
VLAN Tagging
```

The modern standard used for VLAN tagging is:

```text
IEEE 802.1Q
```

---

## IEEE 802.1Q

`802.1Q` adds VLAN information to an Ethernet frame.

Conceptually:

```text
Ethernet Frame
      +
802.1Q VLAN Tag
```

One important field is:

```text
VID
```

`VID` stands for:

```text
VLAN Identifier
```

The VID contains 12 bits.

Therefore:

```text
2^12 = 4096 possible values
```

Since two values are reserved:

```text
4096 - 2 = 4094 usable VLAN IDs
```

---

## ISL

`ISL` stands for:

```text
Inter-Switch Link
```

ISL is an older Cisco proprietary trunking protocol.

Modern networks generally use:

```text
802.1Q
```

instead.

Simplified:

```text
ISL
→ Cisco proprietary
→ Older / deprecated

802.1Q
→ Standard
→ Widely used
```

---

## VLAN Interfaces in Linux

Linux can create VLAN interfaces on top of physical interfaces.

Example:

```text
eth0
 |
 +-- eth0.20
```

Here:

```text
eth0
→ Physical interface

eth0.20
→ VLAN 20 interface
```

A VLAN interface can be created using:

```bash
sudo ip link add link eth0 name eth0.20 type vlan id 20
```

An IP address can then be assigned:

```bash
sudo ip addr add 192.168.1.1/24 dev eth0.20
```

And the interface can be enabled:

```bash
sudo ip link set up eth0.20
```

---

## Analyzing VLAN Traffic

Wireshark can identify VLAN-tagged traffic using:

```text
vlan
```

To filter traffic from a specific VLAN:

```text
vlan.id == 10
```

This allows an analyst to inspect traffic belonging to a particular VLAN.

---

## VLAN Security

Although VLANs improve segmentation, they can still be affected by attacks.

Important examples include:

- VLAN Hopping
- Double-Tagging VLAN Hopping

---

## VLAN Hopping

A **VLAN Hopping** attack attempts to access traffic from VLANs that the attacker should not normally be able to reach.

One technique can abuse:

```text
DTP
```

`DTP` stands for:

```text
Dynamic Trunking Protocol
```

DTP can automatically negotiate trunk links between Cisco devices.

Conceptually, an attacker may try to make their machine behave like a switch:

```text
Attacker
   |
   | Pretends to be a switch
   v
Switch
   |
   | Trunk negotiation
   v
Multiple VLANs
```

If successful, the attacker may gain access to traffic from multiple VLANs.

---

## Double-Tagging VLAN Hopping

A **Double-Tagging** attack places two `802.1Q` VLAN tags inside one Ethernet frame.

Conceptually:

```text
Ethernet Frame
     |
     +-- Outer VLAN Tag
     |
     +-- Inner VLAN Tag
```

A simplified flow is:

```text
Attacker
   |
   | Outer Tag: VLAN 10
   | Inner Tag: VLAN 30
   v
Switch 1
   |
   | Removes outer tag
   v
Switch 2
   |
   | Processes inner tag
   v
VLAN 30
```

Under specific network configurations, this may allow traffic to reach another VLAN.

---

## VXLAN

Traditional VLANs support approximately:

```text
4094 VLANs
```

This can be limiting in large data centers and cloud environments.

`VXLAN` stands for:

```text
Virtual eXtensible Local Area Network
```

VXLAN allows Layer 2 networks to be extended across Layer 3 infrastructure.

Conceptually:

```text
Layer 2 Network
      |
      v
VXLAN Overlay
      |
      v
Layer 3 Infrastructure
```

VXLAN uses a:

```text
24-bit VNI
```

`VNI` stands for:

```text
VXLAN Network Identifier
```

A 24-bit VNI allows approximately:

```text
2^24
≈ 16 million VXLAN segments
```

Comparison:

```text
VLAN
→ About 4094 segments

VXLAN
→ About 16 million segments
```

VXLAN is commonly associated with:

- Data centers
- Cloud environments
- Virtualized networks
- Multi-tenant infrastructure

---

## Cisco Discovery Protocol

`CDP` stands for:

```text
Cisco Discovery Protocol
```

CDP is a Cisco **Layer 2 protocol** used by directly connected Cisco devices to exchange information.

CDP can reveal information such as:

- Device name
- IP address
- Port
- Device type
- Cisco IOS version
- Hardware platform

Example:

```text
Device: router.example.local
IP:     10.129.100.1
Port:   Ethernet0/0
Type:   Router
OS:     Cisco IOS
```

From a cybersecurity perspective, this information can be valuable during network enumeration.

Remember:

```text
CDP
→ Layer 2
→ Cisco Device Discovery
→ Can expose network information
```

---

## Spanning Tree Protocol

`STP` stands for:

```text
Spanning Tree Protocol
```

STP prevents loops in Layer 2 switched networks.

Consider:

```text
Switch A
   | \
   |  \
   |   \
Switch B --- Switch C
```

Multiple paths can create Layer 2 loops.

Without protection:

```text
Frame
  |
  v
Switch A
  |
  v
Switch B
  |
  v
Switch C
  |
  v
Switch A
  |
  v
...
```

The frame could continue circulating through the network.

STP creates a loop-free logical topology by blocking selected redundant paths.

```text
Physical Redundancy
       |
       v
      STP
       |
       v
Loop-Free Logical Topology
```

Remember:

```text
STP
→ Layer 2
→ Prevents switching loops
```

---

## Cybersecurity Perspective

The most important security-related concepts are:

```text
VLAN
→ Network segmentation

Access Port
→ One VLAN

Trunk Port
→ Multiple VLANs

802.1Q
→ VLAN tagging

VLAN Hopping
→ Attempts to cross VLAN boundaries

Double Tagging
→ Uses multiple VLAN tags

CDP
→ Can expose Cisco device information

STP
→ Prevents Layer 2 loops
```

---

## Quick Reference

```text
Cisco IOS
→ Cisco router/switch operating system

VLAN
→ Logical network segmentation

Access Port
→ Carries one VLAN

Trunk Port
→ Carries multiple VLANs

802.1Q
→ VLAN tagging standard

VID
→ VLAN Identifier

DTP
→ Dynamic Trunking Protocol

VLAN Hopping
→ Attempts to access other VLANs

Double Tagging
→ Two VLAN tags inside one frame

VXLAN
→ Layer 2 overlay over Layer 3
→ Uses VNI
→ Supports millions of segments

CDP
→ Cisco device discovery
→ Layer 2

STP
→ Prevents Layer 2 loops
```

---

## What to Focus On

At this stage, make sure you understand:

```text
[ ] What a VLAN is
[ ] Why VLANs improve network segmentation
[ ] Why each VLAN usually has its own subnet
[ ] Difference between Access and Trunk ports
[ ] What 802.1Q tagging does
[ ] Basic idea of VLAN Hopping
[ ] Basic idea of Double Tagging
[ ] Why VXLAN exists
[ ] What information CDP can expose
[ ] Why STP is necessary
```

The most important mental model is:

```text
Physical Switch
      |
      v
     VLANs
      |
      v
Logical Segmentation
      |
      v
Different Broadcast Domains
```

And:

```text
Endpoint
   |
   v
Access Port
   |
   v
Single VLAN
   |
   v
Switch
   |
   v
Trunk Port
   |
   v
Multiple VLANs
   |
   v
Another Switch
```