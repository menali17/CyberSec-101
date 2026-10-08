# SNMP (Simple Network Management Protocol)

---

## Overview

SNMP is an application-layer protocol used to monitor and manage network devices remotely.

It is commonly used with:

- Routers
- Switches
- Servers
- IoT devices
- Network infrastructure

SNMP allows us to retrieve information about devices and, depending on permissions, modify certain settings.

### Default Ports

| Port | Description |
|---|---|
| UDP 161 | SNMP queries and management operations |
| UDP 162 | SNMP traps and notifications |

**SNMP Traps:** Unsolicited notifications sent by devices when specific events occur.

---

## MIB (Management Information Base)

A MIB defines the structure of management objects available through SNMP.

It describes:

- Object names and identifiers.
- Data types.
- Access permissions.
- Object descriptions.

MIBs describe objects but do not store their current values.

## OID (Object Identifier)

An OID uniquely identifies an object within a hierarchical namespace.

OIDs are represented by sequences of numbers separated by dots.

Example:

`1.3.6.1.2.1.1.5.0`

This OID represents `sysName.0`, commonly used to retrieve a device's hostname.

### MIB vs. OID

- **MIB:** Defines and describes management objects.
- **OID:** Identifies a specific object.
- **Value:** The information returned when we query that object.

---

## SNMP Versions

| Version | Security |
|---|---|
| SNMPv1 | Community-based access control, no encryption |
| SNMPv2c | Community-based access control, no encryption |
| SNMPv3 | Supports stronger authentication and encryption |

SNMPv3 provides improved security, although the enabled protections depend on its configuration.

---

## Community Strings

Community strings act as shared access tokens in SNMPv1 and SNMPv2c.

Common examples include:

- `public`
- `private`

Community strings can have different permissions:

- **Read-only (RO):** Allows retrieving information.
- **Read-write (RW):** May allow modifying configuration values.

SNMPv1 and SNMPv2c transmit community strings without encryption.

Weak or exposed community strings may reveal sensitive system information.

---

## Default Configuration

SNMP daemon configuration:

```bash
/etc/snmp/snmpd.conf
```

Important settings:

| Setting | Description |
|---|---|
| `sysLocation` | Device location |
| `sysContact` | Administrative contact |
| `agentaddress` | Addresses on which the SNMP agent listens |
| `rocommunity` | Read-only community access |
| `rwcommunity` | Read-write community access |
| `rwuser` | Read-write SNMPv3 user access |

Overly permissive configurations may expose information or allow unauthorized modifications.

---

## SNMP Enumeration

### 1. Query SNMP Objects — snmpwalk

```bash
snmpwalk -v2c -c public <IP>
```

- `-v2c`: Uses SNMP version 2c.
- `-c public`: Specifies the community string.
- `<IP>`: Target device.

`snmpwalk` retrieves accessible OIDs and their corresponding values.

### Useful OIDs

| OID | Description |
|---|---|
| `1.3.6.1.2.1.1.1.0` | System description |
| `1.3.6.1.2.1.1.3.0` | System uptime |
| `1.3.6.1.2.1.1.4.0` | Administrative contact |
| `1.3.6.1.2.1.1.5.0` | Hostname |
| `1.3.6.1.2.1.1.6.0` | System location |

### 2. Discover Community Strings — onesixtyone

```bash
onesixtyone -c /opt/useful/seclists/Discovery/SNMP/snmp.txt <IP>
```

- `-c`: Specifies a community string wordlist.

`onesixtyone` tests candidate community strings against a target SNMP service.

### 3. Enumerate OIDs — braa

```bash
braa public@<IP>:.1.3.6.*
```

- `public`: Community string.
- `<IP>`: Target address.
- `.1.3.6.*`: OID pattern.

`braa` allows us to query multiple OIDs efficiently.

---

## Security Risks

Common SNMP security concerns include:

- Default or weak community strings.
- Unencrypted SNMPv1/v2c communication.
- Excessive read-write permissions.
- Exposure of system and network information.
- Insecure access control configurations.

---

## Key Takeaways

- SNMP is used for remote monitoring and management.
- UDP 161 handles SNMP queries; UDP 162 handles traps.
- MIBs describe management objects, while OIDs identify them.
- SNMPv1/v2c rely on community strings without encryption.
- SNMPv3 supports stronger authentication and encryption.
- `onesixtyone` can discover community strings.
- `snmpwalk` retrieves values from accessible SNMP objects.
- `braa` helps enumerate OIDs using patterns.

**Our objective is to discover accessible SNMP services, identify valid access configurations, and retrieve useful device information during footprinting.**