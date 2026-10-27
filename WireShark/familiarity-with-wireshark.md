
# Familiarity With Wireshark

## Introduction

This lab focuses on gaining practical experience with **Wireshark** and its graphical interface.

The objective is to become familiar with:

- Navigating the Wireshark GUI.
- Selecting network interfaces.
- Starting and stopping packet captures.
- Creating and applying capture filters.
- Observing network traffic.
- Identifying network connections and protocols.
- Examining plaintext and encrypted communications.

---

## Lab Scenario

A user reports unusually slow web browsing on their laptop.

According to the user:

- Web pages take longer than expected to load.
- The network appears congested.
- The computer seems to establish many different connections.

Our task is to investigate the laptop's network activity.

To accomplish this, we will connect the laptop to the network and capture traffic while browsing the web.

Since we are investigating a specific device, we should filter out traffic that does not originate from or have the laptop as its destination.

---

## Task 1 - Validate Wireshark Installation

The first task is to verify that Wireshark is installed and become familiar with its graphical interface.

### Objectives

- Open Wireshark.
- Explore the available menus.
- Locate the capture options.
- Understand the toolbar.
- Become familiar with the main interface panes.

### Main Wireshark Panes

| Pane | Description |
|---|---|
| Packet List | Displays summaries of captured packets |
| Packet Details | Displays detailed protocol information |
| Packet Bytes | Displays packet contents in hexadecimal and ASCII |

### Capture Menu

The Capture menu provides access to the options required to configure and start packet captures.

Understanding the location of these options is important for performing the following tasks.

---

## Task 2 - Select a Network Interface

Before capturing traffic, select the appropriate network interface.

Examples include:

- `eth0` - Ethernet interface.
- Wi-Fi interface - Wireless network connection.

### Procedure

1. Open Wireshark.
2. Locate the available network interfaces.
3. Select the active network interface.

Alternatively:

1. Open the **Capture** menu.
2. Select **Options**.
3. Choose the appropriate network interface.

The selected interface determines where Wireshark captures network packets.

---

## Task 3 - Create a Capture Filter

The next task is to create a capture filter that restricts traffic to the laptop being investigated.

### Objective

Capture only packets that:

- Originate from the laptop.
- Are destined for the laptop.

### Capture Filter Syntax

```text
host X.X.X.X
```

Replace `X.X.X.X` with the laptop's IP address.

### Example

```text
host 192.168.1.10
```

This filter matches traffic where `192.168.1.10` appears as the source or destination.

### Applying the Filter

1. Open **Capture Options**.
2. Locate the Capture Filter field.
3. Enter the filter expression.
4. Start the capture.

### Important Concept

Wireshark Capture Filters use **BPF syntax**, similar to tcpdump.

Capture filters determine which packets are collected.

Traffic that does not match the filter is excluded from the capture.

---

## Task 4 - Generate Network Traffic

After starting the capture, generate traffic by visiting websites.

### Websites Used in the Lab

```text
pepsi.com
http://apache.org
```

### Procedure

1. Start the Wireshark capture.
2. Open a web browser.
3. Navigate to `pepsi.com`.
4. Navigate to `http://apache.org`.
5. Observe the captured traffic in Wireshark.
6. Stop the capture after the pages have loaded.

### Stopping the Capture

Click the red **Stop** button in Wireshark's toolbar.

The captured packets can now be examined.

---

## Task 5 - Analyze the Capture Results

After collecting network traffic, examine the captured packets.

The lab focuses on three main questions.

### Question 1 - Are Multiple Sessions Established?

Determine whether multiple network conversations appear between the laptop and the web servers.

A web browser may establish several connections while loading websites.

These connections can be identified by examining:

- Source IP addresses.
- Destination IP addresses.
- Source ports.
- Destination ports.
- TCP connection establishment.

### Identifying TCP Connections

A standard TCP connection begins with the Three-way Handshake:

```text
SYN
SYN, ACK
ACK
```

Examining these packets helps identify the establishment of individual TCP connections.

---

### Question 2 - What Application Protocols Appear?

According to the HTB lab, the main protocols expected are:

- DNS
- HTTP
- HTTPS

### DNS

DNS is used to resolve domain names into IP addresses.

When a browser visits a website, DNS queries may be performed to locate the corresponding servers.

### HTTP

HTTP is used for communication between web clients and servers.

The HTB exercise uses the HTTP version of the Apache website to demonstrate plaintext communication.

### HTTPS

HTTPS provides encrypted HTTP communication.

The lab uses Pepsi's website as an example of HTTPS traffic.

### Expected Traffic

| Protocol | Purpose |
|---|---|
| DNS | Resolves domain names |
| HTTP | Transfers web content without HTTP-layer encryption |
| HTTPS | Transfers web content protected by TLS |

Other protocols may also appear depending on the network environment.

---

### Question 3 - Can We Read Information in Cleartext?

One of the objectives of the lab is to compare HTTP and HTTPS traffic.

### HTTP Traffic

HTTP does not encrypt application data.

Therefore, captured HTTP traffic may reveal readable information.

For example, analysts may be able to inspect:

- HTTP requests.
- HTTP responses.
- Request paths.
- HTTP headers.
- Other plaintext application data.

### HTTPS Traffic

HTTPS protects HTTP communication using encryption.

Although packets can still be captured, the protected HTTP contents are not directly readable without decryption.

### HTTP vs. HTTPS

| Characteristic | HTTP | HTTPS |
|---|---|---|
| Typical Port | 80 | 443 |
| HTTP Content Encrypted | No | Yes |
| Plaintext Inspection | Possible | Normally not possible |
| Uses TLS | No | Yes |

### Lab Observation

The HTB material demonstrates that HTTP traffic can expose readable information, while HTTPS traffic appears different because its contents are encrypted.

---

## Experimenting With Wireshark Filters

After completing the capture, additional filters can be used to examine the traffic more closely.

### Capture Filter Example

```text
host 192.168.1.10
```

Captures traffic involving the specified device.

### Display Filter Examples

Show DNS packets:

```text
dns
```

Show HTTP packets:

```text
http
```

Show TCP packets:

```text
tcp
```

Show traffic involving a specific IP address:

```text
ip.addr == 192.168.1.10
```

### Important Difference

Capture Filters determine which packets are collected.

Display Filters determine which captured packets are shown.

Applying a Display Filter does not remove the other packets from the original capture.

---

## Additional Practice Resources

The HTB material recommends practicing with additional PCAP files.

### Wireshark Sample Captures

https://wiki.wireshark.org/SampleCaptures

These files can be used to examine different network protocols and traffic patterns.

The resource includes examples involving:

- Standard network protocols.
- Industrial control systems.
- SCADA communications.
- Malware-related network traffic.

---

## Lab Summary

By completing this lab, we should be able to:

1. Open and navigate Wireshark.
2. Identify the main interface panes.
3. Select a network interface.
4. Configure a capture filter.
5. Start and stop packet captures.
6. Generate traffic for analysis.
7. Identify multiple network conversations.
8. Recognize DNS, HTTP, and HTTPS traffic.
9. Distinguish plaintext from encrypted communication.
10. Use filters to focus network investigations.

---

## Key Takeaways

- Wireshark provides a graphical interface for capturing and analyzing network packets.
- The appropriate network interface must be selected before starting a capture.
- Capture Filters use BPF syntax.
- The `host` filter captures traffic involving a particular IP address in either direction.
- Visiting websites generates different types of network traffic.
- DNS is used to resolve domain names.
- HTTP traffic can contain readable application data.
- HTTPS uses encryption to protect HTTP content.
- Multiple network conversations can be identified using source and destination addresses and ports.
- Display Filters can be used to investigate specific protocols without modifying the original capture.
- Repeated practice helps improve familiarity with Wireshark's interface and analysis capabilities.
