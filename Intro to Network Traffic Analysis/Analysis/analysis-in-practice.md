
# Analysis in Practice

## Introduction

**Network Traffic Analysis (NTA)** is a dynamic process used to investigate network communication, identify anomalies, and understand potential security incidents.

The analysis process is influenced by:

- The type of issue being investigated.
- The available network visibility.
- The network environment.
- The tools and information available to the analyst.

Traffic analysis does not follow a strictly linear workflow. Depending on the findings, analysts may need to return to previous steps and investigate additional evidence.

This section introduces four types of analysis:

1. **Descriptive Analysis**
2. **Diagnostic Analysis**
3. **Predictive Analysis**
4. **Prescriptive Analysis**

Together, these approaches provide a structured workflow for investigating network traffic and determining appropriate actions.

---

## Descriptive Analysis

**Descriptive Analysis** is the process of examining and describing a dataset based on its characteristics.

It helps analysts identify:

- The problem being investigated.
- The scope of the investigation.
- The relevant time period.
- The network components involved.
- Potential errors or outliers in the collected data.

Before capturing and inspecting network traffic, analysts should establish what they are looking for.

### 1. What Is the Issue?

The first step is identifying the problem.

For example:

- Is there a suspected security breach?
- Are users experiencing network connectivity problems?
- Is unusual activity occurring within the network?

Understanding the issue helps determine the appropriate direction for the investigation.

### 2. Define the Scope and Goal

The next step is establishing the objective and time period of the investigation.

Consider the following scenario:

Several hosts may have downloaded malicious executable files from:

```text
bad.example.com
```

The investigation has the following characteristics:

**Target:**

Multiple hosts potentially downloading malicious files.

**Timeframe:**

```text
Last 48 hours + next 2 hours
```

**Supporting Information:**

Suspected filenames:

```text
superbad.exe
new-crypto-miner.exe
```

This information helps analysts identify which communications should be examined.

### 3. Define the Targets

Determine which network segments, hosts, and protocols are relevant.

For the example investigation:

**Network Scope:**

```text
192.168.100.0/24
```

**Protocols:**

- HTTP
- FTP

By identifying the network and protocols involved, analysts can focus on the relevant communications.

### Descriptive Analysis Summary

Descriptive Analysis establishes:

1. What problem are we investigating?
2. What are we trying to find?
3. When did the suspected activity occur?
4. Which hosts, networks, and protocols are involved?

This information establishes the foundation for the next stage of the investigation.

---

## Diagnostic Analysis

**Diagnostic Analysis** focuses on understanding the causes, effects, and relationships associated with an event.

While Descriptive Analysis identifies what happened or what needs investigation, Diagnostic Analysis attempts to determine why it happened.

It uses collected evidence, correlation, and interpretation to investigate the underlying cause.

### 4. Capture Network Traffic

After identifying the investigation scope, the next step is collecting relevant traffic.

In the HTB example, we need visibility into:

```text
192.168.100.0/24
```

The objective is to identify potentially malicious file transfers involving the suspected executable files.

Two types of data may be useful:

**Live Traffic:**

Capture network communications as they occur to identify ongoing file transfers.

**Historical Traffic:**

Request previously collected information from network administrators or monitoring systems.

Examples include:

- PCAP files.
- NetFlow records.
- Historical traffic data available through a SIEM.

The goal is to obtain traffic covering the relevant time period and network segment.

### 5. Identify Required Traffic Components (Filtering)

After collecting traffic, analysts should remove unnecessary information from their analysis.

This includes traffic that:

- Is unrelated to the investigation.
- Matches normal network behavior.
- Does not involve relevant hosts or protocols.

For the example investigation, we should focus on:

- HTTP traffic.
- FTP traffic.
- Communications involving `192.168.100.0/24`.
- Requests involving the suspected executable files.
- Potential file transfers.

Filtering reduces the amount of data that needs to be examined.

### 6. Understand the Captured Network Traffic

Once irrelevant traffic has been filtered out, analysts can examine the remaining communications.

In this example, we are looking for evidence of malicious executable transfers.

#### Analyzing FTP Traffic

Wireshark provides a display filter for FTP data traffic:

```text
ftp-data
```

This filter helps identify FTP data transfers.

When relevant transfer data is available, analysts can attempt to reconstruct transferred files.

The objective is to determine whether the suspected executables were transferred using FTP.

#### Analyzing HTTP Traffic

For HTTP, analysts can filter requests using:

```text
http.request.method == "GET"
```

This Wireshark display filter shows HTTP GET requests.

We can then inspect the requests for filenames such as:

```text
superbad.exe
new-crypto-miner.exe
```

For example:

```http
GET /downloads/superbad.exe HTTP/1.1
Host: bad.example.com
```

Finding a matching request helps identify hosts that attempted to retrieve the suspicious file.

Examining the corresponding responses and traffic may help determine whether the files were successfully transferred.

#### Investigating Internal Transfers

The analysis should not focus exclusively on communication with the suspicious external domain.

Analysts should also examine whether the same files were transferred between internal hosts.

This may help identify additional affected systems.

### Diagnostic Analysis Summary

Diagnostic Analysis involves:

1. Capturing relevant network traffic.
2. Obtaining historical evidence when available.
3. Filtering unnecessary communication.
4. Examining relevant protocols and hosts.
5. Investigating suspicious file transfers.
6. Determining the possible causes and effects of the observed activity.

By examining the traffic and the events surrounding the problem, analysts can begin validating the suspected cause of the incident.

---

## Predictive Analysis

**Predictive Analysis** uses historical and current data to identify trends, recognize deviations from expected behavior, and estimate possible future occurrences.

It builds upon the findings obtained during Descriptive and Diagnostic Analysis.

By comparing observed traffic against established baselines and known malicious indicators, analysts can better understand the significance of suspicious activity.

The HTB workflow associates this stage with documenting findings and summarizing the investigation.

### 7. Note-Taking and Mind Mapping

Documenting findings is essential throughout a network traffic investigation.

Analysts should maintain detailed notes about:

- Actions performed during the investigation.
- Relevant observations.
- Suspicious hosts.
- Network conversations.
- Captured evidence.
- Important timestamps.

For the example investigation, the HTB material emphasizes documenting:

#### Capture Timeframes

Record the time periods during which network traffic was captured.

This helps correlate the collected evidence with the suspected incident.

#### Suspicious Hosts

Identify the systems involved in unusual communications or potentially malicious file transfers.

#### Relevant Conversations

Document network conversations involving the suspicious executable files.

Include:

- Source and destination hosts.
- Relevant timestamps.
- Packet numbers.
- Conversations containing the suspected files.

These details allow analysts to revisit important evidence during further investigation.

### 8. Summary of the Analysis

After examining the relevant traffic, analysts should summarize their findings.

The report should explain:

- What was investigated.
- What suspicious activity was identified.
- Which hosts were involved.
- What evidence supports the findings.
- What the investigation revealed.

The findings should be communicated clearly so that the appropriate personnel can make informed decisions.

For example, the results may help determine whether to:

- Quarantine affected hosts.
- Initiate a broader incident response.
- Continue investigating the environment.

**Important:** Analysis findings can influence security decisions, so reports should be clear, accurate, and concise.

### Predictive Analysis Summary

By evaluating collected data and comparing it against:

- Normal network baselines.
- Historical traffic patterns.
- Known malicious indicators.
- Signatures associated with exploitation or malicious tools.

Analysts can identify trends and potentially suspicious behavior.

The resulting findings help create a clearer understanding of the incident and support subsequent decisions.

---

## Prescriptive Analysis

**Prescriptive Analysis** focuses on determining which actions should be taken to resolve an identified problem or prevent future occurrences.

It uses the results of the previous analysis stages to support decisions.

The objective is to identify appropriate actions based on the available evidence.

### Purpose

Prescriptive Analysis helps determine:

- What actions should be taken to address the problem?
- How can the impact be reduced?
- What measures could prevent similar incidents?
- What improvements can be made to the investigation process?

In the HTB example, the findings may support actions such as isolating affected systems or performing additional incident response activities.

### Lessons Learned

After resolving the problem, analysts should review the investigation process.

This includes documenting:

- Which actions were successful.
- Which actions were ineffective.
- What could have been done differently.
- What improvements should be made.

Recording lessons learned helps improve future investigations and strengthen the organization's response capabilities.

---

## Complete Network Traffic Analysis Workflow

The HTB section combines the previous analysis stages into an eight-step workflow.

### 1. What Is the Issue?

Identify the problem being investigated.

Examples:

- Suspected security breach.
- Network connectivity issue.

### 2. Define the Scope and Goal

Determine what needs to be investigated and during which period.

Example:

```text
Target: Hosts potentially downloading malicious files.

Domain: bad.example.com

Timeframe: Last 48 hours + next 2 hours.

Files:
- superbad.exe
- new-crypto-miner.exe
```

### 3. Define the Targets

Identify the relevant networks, hosts, and protocols.

Example:

```text
Network: 192.168.100.0/24

Protocols:
- HTTP
- FTP
```

### 4. Capture Network Traffic

Collect live traffic from an appropriate monitoring location.

Request historical information, such as PCAP or NetFlow data, when available.

### 5. Identify Required Traffic Components

Filter out irrelevant traffic and focus on communications related to the investigation.

For example:

- HTTP and FTP traffic.
- Relevant hosts within the subnet.
- Requests involving the suspected filenames.

### 6. Understand the Captured Traffic

Examine the relevant network conversations.

Useful Wireshark display filters include:

```text
ftp-data
```

```text
http.request.method == "GET"
```

Look for evidence of file transfers involving the suspected executables.

### 7. Document the Findings

Record:

- Capture timeframes.
- Suspicious hosts.
- Relevant network conversations.
- Timestamps.
- Packet numbers.

### 8. Summarize the Analysis

Prepare a summary explaining the relevant findings.

The summary should provide enough information for the appropriate personnel to determine the next steps.

### Workflow Overview

```text
1. Identify the Issue
          |
          v
2. Define Scope and Goal
          |
          v
3. Define Targets
          |
          v
4. Capture Network Traffic
          |
          v
5. Filter Relevant Traffic
          |
          v
6. Analyze Captured Traffic
          |
          v
7. Document Findings
          |
          v
8. Summarize Results
```

### Iterative Analysis

Network traffic analysis is not always completed in a single attempt.

New findings may require analysts to return to previously captured traffic and perform additional investigations.

For example, if a malicious executable was transferred to one host, analysts may need to examine other communications involving that host.

This could reveal whether the activity spread through additional network connections.

**Important:** The analysis process may need to be repeated as new evidence becomes available.

---

## Key Components of an Effective Analysis

The HTB section identifies three important factors for effective network traffic analysis.

### 1. Know Your Environment

Understanding the network environment is essential for identifying suspicious activity.

Analysts should know:

- Which hosts belong to the network.
- Which devices provide important services.
- How network segments are connected.
- What normal communication patterns look like.

Useful resources include:

- Asset inventories.
- Network maps.

Without this information, distinguishing legitimate hosts from unauthorized or rogue devices becomes more difficult.

### 2. Placement Is Key

The location of the packet capture device determines which communications can be observed.

The ideal capture location is generally as close as possible to the source of the problem, while still providing the necessary visibility.

#### External Traffic

If suspicious traffic originates from the internet, monitoring inbound network links can help analysts observe the relevant communications.

#### Internal Traffic

If the issue appears to affect a specific internal host, monitoring the same network segment may provide useful information.

**Important:** Proper monitoring placement is essential for capturing the traffic relevant to an investigation.

### 3. Persistence

Not every suspicious activity will be visible during the first capture.

Some malicious communications occur infrequently.

For example, an attacker may configure a **Command and Control (C2)** server to communicate with compromised hosts only at specific intervals.

These communications might occur:

- Every few hours.
- Once per day.
- Even less frequently.

A short capture may not contain the suspicious activity.

Analysts should remain persistent and continue investigating when the available evidence indicates a potential problem.

Failing to identify these communications could allow malicious activity to continue undetected.

---

## Analysis Approach

The HTB section provides several recommendations for approaching network traffic investigations.

### Start With Standard Protocols

Begin by examining commonly used network protocols before moving toward more specialized or environment-specific traffic.

Examples include:

- HTTP/HTTPS.
- FTP.
- Email protocols.
- TCP.
- UDP.

Investigating common protocols first can help identify relevant communication and eliminate unnecessary traffic from the analysis.

Afterward, examine protocols commonly used for remote access and communication between systems, including:

- SSH.
- RDP.
- Telnet.

When evaluating these connections, consider the organization's security policies.

For example:

- Are external RDP connections permitted?
- Is Telnet allowed in the environment?
- Are remote administrative connections expected?

A protocol may be legitimate in one environment but prohibited in another.

### Look for Patterns

Repeated communication patterns can reveal potentially suspicious behavior.

For example, a host may contact an external server at the same time every day.

This could represent normal application activity or a possible Command and Control communication pattern.

Analysts should examine recurring connections to determine whether they match expected network behavior.

### Check Host-to-Host Traffic

In the environment described by the HTB material, ordinary user workstations generally communicate with infrastructure services rather than directly with each other.

Common destinations include:

- DHCP infrastructure.
- DNS servers.
- Enterprise applications.
- Web servers.
- File-sharing servers.
- Domain controllers.
- Authentication services.

Unexpected workstation-to-workstation communication may require investigation.

This is particularly important when the communication differs from the established network baseline.

### Look for Unique Events

Unusual or infrequent events may indicate suspicious activity.

Examples include:

**Changes in Communication Frequency**

A host that normally accesses a particular website ten times per day suddenly accesses it only once.

**Unexpected User-Agent Strings**

A host sends an HTTP request containing a User-Agent value that does not match its usual applications.

**Unusual External Connections**

A workstation communicates with an unexpected internet server.

**Uncommon Port Usage**

A port appears only once or twice during the observation period.

These events may indicate:

- Command and Control callbacks.
- Unexpected services.
- Non-standard application behavior.
- Potentially malicious communication.

In large networks, recurring patterns are common, making unusual behavior particularly relevant for investigation.

### Don't Be Afraid to Ask for Help

Traffic analysis can involve large amounts of information and complex communication patterns.

After examining captures for extended periods, analysts may overlook important details.

Requesting assistance from another analyst can help identify evidence that was initially missed.

A second perspective may improve the quality of the investigation.

---

## Conclusion

Network traffic analysis is a dynamic process that requires technical knowledge, persistence, and an understanding of the network environment.

Although tools such as **tcpdump** and **Wireshark** are important, they are not the only resources available.

Other tools mentioned in the HTB section include:

- Snort.
- Security Onion.
- Firewalls.
- SIEM platforms.

These technologies can support investigations and provide additional visibility into network activity.

As analysts develop their understanding of protocols, communication patterns, and investigation workflows, they become better equipped to identify and respond to security threats.

---

## Key Takeaways

- **Descriptive Analysis** defines the problem, investigation scope, and relevant targets.
- **Diagnostic Analysis** captures and examines traffic to understand the causes of an event.
- **Predictive Analysis** evaluates findings, identifies trends, and supports documentation and reporting.
- **Prescriptive Analysis** determines appropriate actions and improvements based on investigation results.
- Network traffic analysis follows a **repeatable but iterative workflow**.
- Defining the scope before capturing traffic helps focus the investigation.
- Filtering reduces unnecessary traffic and highlights relevant communications.
- Wireshark filters such as `ftp-data` and `http.request.method == "GET"` can help identify file transfers and HTTP requests.
- Documenting timestamps, hosts, and packet numbers is essential for maintaining useful investigation records.
- Understanding the network environment helps distinguish normal communication from suspicious activity.
- **Capture placement** determines which traffic is visible to the analyst.
- **Persistence** is important because suspicious communication may occur infrequently.
- Analysts should start with common protocols and then investigate more specific communication.
- Repeated patterns, unusual connections, and unexpected host-to-host traffic may indicate suspicious activity.
- Collaboration with other analysts can improve the accuracy of an investigation.
