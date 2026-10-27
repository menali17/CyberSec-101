
# Guided Lab: Traffic Analysis Workflow

## Introduction

This guided lab focuses on applying the network traffic analysis workflow to investigate suspicious activity within a corporate network.

The objective is to use previously learned concepts to:

- Define the investigation scope.
- Identify suspicious hosts and network conversations.
- Analyze captured network traffic.
- Apply Wireshark display filters.
- Examine protocol statistics.
- Follow TCP streams.
- Identify possible signs of compromise.
- Document findings.
- Recommend an appropriate response.

The investigation uses the historical capture provided with `guided-analysis.zip` and live traffic from the HTB Academy network.

---

## Lab Scenario

A network administrator identified an unusual connection involving Bob's computer while examining network baseline captures.

The initial scenario identifies Bob's host as:

`172.16.10.90`

However, the guided walkthrough investigates traffic involving:

- `10.129.43.4`
- `10.129.43.29`

The walkthrough identifies `10.129.43.4` as the primary suspicious host.

**Note:** The source material uses different IP addresses for Bob's host in the introduction and walkthrough. These addresses should not be treated as interchangeable without additional evidence.

### Investigation Objective

Determine whether the unusual network activity represents:

- Legitimate communication.
- Unauthorized access.
- Suspicious remote command execution.
- Potential compromise of additional hosts.

---

## Task 1 - Connect to the Live Host

The lab uses a graphical environment accessible through RDP.

The connection can be established from:

- HTB Pwnbox.
- A personal VM connected to the Academy VPN.

### Connection Command

```bash
xfreerdp /v:<target IP> /u:htb-student /p:'HTB_@cademy_stdnt!'
```

The target IP is provided when the lab machine is spawned.

### Starting the Capture

After connecting:

1. Open Wireshark.
2. Select interface `ens224`.
3. Start capturing network traffic.
4. Examine the captured communications.

The investigation can also be performed using the provided historical PCAP file.

---

# Traffic Analysis Workflow

The HTB material presents an eight-step workflow for investigating network traffic.

## Step 1 - Identify the Issue

Determine what problem has been reported.

### Lab Finding

Suspicious traffic has been detected originating from within the corporate network.

The objective is to determine whether this communication indicates malicious activity.

---

## Step 2 - Define the Scope and Goal

Establish the boundaries of the investigation.

Identify:

- The suspicious activity.
- The affected systems.
- The relevant time period.
- The available supporting evidence.

### Lab Scope

The guided walkthrough investigates traffic associated with:

`10.129.43.4`

### Timeframe

The investigation considers activity within the last 48 hours.

Live capture is recommended to determine whether the suspicious communication is still occurring.

### Supporting Evidence

```text
NTA-guided.pcap
```

This file contains historical traffic relevant to the investigation.

---

## Step 3 - Define the Targets

Identify the hosts, networks, and protocols that should be investigated.

### Target Host

`10.129.43.4`

### Target Network

`10.129.43.0/24`

### Additional Investigation Scope

The analysis includes any hosts communicating with the suspicious system.

### Port of Interest

`4444`

The walkthrough identifies an unknown protocol using this port.

The port should be investigated to determine the nature of the communication.

---

## Step 4 - Capture Network Traffic

The investigation uses two possible sources of evidence.

### Historical Traffic

Open the provided PCAP:

`NTA-guided.pcap`

### Live Traffic

Capture network traffic from a connection with visibility into the target network.

The purpose is to determine whether suspicious communication is still occurring.

---

## Step 5 - Identify Relevant Traffic

Once network traffic has been collected, reduce unnecessary information using filters.

### Initial Objective

Focus on traffic involving the primary suspicious host:

`10.129.43.4`

### Display Filter

```text
ip.addr == 10.129.43.4
```

This displays packets where the specified IPv4 address appears as a source or destination.

The purpose of filtering is to:

- Remove unrelated communications.
- Reduce noise.
- Focus on the investigation scope.
- Identify relevant hosts and conversations.

---

## Step 6 - Understand the Captured Traffic

Once irrelevant traffic has been removed, examine the remaining packets.

The HTB walkthrough begins with a broad overview and progressively focuses on suspicious communications.

### Investigative Approach

1. Review conversation statistics.
2. Examine protocol distribution.
3. Analyze less common traffic.
4. Investigate TCP communications.
5. Identify suspicious TCP streams.
6. Examine application data.

---

# Guided Investigation

## 1. Conversation Statistics

Wireshark provides conversation information through:

**Statistics → Conversations**

This feature helps identify communication between network endpoints.

### HTB Findings

The walkthrough reports three conversations in the PCAP file.

All three are associated with the suspicious host.

### Purpose

Conversation statistics help determine:

- Which hosts communicate.
- How many conversations exist.
- Which endpoints require further investigation.

---

## 2. Protocol Hierarchy

Wireshark provides protocol distribution information through:

**Statistics → Protocol Hierarchy**

This feature shows which protocols are present in the capture.

### HTB Findings

The capture contains:

- Predominantly TCP traffic.
- A smaller amount of UDP and other network traffic.

Because TCP represents most of the captured traffic, the walkthrough first examines the smaller set of packets.

---

## 3. Investigating UDP and Other Traffic

The walkthrough examines the less frequent network traffic first.

### Reported Observations

The material mentions:

- Four ARP packets.
- Four NAT-related packets.
- One SSDP packet.

### Assessment

The walkthrough concludes that this traffic represents normal network activity and is not the primary concern.

**Important:** ARP is not a UDP protocol. The original walkthrough groups these packets while discussing UDP, but ARP packets should be considered separately.

---

## 4. Investigating TCP Traffic

After examining the less frequent traffic, the analysis focuses on TCP.

### Display Filter

```text
!udp && !arp
```

This expression excludes UDP and ARP packets from the displayed results.

### HTB Findings

The remaining traffic is described as a TCP conversation between:

```text
10.129.43.4 ↔ 10.129.43.29
```

### Identifying the Connection

The walkthrough identifies the TCP Three-way Handshake.

```text
SYN
SYN, ACK
ACK
```

The same endpoint addresses and ports continue to appear throughout the connection.

This allows the analyst to identify packets belonging to the same conversation.

---

## 5. Examining TCP Session Termination

The walkthrough notes that the capture does not show a normal TCP connection termination.

### Missing Events

No connection teardown is visible.

The analysis also does not identify TCP RST packets.

### Interpretation

This suggests that the TCP session may have remained active when the capture ended.

However, the absence of termination packets in a capture does not independently prove that the connection is still active.

---

## 6. Following the TCP Stream

The next step is to reconstruct the suspicious conversation.

### Wireshark Procedure

1. Select a packet belonging to the suspicious TCP conversation.
2. Right-click the packet.
3. Choose **Follow → TCP Stream**.
4. Examine the reconstructed data.

### Purpose

Following the stream allows the analyst to inspect the application data exchanged between the endpoints.

### HTB Finding

The walkthrough reveals readable commands being executed on a Windows system.

The contents indicate suspicious remote interaction with the host.

---

## 7. Identifying Suspicious Commands

The reconstructed TCP stream reveals commands associated with system reconnaissance.

### Observed Commands

| Command | Purpose |
|---|---|
| `whoami` | Identifies the current user |
| `ipconfig` | Displays network configuration |
| `dir` | Lists files and directories |

### Interpretation

These commands suggest that someone was gathering information about the system.

The commands themselves are legitimate administrative utilities.

However, when observed within an unexplained remote session, they can indicate an attempt to understand the compromised environment.

---

## 8. Identifying Account Manipulation

The walkthrough reveals additional actions involving local user accounts.

### Observed Behavior

Someone created a user account named:

```text
hacker
```

The account was then added to the local administrators group.

### Why This Matters

Creating an administrative account can provide an additional way to access and control a system.

In the context of unauthorized remote activity, this is a strong indicator of compromise.

### Investigation Assessment

The walkthrough interprets the command activity and account manipulation as evidence that an unauthorized actor has accessed the corporate environment.

---

# Step 7 - Document Findings

Documentation is an essential part of the network traffic analysis process.

The HTB material recommends recording information throughout the investigation.

### Information to Document

- Timeframes covered by captures.
- Suspicious hosts.
- Network ports involved.
- Relevant network conversations.
- Timestamps.
- Packet numbers.
- Commands observed.
- Files or other significant data.
- Supporting screenshots.

### Purpose

Documentation helps preserve the investigative findings and supports decisions made by incident response personnel.

The walkthrough uses screenshots to document the traffic and suspicious commands.

---

# Step 8 - Summarize the Analysis

The final step is to prepare a clear summary of the findings.

### HTB Investigation Findings

The walkthrough concludes that:

1. Suspicious communications were identified within the corporate network.
2. Hosts `10.129.43.4` and `10.129.43.29` were involved in the suspicious TCP communication.
3. The TCP stream contained readable Windows commands.
4. Commands such as `whoami`, `ipconfig`, and `dir` indicated system reconnaissance.
5. A user account named `hacker` was created.
6. The account was assigned local administrator privileges.
7. The activity was interpreted as evidence of a network compromise.

### Recommended Response

The walkthrough recommends initiating a formal Incident Response (IR) procedure.

The objective is to:

- Stop further malicious activity.
- Investigate affected systems.
- Determine the scope of compromise.
- Identify additional impacted hosts.
- Remove the malicious presence.

---

# Incident Response Findings

The final HTB summary describes conclusions reached by the incident response team.

### Netcat Shell

The team determined that the attacker was using a Netcat shell to interact remotely with Bob's host.

This explains why command execution was visible in the reconstructed TCP stream.

### Lateral Movement

The attacker also attempted to use RDP from Bob's host to access another Windows desktop in the network.

The incident response team obtained packet capture data associated with that RDP activity.

### Containment

Bob's host was quarantined.

Incident response procedures were initiated to determine:

- What information had been accessed or taken.
- Whether additional hosts were compromised.
- How the attacker could be removed from the environment.

These findings are presented in the HTB summary as conclusions of the incident response investigation.

---

## Useful Wireshark Features and Filters

| Feature / Filter | Purpose |
|---|---|
| Statistics → Conversations | Identify communicating endpoints |
| Statistics → Protocol Hierarchy | Examine protocol distribution |
| `ip.addr == 10.129.43.4` | Focus on the suspicious host |
| `udp` | Display UDP packets |
| `tcp` | Display TCP packets |
| `arp` | Display ARP packets |
| `!udp && !arp` | Exclude UDP and ARP |
| Follow → TCP Stream | Reconstruct a TCP conversation |
| `tcp.stream eq 0` | Display a specific TCP conversation |

---

## Key Takeaways

- Effective network traffic analysis begins with a defined problem and scope.
- Identifying relevant hosts and timeframes helps focus an investigation.
- Wireshark's Conversations and Protocol Hierarchy features provide useful traffic statistics.
- Filtering helps remove irrelevant traffic and isolate suspicious communications.
- TCP handshakes help identify established network connections.
- Following TCP streams can reveal application data exchanged between hosts.
- Readable command execution can provide evidence of suspicious remote activity.
- System reconnaissance commands should be interpreted within their context.
- Unauthorized creation of an administrative account is a significant indicator of compromise.
- Accurate documentation is essential for incident response.
- Investigation summaries should communicate the evidence and potential impact clearly.
- Network traffic analysis can help identify compromise, remote command execution, and attempted lateral movement.
