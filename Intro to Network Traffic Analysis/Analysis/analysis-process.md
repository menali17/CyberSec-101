
# The Analysis Process

## Introduction

**Network Traffic Analysis (NTA)** is a dynamic process that depends on the available tools, organizational permissions, and visibility into the network.

The objective is to establish a repeatable process for examining network traffic, identifying anomalies, investigating security incidents, and troubleshooting network problems.

Traffic analysis involves the detailed examination of network events and communication patterns to determine their origin, behavior, and potential impact.

### Purpose of Traffic Analysis

Network traffic analysis helps security professionals:

- Identify deviations from normal network behavior.
- Detect potentially malicious network communications.
- Investigate unauthorized remote connections using protocols such as RDP, SSH, and Telnet.
- Identify unusual activity preceding network problems.
- Analyze communication trends and compare them against established baselines.
- Troubleshoot connectivity and protocol-related issues.
- Determine whether network infrastructure is functioning correctly.

### Network Visibility

One of the most important benefits of NTA is **network visibility**.

By monitoring network traffic, analysts can obtain information about:

- Network utilization.
- Hosts generating the most traffic (**Top Talkers**).
- Communication between internal hosts and servers.
- Commonly used ports and protocols.
- Unusual or unexpected connections.
- Changes in normal network behavior.

This visibility helps administrators and defenders identify problems before or shortly after they occur.

### Establishing a Baseline

A **baseline** represents the normal traffic patterns of a network.

By capturing and analyzing traffic over different periods, analysts can determine which communications are expected.

A baseline helps identify:

- Normal communication patterns.
- Commonly used services and protocols.
- Expected connections between hosts.
- Unusual traffic volumes.
- Deviations from regular network operations.

Without a baseline, determining whether observed traffic is normal can be significantly more difficult and time-consuming.

### Automated and Manual Analysis

Network traffic analysis can be supported by several security tools, including:

- Intrusion Detection Systems (IDS).
- Intrusion Prevention Systems (IPS).
- Firewalls.
- Host and network logs.
- Splunk.
- Elastic Stack (ELK).

These tools can help identify and alert on suspicious activities using predefined signatures and detection mechanisms.

However, analysts should not rely exclusively on automated tools.

Attackers continuously develop techniques to bypass existing security measures.

**Important:** Automated detection should be combined with manual traffic inspection to validate findings and identify activities that automated tools might miss.

---

## Analysis Dependencies

Network traffic capture and analysis can be performed using two approaches:

1. **Passive Traffic Capture**
2. **Active Traffic Capture**

Both approaches have different requirements depending on the network environment and the available equipment.

Traffic can be analyzed in real time during capture or examined afterward using previously collected data.

### Passive Traffic Capture

**Passive capture** involves copying and observing network traffic without directly interacting with or modifying the packets being transmitted.

The analyst collects traffic visible from the monitoring location and examines it using packet analysis tools.

Passive capture typically relies on:

- A monitoring device connected to the appropriate network segment.
- A mirrored port configured on a switch or router.
- A network interface capable of receiving the copied traffic.
- A packet capture tool such as Wireshark or tcpdump.

The amount of traffic visible depends on the monitoring device's position within the network.

In switched networks, traffic from other devices is not automatically forwarded to the monitoring interface.

To observe communications that would otherwise be unavailable, a mirrored port can be configured to send copies of selected traffic to the capture device.

### Active Traffic Capture

**Active capture**, as described in this section, involves a more hands-on approach to monitoring network communications.

This approach is also referred to as **in-line traffic capture**.

The monitoring equipment is positioned directly along the communication path between network segments or devices.

This configuration may require:

- Changes to the existing network topology.
- A network TAP or monitoring device with multiple network interfaces.
- Proper placement along the network communication path.
- Sufficient storage and processing resources.

An in-line monitoring setup allows traffic passing through the monitored link to be observed.

---

## Traffic Capture Dependencies

The following dependencies should be considered when preparing to capture network traffic.

| Dependency | Passive | Active |
|---|---|---|
| Permission | Required | Required |
| Mirrored Port | Yes | No |
| Capture Tool | Required | Required |
| In-line Placement | No | Yes |
| Network TAP or Multiple NICs | No | Yes |
| Storage and Processing Power | Required | Required |

These requirements represent the capture configurations described in the HTB section.

### 1. Permission

Before performing network traffic capture, analysts must obtain permission from an authorized individual within the organization.

Capturing network traffic without authorization may violate organizational policies or legal requirements.

This is particularly important in environments that handle sensitive information, such as:

- Healthcare organizations.
- Financial institutions.
- Other environments with strict data protection requirements.

**Important:** Always obtain written authorization before capturing or analyzing network traffic.

### 2. Mirrored Port

A **mirrored port** is a network interface configured to receive copies of traffic from other ports or interfaces.

Switches and routers can be configured to forward copies of selected network communications to a monitoring device.

This allows analysts to inspect traffic that would not normally be visible from their connected interface.

#### Promiscuous Mode

A network interface can be configured in **promiscuous mode** to receive frames visible to that interface, including frames not addressed to its own MAC address.

When combined with a mirrored port, this allows the monitoring device to inspect copied network traffic.

#### Network Segmentation

In switched environments, VLANs and switch ports limit which traffic is visible from a particular monitoring location.

To capture traffic from a specific segment, the monitoring device must have access to that traffic.

This may require:

- Connecting to the appropriate network segment.
- Configuring port mirroring.
- Receiving copied traffic from the relevant interfaces.

#### Wireless Networks

Passive traffic capture in wireless environments can be more complicated.

The analyst must consider the wireless network being monitored and the available capture capabilities.

Simply observing nearby wireless transmissions may reveal SSID advertisements without providing access to the actual communications that need to be analyzed.

### 3. Capture Tool

A **capture tool** is necessary to collect network traffic and make it available for analysis.

Examples include:

- tcpdump.
- Wireshark.
- NetMiner.

These tools can capture network packets and help analysts inspect their contents.

#### PCAP Files

Captured network traffic can be stored in **PCAP (Packet Capture)** files for later analysis.

PCAP files may become very large, depending on the amount of traffic collected and the capture duration.

When analyzing large PCAP files in tools such as Wireshark, applying filters may require the application to process significant amounts of packet data.

This can consume substantial computing resources.

Therefore, the capture system should have sufficient storage and processing capacity.

### 4. In-line Placement

In-line placement involves positioning monitoring equipment directly within a network communication path.

This requires a change to the network topology.

The monitoring device observes traffic as it travels between its source and destination.

The goal is to maintain normal communication while allowing the traffic to be captured and analyzed.

### 5. Network TAP or Multiple NICs

A **Network TAP** is a device used to obtain copies of traffic passing through a network link.

A monitoring computer equipped with multiple Network Interface Cards (NICs) can also be used in certain in-line configurations.

The HTB section discusses placing monitoring equipment on a **Layer 3 link between switched network segments**.

This position can provide visibility into traffic being routed between networks.

Such placement is useful because traffic crossing the monitored link can be observed without relying solely on visibility from individual switched ports.

### 6. Storage and Processing Power

Capturing network traffic requires sufficient storage space and processing resources.

The amount of data collected varies depending on the capture location.

For example:

- Capturing traffic from a local network segment may produce a manageable amount of data.
- Capturing traffic from a heavily utilized routed link may produce significantly more data.

Large captures can place considerable demands on the monitoring system.

**Important:** The capture device should have enough resources to collect and process the expected network traffic.

---

## The Importance of a Network Baseline

Understanding the normal flow of network traffic is one of the most useful preparations for traffic analysis.

Although analysis can be performed without a baseline, the investigation may require significantly more time and effort.

A baseline allows analysts to recognize known-good communication and focus on unusual activities.

### Benefits of a Baseline

- Quickly identify expected network communication.
- Filter out traffic that is not relevant to the investigation.
- Recognize unusual connections.
- Compare current traffic volumes against normal behavior.
- Identify suspicious communication between hosts.
- Reduce the time required to investigate network problems.

Without a baseline, analysts may need to inspect individual network conversations to determine whether each activity is legitimate.

---

## Practical Scenario - Investigating Network Issues

The HTB section presents an example involving a large corporate network.

### Scenario

You are a network administrator responsible for a company with several thousand employees.

Several workstations within a network segment begin experiencing problems.

The reported symptoms include:

- Extremely high network latency.
- Unexpected files appearing on users' desktops.

Your task is to investigate the affected network segment and determine what might be causing these issues.

### Initial Traffic Capture

To begin the investigation:

1. Connect a monitoring computer to the affected network segment.
2. Start capturing network traffic.
3. Allow the capture to run for several minutes.
4. Stop the capture.
5. Begin analyzing the collected packets.

The captured traffic provides information about the communication occurring during the observed period.

### Analysis Without a Baseline

Without knowing the network's normal behavior, it becomes difficult to determine which communications are relevant.

The analyst must investigate questions such as:

- Which hosts normally communicate with each other?
- Which ports and protocols are commonly used?
- Are all observed hosts legitimate network devices?
- Are there any unexpected connections?
- Which communications might be causing the reported problems?

The investigation becomes more difficult because a large amount of traffic must be examined.

### Analysis With a Baseline

With an established baseline, analysts can quickly identify and filter out known-good communications.

This allows them to focus on unusual network activity.

One useful approach is to examine the hosts generating the most traffic.

#### Top Talkers

**Top Talkers** are hosts responsible for a large amount of network communication during a particular observation period.

Wireshark provides statistical features that help analysts identify these hosts.

By comparing current network activity against the baseline, analysts can determine whether a host is generating an unusual amount of traffic.

For example:

- A host generating much more traffic than usual may require investigation.
- A workstation communicating with unexpected destinations may indicate abnormal behavior.
- Unexpected connections between internal hosts may reveal potentially suspicious activity.

### Identifying Suspicious Connections

After filtering out expected traffic, the investigation reveals several user workstations communicating over the following ports:

```text
TCP 8080
TCP 445
```

These ports are commonly associated with:

| Port | Common Service |
|---|---|
| 8080/TCP | Alternative HTTP / Web Services |
| 445/TCP | SMB |

Neither port is inherently suspicious.

However, the unusual behavior comes from the devices involved in the communication.

The HTB scenario describes **user workstations communicating directly with other user workstations** over these ports.

In the environment described, this differs from the expected communication patterns.

Normally:

- Web traffic is expected to reach web servers or internal application servers.
- SMB traffic commonly involves file-sharing resources and designated servers.

Unexpected connections between user workstations may therefore require additional investigation.

### Potential Security Implications

When combined with the reported symptoms, the unusual traffic patterns raise concerns about a possible security incident.

Relevant observations include:

- High network latency.
- Unexpected files appearing on desktops.
- Unusual connections between user workstations.
- Communication over TCP ports 8080 and 445.

These findings may indicate suspicious activity within the affected network segment.

However, additional investigation is necessary before confirming a security breach.

### Escalating the Incident

After identifying potentially suspicious communication, the analyst can create a trouble ticket or escalate the findings for further investigation.

The objective is to determine whether the unusual activity is related to a potential network compromise.

**Important:** The faster defenders obtain useful network visibility, the sooner they can investigate suspicious behavior and potentially limit the impact of a security incident.

---

## Key Takeaways

- **Network Traffic Analysis** is a dynamic process influenced by available tools, permissions, and network visibility.
- NTA is useful for both security investigations and network troubleshooting.
- **Network visibility** allows analysts to understand communication patterns and identify anomalies.
- A **baseline** provides a reference for normal network behavior.
- **Passive capture** involves observing and copying traffic without directly modifying the communication.
- **Active / in-line capture** involves placing monitoring equipment within the network communication path.
- **Mirrored ports** can provide visibility into traffic that would otherwise not reach the monitoring interface.
- Capturing traffic requires proper authorization, appropriate tools, and sufficient computing resources.
- **PCAP files** can become large and require significant processing resources.
- **Top Talkers** help identify hosts generating large amounts of network traffic.
- Traffic over legitimate ports may still be suspicious when communication patterns differ from the network baseline.
- Automated security tools should be combined with manual analysis.
- Understanding normal network communication is essential for identifying potential security incidents.
