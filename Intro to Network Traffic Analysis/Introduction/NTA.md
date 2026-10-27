
# Network Traffic Analysis

## What is Network Traffic Analysis (NTA)?

**Network Traffic Analysis (NTA)** is the process of examining network traffic to understand communication patterns, identify common ports and protocols, establish a baseline, detect anomalies, and investigate potential security threats.

NTA provides visibility into network activity, allowing security analysts to identify suspicious behavior and respond to incidents more effectively.

### Main Objectives

- **Traffic Monitoring:** Collect and inspect network traffic in real time to identify potential threats.
- **Baseline Establishment:** Understand normal network communication patterns to recognize unusual behavior.
- **Anomaly Detection:** Identify suspicious hosts, uncommon ports, unexpected connections, and abnormal network activity.
- **Troubleshooting:** Investigate networking issues, such as HTTP errors, TCP connection problems, and protocol misconfigurations.
- **Malware Detection:** Identify malicious network activity associated with ransomware, exploitation attempts, and other threats.
- **Incident Investigation:** Analyze historical traffic to determine what happened during a security incident.
- **Threat Hunting:** Proactively search for indicators of malicious activity that may have bypassed existing security controls.

### Establishing a Baseline

A **baseline** represents the expected or normal behavior of a network.

By understanding which devices communicate, which protocols are commonly used, and what normal traffic volumes look like, analysts can identify deviations that may indicate security incidents.

For example:

- Employees accessing websites over HTTPS is generally expected.
- Workstations communicating with internal DNS servers is normal.
- A workstation attempting to connect to hundreds of different ports on another host may indicate port scanning.
- An internal server communicating with an unexpected external host may require investigation.

**Important:** Unusual traffic is not necessarily malicious. Analysts must examine the context before determining whether an activity represents a threat.

### Example: Detecting a Port Scan

An attacker attempting to discover open ports may generate many TCP SYN packets directed at different ports on the same target.

For example, an Nmap SYN scan:

```bash
nmap -sS -p 1-1000 192.168.1.10
```

This command scans TCP ports 1 through 1000 using SYN packets.

From a defender's perspective, a packet capture may reveal:

- Multiple SYN packets originating from the same host.
- Connections targeting many different destination ports.
- Numerous unsuccessful connection attempts.
- A communication pattern consistent with port enumeration.

This behavior may indicate a **port scan**, although additional investigation is necessary to confirm whether the activity is malicious.

---

## Required Skills and Knowledge

Effective network traffic analysis requires an understanding of several networking concepts.

### 1. TCP/IP Stack and OSI Model

Understanding the TCP/IP and OSI models helps analysts interpret how applications communicate across a network.

Each layer performs specific functions and uses different protocols.

| OSI Layer | Purpose | Examples |
|---|---|---|
| 7 - Application | Provides network services to applications | HTTP, DNS, FTP |
| 6 - Presentation | Handles data representation and encoding | Encryption, encoding |
| 5 - Session | Manages communication sessions | Session establishment and management |
| 4 - Transport | Provides end-to-end transport | TCP, UDP |
| 3 - Network | Handles logical addressing and routing | IPv4, IPv6, ICMP |
| 2 - Data Link | Handles local delivery using frames | Ethernet, MAC |
| 1 - Physical | Transmits bits through physical media | Copper, fiber, radio |

Understanding these layers allows analysts to identify where communication problems or suspicious activities may occur.

### 2. Basic Network Concepts

Analysts should understand:

- **Switching:** How devices communicate within local networks.
- **Routing:** How packets travel between different networks.
- **VLANs:** How networks are logically segmented.
- **Network Topology:** How devices and network segments are connected.
- **Traffic Visibility:** Which communications can be observed from a specific capture point.

The location of a packet capture significantly affects the traffic available for analysis.

For example, capturing traffic on a network backbone may provide visibility into many communications, while capturing traffic from a workstation generally reveals a more limited view.

### 3. Common Ports and Protocols

Recognizing common ports and protocols helps analysts identify unexpected network behavior.

| Protocol | Port | Purpose |
|---|---|---|
| FTP | 20/21 TCP | File transfer |
| SSH | 22 TCP | Secure remote access |
| Telnet | 23 TCP | Unencrypted remote access |
| SMTP | 25 TCP | Email transmission |
| DNS | 53 TCP/UDP | Domain name resolution |
| HTTP | 80 TCP | Web communication |
| POP3 | 110 TCP | Email retrieval |
| IMAP | 143 TCP | Email retrieval |
| HTTPS | 443 TCP | Encrypted web communication |
| SMB | 445 TCP | File and resource sharing |
| RDP | 3389 TCP/UDP | Remote desktop access |

Identifying traffic on unusual ports can reveal misconfigurations, unauthorized services, or suspicious activity.

**Note:** Port numbers alone do not guarantee which application protocol is being used. Services can operate on non-standard ports.

### 4. IP Packets, TCP, and UDP

Understanding the structure and behavior of IP packets and transport protocols is essential for interpreting captured traffic.

**TCP (Transmission Control Protocol)**

- Connection-oriented protocol.
- Provides reliable, ordered delivery.
- Uses sequence numbers and acknowledgments.
- Retransmits lost segments.
- Operates as a byte stream.
- Allows analysts to reconstruct application conversations when sufficient traffic is available.

**UDP (User Datagram Protocol)**

- Connectionless protocol.
- Does not guarantee delivery or ordering.
- Has lower protocol overhead.
- Does not provide built-in retransmission mechanisms.
- Uses individual datagrams rather than a continuous byte stream.

TCP streams are generally easier to follow and reconstruct because TCP maintains information about sequence and acknowledgment.

UDP traffic may be more difficult to reconstruct, depending on the application protocol.

### 5. Protocol Transport Encapsulation

**Encapsulation** is the process of adding protocol headers as data travels down the networking stack.

For example, an HTTP communication over TCP/IP and Ethernet can be represented as:

```text
Application Data (HTTP)
       |
       v
TCP Header + HTTP Data
       |
       v
IP Header + TCP Segment
       |
       v
Ethernet Header + IP Packet + Trailer
```

Each protocol layer adds information necessary for communication.

When receiving traffic, the headers are interpreted and removed as the data moves up the stack. This is called **decapsulation**.

Understanding encapsulation helps analysts identify:

- Source and destination MAC addresses.
- Source and destination IP addresses.
- Source and destination ports.
- Transport protocols.
- Application-layer data.
- Changes in protocol behavior.

---

## Environment and Equipment

Network traffic analysis can involve different software tools and specialized network equipment.

Some tools capture packets directly, while others analyze previously collected traffic or aggregate information from multiple sources.

### Common Traffic Analysis Tools

#### tcpdump

**tcpdump** is a command-line utility used to capture and analyze network packets.

It uses the packet capture library **libpcap** on supported systems.

Capabilities include:

- Capturing live network traffic.
- Filtering traffic by host, port, or protocol.
- Reading previously saved packet captures.
- Saving captured packets to files.
- Inspecting packet headers and protocol information.

Example:

```bash
sudo tcpdump -i eth0
```

Captures traffic from the `eth0` interface.

#### TShark

**TShark** is the command-line version of Wireshark.

It supports:

- Live packet capture.
- Reading packet capture files.
- Protocol decoding.
- Traffic filtering.
- Extracting packet information for further analysis.

TShark is useful when a graphical interface is unavailable or when traffic analysis needs to be automated.

#### Wireshark

**Wireshark** is a graphical network protocol analyzer.

It allows analysts to:

- Capture and inspect network packets.
- Examine protocol headers.
- Apply display filters.
- Follow TCP streams.
- Investigate communication between hosts.
- Identify suspicious traffic patterns.
- Analyze previously captured PCAP files.

Wireshark provides detailed packet dissection, making it easier to examine network communication across different protocol layers.

#### NGrep

**NGrep (Network Grep)** is a packet analysis tool that searches network traffic for specific patterns.

It supports:

- Regular expressions.
- Berkeley Packet Filter syntax.
- Live traffic inspection.
- Reading packet capture files.

NGrep can be particularly useful for analyzing plaintext application protocols such as HTTP and FTP.

#### tcpick

**tcpick** specializes in tracking and reconstructing TCP streams.

It can:

- Track TCP connections.
- Reassemble TCP conversations.
- Extract data from captured streams.
- Reconstruct transferred content when possible.

#### Network TAPs

A **Network TAP (Test Access Point)** is a hardware device used to copy network traffic for monitoring and analysis.

It can provide visibility into traffic traveling across a particular network link.

Network TAPs are useful when analysts need to observe communications without relying on endpoint packet capture.

#### SPAN Ports

**SPAN (Switched Port Analyzer)**, also known as port mirroring, is a feature available on many network switches.

It allows network administrators to copy traffic from selected interfaces or VLANs to a monitoring port.

This enables packet capture tools to analyze traffic that would not normally be visible from their connected interface.

#### Elastic Stack

The **Elastic Stack** provides tools for collecting, storing, searching, and visualizing large volumes of data.

It can aggregate information from multiple network and security sources.

Common uses include:

- Log analysis.
- Traffic monitoring.
- Searching historical data.
- Building security dashboards.
- Investigating suspicious activity.

#### SIEM

**Security Information and Event Management (SIEM)** platforms centralize security data and support event correlation, detection, investigation, and alerting.

An example is **Splunk**.

SIEM platforms can help analysts:

- Correlate events from multiple sources.
- Detect suspicious network behavior.
- Generate security alerts.
- Investigate historical incidents.
- Support threat hunting.

**Note:** A SIEM typically analyzes logs, network metadata, and other security telemetry. Full packet capture is not necessarily available unless integrated with appropriate collection systems.

---

## Berkeley Packet Filter (BPF)

**Berkeley Packet Filter (BPF)** is a technology used to filter network packets according to specified criteria.

BPF filter expressions are commonly used with packet capture tools such as tcpdump.

Instead of collecting every packet, analysts can use filters to focus on the traffic relevant to an investigation.

### Why Use BPF?

Network environments can generate large volumes of traffic.

Filtering helps:

- Reduce unnecessary packet collection.
- Focus on specific hosts or protocols.
- Identify relevant communication patterns.
- Simplify traffic analysis.
- Reduce processing and storage requirements.

### BPF Filter Examples

**Capture traffic involving a specific host:**

```bash
sudo tcpdump -i eth0 'host 192.168.1.10'
```

**Capture traffic involving a specific port:**

```bash
sudo tcpdump -i eth0 'port 80'
```

**Capture TCP traffic:**

```bash
sudo tcpdump -i eth0 'tcp'
```

**Capture UDP traffic:**

```bash
sudo tcpdump -i eth0 'udp'
```

**Capture traffic from a specific source:**

```bash
sudo tcpdump -i eth0 'src host 192.168.1.10'
```

**Capture traffic to a specific destination:**

```bash
sudo tcpdump -i eth0 'dst host 192.168.1.20'
```

**Combine multiple conditions:**

```bash
sudo tcpdump -i eth0 'host 192.168.1.10 and port 443'
```

This captures packets involving `192.168.1.10` and port `443`.

BPF expressions can use logical operators such as:

- `and`
- `or`
- `not`

**Important:** Wireshark capture filters can use BPF syntax, while Wireshark display filters use a different syntax.

---

## Performing Network Traffic Analysis

Network traffic analysis can range from simple packet inspection to complex monitoring environments involving packet capture devices, SIEM platforms, and intrusion detection systems.

Before capturing traffic, analysts must determine where the capture should take place.

### Traffic Visibility

A packet capture tool can only analyze traffic that is visible at its capture point.

In switched networks, traffic is not automatically forwarded to every connected device.

Several factors influence capture visibility:

- Network segmentation.
- VLAN configuration.
- Switch forwarding behavior.
- Monitoring interface placement.
- Port mirroring and Network TAP configurations.

For example, connecting a workstation to the same VLAN as a target does not guarantee visibility into all unicast traffic between other devices.

To capture traffic from additional network segments or specific communication links, analysts may need:

- Network TAPs.
- SPAN ports.
- Port mirroring.
- Appropriate router or switch configurations.

Proper capture placement is essential for obtaining useful evidence.

---

## Network Traffic Analysis Workflow

NTA is a dynamic process rather than a strict sequence of actions.

The investigation may change depending on the objective, available evidence, and network visibility.

The HTB workflow consists of four primary stages, followed by an additional remediation and monitoring activity.

### 1. Ingest Traffic

The first step is to collect network traffic from the appropriate location.

Analysts must determine:

- Which network segment should be monitored?
- Which hosts or protocols are relevant?
- What capture method should be used?
- Are capture filters necessary?

Traffic can be collected through packet capture utilities, network monitoring equipment, or existing monitoring infrastructure.

Capture filters can be applied when the investigation already has a specific target or objective.

### 2. Reduce Noise by Filtering

Network captures often contain large amounts of unrelated traffic.

Filtering reduces unnecessary information and makes suspicious activity easier to identify.

Examples of potentially irrelevant traffic include:

- Broadcast packets.
- Multicast packets.
- Unrelated host communications.
- Unnecessary application traffic.

Analysts can filter by:

- IP addresses.
- Protocols.
- Ports.
- TCP flags.
- Communication patterns.

**Important:** Traffic should only be excluded when it is not relevant to the investigation. Broadcast and multicast traffic may also contain valuable evidence.

### 3. Analyze and Explore

This stage focuses on examining relevant network communications to identify anomalies or security threats.

Analysts may investigate:

- Specific source and destination hosts.
- Unusual network connections.
- Protocol behavior.
- TCP flags.
- Communication frequency.
- Unexpected traffic patterns.
- Encrypted or plaintext communications.

Key questions include:

1. Is the traffic encrypted or transmitted in plaintext?
2. Should this communication be encrypted?
3. Are users attempting to access resources they should not access?
4. Are hosts communicating with devices they normally do not contact?
5. Are there unexpected communication patterns?

The objective is to understand what is happening and determine whether further investigation is necessary.

### 4. Detect and Alert

After analyzing the traffic, analysts evaluate whether the observed behavior is benign or potentially malicious.

Questions include:

- Are there communication errors?
- Are expected devices failing to respond?
- Does the traffic indicate suspicious activity?
- Does the behavior match known attack patterns?

Additional security technologies may assist this process.

**IDS (Intrusion Detection System)**

An IDS monitors activity and generates alerts when suspicious behavior is detected.

**IPS (Intrusion Prevention System)**

An IPS can detect suspicious traffic and, when configured appropriately, actively block or prevent malicious communications.

These systems may use:

- Known signatures.
- Detection rules.
- Heuristics.
- Behavioral analysis.

### 5. Fix and Monitor

Although this step is not part of the main four-stage cycle, it is an important part of the overall investigation.

Once a problem or incident is identified, appropriate corrective actions should be taken.

After applying a fix, analysts should continue monitoring to confirm that:

- The issue has been resolved.
- Suspicious traffic has stopped.
- Network communications have returned to expected behavior.
- The problem does not reappear.

Continuous monitoring helps validate the effectiveness of remediation efforts.

### Workflow Summary

```text
       1. Ingest Traffic
               |
               v
     2. Reduce Noise by Filtering
               |
               v
       3. Analyze and Explore
               |
               v
        4. Detect and Alert
               |
               v
         Fix and Monitor
```

**Important:** NTA is an iterative process. Analysts may return to previous stages, change filters, collect additional traffic, or investigate new hypotheses.

---

## Key Takeaways

- **NTA** provides visibility into network communications and helps detect anomalies, troubleshoot problems, and investigate security incidents.
- A **baseline** helps distinguish normal network activity from potentially suspicious behavior.
- Knowledge of **TCP/IP, OSI, ports, protocols, and encapsulation** is essential for understanding packet captures.
- **tcpdump** and **Wireshark** are two of the main tools used for packet capture and traffic analysis.
- **BPF** allows analysts to focus on relevant traffic using capture filters.
- Network capture visibility depends on the capture point and network architecture.
- The core NTA workflow is **Ingest Traffic → Reduce Noise → Analyze and Explore → Detect and Alert**.
- **Fix and Monitor** helps verify that detected problems have been successfully addressed.
- Suspicious traffic should be investigated in context before being classified as malicious.
