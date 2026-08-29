# Network Security

**Network security** refers to the measures used to protect networked data, devices, applications, and systems from unauthorized access, misuse, modification, or disruption.

A central objective of network security is preserving the **CIA Triad**:

* **Confidentiality**
* **Integrity**
* **Availability**

---

# CIA Triad

## Confidentiality

**Confidentiality** ensures that information can only be accessed by authorized users or systems.

Examples of controls that support confidentiality include:

* Access control
* Encryption
* Authentication
* Network segmentation

The goal is to prevent unauthorized disclosure of information.

---

## Integrity

**Integrity** ensures that information remains accurate and is not modified without authorization.

Security controls should prevent or detect:

* Unauthorized modification
* Data corruption
* Tampering

---

## Availability

**Availability** ensures that systems and network resources remain accessible when they are needed.

Threats to availability can include:

* Hardware failure
* Network outages
* Denial-of-Service attacks
* Misconfiguration

---

# Firewalls

A **firewall** is a hardware or software security mechanism that monitors incoming and outgoing network traffic.

It uses predefined rules to determine whether traffic should be:

```text
ALLOW
```

or:

```text
BLOCK
```

These rules can be called:

* Firewall policies
* Access Control Lists (ACLs)

A firewall therefore acts as a control point between different systems or network segments.

---

# Traffic Filtering

Firewalls commonly inspect characteristics such as:

* Source IP address
* Destination IP address
* Source port
* Destination port
* Protocol

Conceptually:

```text
Incoming Packet
      │
      ▼
   Firewall
      │
      ├── Rule Match → Allow
      │
      └── Rule Match → Block
```

Administrators define these policies according to the network's security requirements.

Firewalls can also:

* Log traffic
* Record blocked connections
* Generate alerts
* Enforce access restrictions

---

# Packet Filtering Firewall

A **Packet Filtering Firewall** examines basic information contained in packets.

It commonly operates at:

* **Layer 3 — Network**
* **Layer 4 — Transport**

It can inspect:

```text
Source IP
Destination IP
Source Port
Destination Port
Protocol
```

For example:

```text
ALLOW TCP 80
ALLOW TCP 443
BLOCK everything else
```

This would permit HTTP and HTTPS traffic while rejecting other connections.

---

# Stateful Inspection Firewall

A **Stateful Inspection Firewall** maintains information about active network connections.

Instead of examining packets independently, it understands whether a packet belongs to an established communication session.

For example:

```text
Internal Host
     │
     │ Outbound Request
     ▼
   Firewall
     │
     ▼
Internet
     │
     │ Response
     ▼
   Firewall
     │
     │ Matches established session
     ▼
Internal Host
```

A response belonging to a legitimate existing connection can therefore be allowed automatically.

This makes stateful inspection more context-aware than simple packet filtering.

---

# Application Layer Firewall

An **Application Layer Firewall**, sometimes called a **Proxy Firewall**, can inspect traffic at the application layer.

It can operate up to:

**OSI Layer 7 — Application**

Instead of only checking IP addresses and ports, it may analyze the actual application content.

For example:

```text
HTTP Request
     │
     ▼
Application Firewall
     │
     ├── Legitimate Request → Allow
     │
     └── Suspicious Pattern → Block
```

This allows more detailed control over protocols such as HTTP.

---

# Next-Generation Firewall

A **Next-Generation Firewall (NGFW)** combines traditional firewall capabilities with more advanced security features.

These may include:

* Stateful inspection
* Deep packet inspection
* Intrusion detection
* Intrusion prevention
* Application control
* Threat intelligence

An NGFW therefore provides more contextual inspection than basic firewall technologies.

---

# Firewall Placement

Firewalls are often positioned between trusted and untrusted networks.

A simplified enterprise topology might be:

```text
Internet
   │
   ▼
Firewall
   │
   ▼
Internal Network
```

In home environments, firewall functionality is often integrated into the router.

```text
Internet
   │
   ▼
Router / Firewall
   │
   ├── Laptop
   ├── PC
   └── Smartphone
```

In larger environments, the firewall may be a dedicated appliance positioned at the network perimeter.

---

# Intrusion Detection and Prevention Systems

**Intrusion Detection Systems (IDS)** and **Intrusion Prevention Systems (IPS)** monitor activity for indications of attacks or policy violations.

Although they are related, their behavior differs.

---

# IDS

An **Intrusion Detection System (IDS)** monitors traffic or system activity and generates alerts when suspicious behavior is detected.

The key characteristic is:

```text
Detect + Alert
```

It does **not normally block the traffic itself**.

Conceptually:

```text
Traffic
   │
   ▼
  IDS
   │
   ├── Normal → Observe
   │
   └── Suspicious → Alert
```

Security analysts can then investigate the event.

---

# IPS

An **Intrusion Prevention System (IPS)** also analyzes traffic for suspicious or malicious activity.

However, an IPS can actively prevent the traffic.

Its behavior is:

```text
Detect + Block
```

Conceptually:

```text
Traffic
   │
   ▼
  IPS
   │
   ├── Normal → Allow
   │
   └── Malicious → Block
```

---

# IDS vs IPS

| Technology | Detects | Alerts | Blocks |
| ---------- | ------: | -----: | -----: |
| **IDS**    |     Yes |    Yes |     No |
| **IPS**    |     Yes |    Yes |    Yes |

A simple way to remember the distinction is:

```text
IDS → Detect

IPS → Prevent
```

---

# Detection Techniques

IDS and IPS solutions can use different techniques to identify malicious behavior.

Two important approaches are:

* Signature-based detection
* Anomaly-based detection

---

## Signature-Based Detection

**Signature-based detection** compares network activity with known attack patterns.

Conceptually:

```text
Traffic
   │
   ▼
Known Signature Database
   │
   ├── Match → Suspicious
   └── No Match → Continue
```

This approach is effective for identifying previously known threats.

However, it depends on having an appropriate signature for the attack being detected.

---

## Anomaly-Based Detection

**Anomaly-based detection** looks for behavior that differs from expected or normal activity.

For example:

```text
Normal Traffic Pattern
        │
        ▼
Current Activity
        │
   ┌────┴────┐
 Similar    Unusual
              │
              ▼
            Alert
```

This approach can potentially identify previously unknown attacks, but unusual legitimate activity may also generate alerts.

---

# Network-Based IDS/IPS

A **Network-Based IDS/IPS** monitors traffic passing through a network location.

Common abbreviations include:

* **NIDS — Network Intrusion Detection System**
* **NIPS — Network Intrusion Prevention System**

It can be positioned at strategic locations such as:

* Core switches
* Network gateways
* Datacenter segments

Conceptually:

```text
Network Traffic
      │
      ▼
    NIDS
      │
      ▼
Internal Network
```

A network-based system can inspect traffic involving multiple hosts.

---

# Host-Based IDS/IPS

A **Host-Based IDS/IPS** operates directly on an individual device.

Common abbreviations include:

* **HIDS — Host Intrusion Detection System**
* **HIPS — Host Intrusion Prevention System**

It can monitor:

* Network traffic
* System logs
* Host activity

Conceptually:

```text
┌──────────────────────┐
│        Server        │
│                      │
│  HIDS / HIPS Agent   │
│                      │
└──────────────────────┘
```

This provides visibility into activity occurring on the specific host.

---

# IDS/IPS Placement

IDS and IPS technologies can be placed in different parts of a network.

One common design is:

```text
Internet
   │
   ▼
Firewall
   │
   ▼
IDS / IPS
   │
   ▼
Internal Network
```

In this architecture:

1. The firewall performs initial traffic filtering.
2. The IDS/IPS analyzes traffic that passes through.
3. Suspicious activity can be detected or prevented.

---

# DMZ

A **DMZ (Demilitarized Zone)** is a network segment used for systems that need to be accessible from external networks.

Examples might include publicly accessible servers.

IDS/IPS solutions may monitor traffic entering or leaving this network segment.

Conceptually:

```text
              Internet
                 │
                 ▼
              Firewall
              /       \
             /         \
           DMZ       Internal
            │         Network
            │
       Public Server
```

Security monitoring can be deployed around the DMZ to identify suspicious activity involving exposed services.

---

# Suricata

**Suricata** is an example of software that can operate as both:

* IDS
* IPS

Depending on how it is deployed, it can inspect network traffic and either:

```text
IDS Mode → Detect and Alert
```

or:

```text
IPS Mode → Detect and Prevent
```

---

# Firewall vs IDS vs IPS

These technologies perform related but different functions.

| Technology   | Main Role                                             |
| ------------ | ----------------------------------------------------- |
| **Firewall** | Controls traffic according to predefined access rules |
| **IDS**      | Detects suspicious activity and generates alerts      |
| **IPS**      | Detects suspicious activity and actively blocks it    |

They are often used together as part of a layered security architecture.

For example:

```text
Internet
   │
   ▼
Firewall
   │
   ▼
IPS
   │
   ▼
Internal Network
   │
   ▼
Host Security
```

---

# Network Security Best Practices

## Least Privilege

Firewall rules should follow the principle of:

**Least Privilege**

Only network communication that is necessary should be permitted.

Instead of:

```text
ALLOW EVERYTHING
```

a stronger approach is:

```text
ALLOW only required traffic
BLOCK unnecessary traffic
```

---

## Regular Updates

Security systems should be kept updated.

This includes:

* Operating systems
* Firewall software
* IDS/IPS software
* Detection signatures
* Security tools

Updates help systems recognize and defend against newer threats.

---

## Monitoring and Logging

Security controls should generate logs that can be reviewed for suspicious behavior.

Useful sources include:

* Firewall logs
* IDS alerts
* IPS events
* System logs

Monitoring these events can help detect security incidents earlier.

---

# Defense in Depth

**Defense in Depth** means using multiple security controls instead of relying on a single defense mechanism.

For example:

```text
Internet
   │
   ▼
Firewall
   │
   ▼
IDS / IPS
   │
   ▼
Endpoint Protection
   │
   ▼
Application Security
   │
   ▼
Data
```

If one security layer fails, another layer may still detect or stop the attack.

Possible security layers include:

* Firewalls
* IDS/IPS
* Antivirus
* Endpoint protection
* Authentication
* Access control
* Network segmentation

---

# Penetration Testing

**Penetration testing** can be used to evaluate whether security controls and policies work as expected.

Instead of waiting for a real attacker to discover weaknesses, authorized security testing simulates attacks to identify vulnerabilities.

Penetration tests can help evaluate:

* Firewall rules
* Exposed services
* Network segmentation
* Security configurations
* Detection capabilities

---

# Cybersecurity Perspective

Understanding these technologies is fundamental because firewalls and IDS/IPS systems appear throughout both offensive and defensive cybersecurity.

During offensive assessments, they may influence:

* Which ports are reachable
* Which protocols are allowed
* Whether malicious traffic is detected
* Whether connections are blocked

During defensive operations, they provide:

* Traffic control
* Detection
* Prevention
* Logging
* Security visibility

The relationship can be summarized as:

```text
Firewall
   │
   └── Should this traffic be allowed?

IDS
   │
   └── Does this traffic look suspicious?

IPS
   │
   └── Does this traffic look suspicious,
       and should it be stopped?
```

---

# Key Takeaways

* Network security aims to protect systems, devices, applications, and data.
* The **CIA Triad** consists of Confidentiality, Integrity, and Availability.
* **Firewalls** permit or block traffic based on predefined security rules.
* Packet filtering firewalls commonly inspect Layer 3 and Layer 4 information.
* Stateful firewalls track active network connections.
* Application-layer firewalls can inspect application traffic.
* NGFWs combine traditional firewall capabilities with advanced inspection and security functionality.
* **IDS** detects suspicious behavior and generates alerts.
* **IPS** detects and actively prevents malicious activity.
* Signature-based detection compares activity against known attack patterns.
* Anomaly-based detection identifies deviations from expected behavior.
* **NIDS/NIPS** monitor network traffic.
* **HIDS/HIPS** monitor individual hosts.
* Security technologies can be deployed at multiple strategic points within a network.
* **Least privilege** minimizes unnecessary network access.
* **Defense in depth** combines multiple security layers.
* Continuous monitoring, updates, and penetration testing help maintain effective network defenses.
