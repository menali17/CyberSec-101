
# Analysis with Wireshark

## Introduction

**Wireshark** is a free and open-source network traffic analyzer that provides a graphical interface for capturing and inspecting network packets.

Like tcpdump, Wireshark can capture live network traffic and analyze previously saved capture files.

However, its graphical interface provides more detailed and accessible packet inspection capabilities.

### Features and Capabilities

Wireshark supports:

- Deep packet inspection for hundreds of protocols.
- Graphical and terminal-based interfaces.
- Multiple operating systems.
- Live traffic capture.
- Reading and saving packet captures.
- Multiple network interface types.
- Protocol dissection and inspection.
- Decryption capabilities for supported protocols when the necessary information is available.

### Supported Network Technologies

Examples include:

- Ethernet
- IEEE 802.11 (Wi-Fi)
- PPP/HDLC
- ATM
- Bluetooth
- USB
- Token Ring
- Frame Relay
- FDDI

Wireshark provides several tools for capturing and examining traffic, including its graphical application and the terminal-based TShark.

---

## Requirements for Use

Wireshark is available for Windows, Linux, and other operating systems.

### Windows

The HTB material lists the following requirements:

- Universal C Runtime.
- Supported x86 or x86-64 processor.
- At least 500 MB of available RAM.
- At least 500 MB of available disk space.
- A modern display.
- A supported network interface.

The recommended display resolution in the material is 1280 × 1024 or higher.

Larger packet captures may require additional memory and storage.

### Linux

Wireshark is available for most Linux and UNIX-like distributions.

To check whether Wireshark is installed:

```bash
which wireshark
```

To install Wireshark on an apt-based Linux distribution:

```bash
sudo apt install wireshark
```

---

## TShark vs. Wireshark

Wireshark and TShark provide network packet capture and analysis capabilities.

Their primary difference is the interface through which they operate.

### Wireshark

Wireshark provides a graphical user interface (GUI).

It is useful for:

- Interactive packet inspection.
- Navigating protocol headers.
- Analyzing individual packet fields.
- Applying graphical filters.
- Examining packet contents.

### TShark

TShark is a terminal-based network protocol analyzer associated with Wireshark.

It provides many of the same protocol analysis capabilities through a command-line interface.

It is particularly useful on machines without a graphical desktop environment.

TShark can also send its output to other command-line tools.

### Comparison

| Feature | Wireshark | TShark |
|---|---|---|
| Interface | GUI | CLI |
| Packet capture | Yes | Yes |
| PCAP analysis | Yes | Yes |
| Protocol inspection | Yes | Yes |
| Terminal integration | Limited | Yes |
| Graphical packet navigation | Yes | No |

---

## Basic TShark Switches

| Switch | Description |
|---|---|
| `-D` | Lists available capture interfaces |
| `-L` | Lists supported link-layer types |
| `-i` | Selects a network interface |
| `-f` | Applies a capture filter |
| `-c` | Captures a specified number of packets |
| `-a` | Specifies an automatic stopping condition |
| `-r` | Reads an existing capture file |
| `-w` | Writes captured packets to a file |
| `-P` | Prints packet summaries while writing a capture |
| `-x` | Includes hexadecimal and ASCII packet output |
| `-h` | Displays help information |

### TShark Help

```bash
tshark -h
```

This displays information about the tool's available options.

### Verify Installation

```bash
which tshark
```

### List Network Interfaces

```bash
tshark -D
```

This displays the interfaces that TShark can use for capturing network traffic.

---

## Basic TShark Usage

### Selecting an Interface

To capture traffic from a specific interface:

```bash
sudo tshark -i eth0
```

The `-i` option selects the interface.

### Writing a Capture to a File

```bash
sudo tshark -i eth0 -w /tmp/test.pcap
```

This command:

1. Selects the `eth0` interface.
2. Starts capturing traffic.
3. Writes captured packets to `/tmp/test.pcap`.

### Reading an Existing Capture

```bash
tshark -r /tmp/test.pcap
```

The `-r` switch reads packets from an existing capture file.

### Capture Filters

TShark supports capture filters using BPF syntax.

Example:

```bash
sudo tshark -i eth0 -f "host 172.16.146.2"
```

The `-f` option applies a filter while capturing traffic.

Only traffic involving `172.16.146.2` matches the filter.

### Example Output

```text
172.16.146.2 → 172.16.146.1 DNS
Standard query A github.com

172.16.146.1 → 172.16.146.2 DNS
Standard query response A github.com

172.16.146.2 → 140.82.113.4 TCP
48256 → 443 [SYN]

140.82.113.4 → 172.16.146.2 TCP
443 → 48256 [SYN, ACK]

172.16.146.2 → 140.82.113.4 TCP
48256 → 443 [ACK]
```

This output demonstrates:

- A DNS request.
- A DNS response.
- TCP connection establishment.
- Communication with a server on port 443.

The subsequent packets in the HTB example include TLS traffic.

### Key Point

TShark and tcpdump share several command-line options and support BPF capture filters.

TShark also provides protocol decoding capabilities associated with Wireshark.

---

## Termshark

**Termshark** is a text-based user interface (TUI) application for examining packet captures.

It provides a Wireshark-like experience directly inside a terminal.

Unlike TShark's standard command-line output, Termshark provides an interactive interface for navigating packet data.

### Features

- Terminal-based packet inspection.
- Interactive packet navigation.
- Wireshark-like presentation.
- Support for packet capture filters.
- Inspection of network protocol details.

### Installation

The HTB material provides the Termshark GitHub repository:

https://github.com/gcla/termshark

Releases are available at:

https://github.com/gcla/termshark/releases

Termshark can be built from source or installed using a release package.

### Usage

Like tcpdump and TShark, Termshark can use network interfaces and filtering options.

The HTB material notes that the interface may not become visible until matching network traffic is detected.

---

## Wireshark GUI Walkthrough

Wireshark's graphical interface contains three main panes:

1. Packet List.
2. Packet Details.
3. Packet Bytes.

These panes work together to provide different levels of detail about the selected packet.

---

## 1. Packet List Pane

The Packet List pane displays a summary of captured packets.

Each row represents a packet.

### Common Columns

| Column | Description |
|---|---|
| No. | Packet number in the capture |
| Time | Packet timestamp |
| Source | Source address |
| Destination | Destination address |
| Protocol | Identified protocol |
| Information | Protocol-specific information |

### Example

```text
No.  Time      Source          Destination      Protocol
1    0.000000  192.168.1.10    192.168.1.1      DNS
2    0.025000  192.168.1.1     192.168.1.10     DNS
3    0.030000  192.168.1.10    93.184.215.14   TCP
```

The Packet List provides a high-level overview of the capture.

It helps analysts identify:

- Communication endpoints.
- Protocols.
- Packet order.
- Traffic patterns.
- Relevant packets for further inspection.

Wireshark allows columns to be added or removed to customize the information displayed.

---

## 2. Packet Details Pane

The Packet Details pane provides detailed information about the selected packet.

It separates the packet into its protocol layers.

For example:

```text
Frame
 └── Ethernet II
      └── Internet Protocol Version 4
           └── Transmission Control Protocol
                └── Hypertext Transfer Protocol
```

Each section contains information associated with a particular protocol.

### Examples of Information

Ethernet:

- Source MAC address.
- Destination MAC address.

IPv4:

- Source IP address.
- Destination IP address.
- Protocol information.

TCP:

- Source port.
- Destination port.
- Sequence number.
- Acknowledgment number.
- TCP flags.

Application protocols:

- Protocol-specific fields.
- Requests and responses.
- Application information.

### Encapsulation

Wireshark displays the encapsulated protocols from the lower layers toward the higher layers.

This allows analysts to inspect individual headers and understand how network traffic is structured.

---

## 3. Packet Bytes Pane

The Packet Bytes pane displays the raw contents of the selected packet.

Information is shown in:

- Hexadecimal.
- ASCII.

### Structure

The HTB material describes each row as containing:

1. A byte offset.
2. Sixteen hexadecimal bytes.
3. Sixteen ASCII characters.

Non-printable characters are represented by periods.

### Example

```text
0000  45 00 00 34 1c 46 40 00 40 06 ...  E..4.F@.@...
```

### Connection to Packet Details

When selecting a protocol field in the Packet Details pane, Wireshark highlights the corresponding bytes in the Packet Bytes pane.

This allows analysts to compare the decoded information with the actual packet contents.

### Why It Matters

The Packet Bytes pane is useful for:

- Inspecting raw network data.
- Understanding protocol headers.
- Identifying field offsets.
- Verifying protocol interpretation.

---

## Performing the First Capture in Wireshark

Wireshark allows users to capture network traffic directly through its graphical interface.

### Basic Capture Workflow

1. Open Wireshark.
2. Select the network interface.
3. Configure any necessary capture options.
4. Start the capture.
5. Observe incoming packets.
6. Stop the capture when finished.

Wireshark also provides options for applying filters during or after packet capture.

The HTB material notes that changing certain capture options restarts the active capture.

---

## The Wireshark Toolbar

Wireshark's toolbar provides access to common capture and analysis actions.

### Main Functions

- Start a capture.
- Stop a capture.
- Select interfaces.
- Open PCAP files.
- Save captures.
- Apply filters.
- Access analysis features.

### Saving a Capture

Captures can be saved using:

`File → Save`

Wireshark supports several capture file formats.

The HTB section focuses on saving packet captures as `.pcap` files.

---

## Pre-Capture and Post-Capture Filtering

Wireshark provides two main filtering mechanisms:

1. Capture Filters.
2. Display Filters.

Although both are used to reduce the visible network traffic, they operate at different stages.

---

## Capture Filters

Capture Filters are applied before starting a network capture.

They determine which packets are collected.

Wireshark uses **Berkeley Packet Filter (BPF)** syntax for capture filters.

This syntax is similar to the filtering expressions used by tcpdump.

### Example

```text
host 214.15.2.30
```

This filter captures traffic involving the specified host.

Packets that do not match the filter are excluded from the capture.

### Common Capture Filters

| Filter | Description |
|---|---|
| `host x.x.x.x` | Traffic involving a particular host |
| `net x.x.x.x/24` | Traffic involving a specified network |
| `src net x.x.x.x/24` | Traffic originating from a network |
| `dst net x.x.x.x/24` | Traffic destined for a network |
| `port 80` | Traffic involving port 80 |
| `not port 80` | Traffic excluding port 80 |
| `port 80 and port 443` | Requires both port conditions |
| `portrange 1000-2000` | Traffic involving the specified port range |
| `ip` | IPv4 traffic |
| `ether` | Ethernet traffic |
| `tcp` | TCP traffic |
| `broadcast` | Broadcast traffic |
| `multicast` | Multicast traffic |
| `unicast` | Unicast traffic |

### Applying a Capture Filter

According to the HTB material:

1. Open the Capture menu.
2. Select Capture Filters to view or edit saved filters.
3. Open Capture Options.
4. Select or enter a capture filter for the chosen interface.
5. Start the capture.

### Advantages

Capture filters can:

- Reduce the amount of collected traffic.
- Reduce disk usage.
- Help investigate specific communications.
- Focus captures on particular hosts or services.

### Important Limitation

Traffic that does not match the capture filter is not collected.

This means that potentially relevant packets may be unavailable for later analysis.

---

## Display Filters

Display Filters are applied to packets that have already been captured.

They determine which packets are shown in the Wireshark interface.

Unlike Capture Filters, Display Filters use Wireshark's own filtering syntax.

### Example

```text
ip.addr == 172.16.146.2
```

This displays packets involving the specified IPv4 address.

### Common Display Filters

| Filter | Description |
|---|---|
| `ip.addr == x.x.x.x` | Traffic involving a specific IPv4 address |
| `ip.addr == x.x.x.x/24` | Traffic involving an IPv4 network |
| `ip.src == x.x.x.x` | Packets originating from an IP address |
| `ip.dst == x.x.x.x` | Packets destined for an IP address |
| `dns` | DNS traffic |
| `tcp` | TCP traffic |
| `ftp` | FTP traffic |
| `arp` | ARP traffic |
| `ip` | IPv4 traffic |
| `tcp.port == 443` | TCP traffic involving port 443 |
| `and` | Requires both conditions |
| `or` | Matches either condition |
| `not` | Excludes a matching condition |

### Filtering by Protocol

```text
dns
```

Displays DNS packets.

```text
tcp
```

Displays TCP packets.

```text
arp
```

Displays ARP packets.

### Filtering by IP Address

```text
ip.addr == 192.168.1.10
```

Displays packets where the specified address appears as source or destination.

To match only the source:

```text
ip.src == 192.168.1.10
```

To match only the destination:

```text
ip.dst == 192.168.1.10
```

### Filtering by TCP Port

```text
tcp.port == 443
```

Displays TCP traffic involving port 443.

### Combining Conditions

```text
ip.addr == 192.168.1.10 and tcp.port == 443
```

Both conditions must match.

```text
dns or http
```

Displays packets recognized as DNS or HTTP.

```text
not tcp
```

Excludes TCP packets from the display.

### Applying Display Filters

The HTB section describes the following procedure:

1. Locate the Display Filter field in Wireshark.
2. Enter the filtering expression.
3. Apply the filter.

A valid expression is indicated by a green filter field.

### Important Behavior

Applying a Display Filter does not remove nonmatching packets from the capture file.

Instead, Wireshark processes the existing capture and changes which packets are displayed.

Removing the filter allows the other packets to become visible again.

---

## Capture Filters vs. Display Filters

| Characteristic | Capture Filter | Display Filter |
|---|---|---|
| Applied | Before capture | During or after capture |
| Syntax | BPF | Wireshark Display Filter |
| Purpose | Select packets to collect | Select packets to display |
| Reduces captured data | Yes | No |
| Preserves nonmatching packets | No | Yes, if already captured |
| Can filter protocol fields | More limited | More detailed |

### Example - Filtering a Host

Capture Filter:

```text
host 192.168.1.10
```

Display Filter:

```text
ip.addr == 192.168.1.10
```

Both focus on the same host, but at different stages of traffic analysis.

### Example - Filtering TCP Port 443

Capture Filter:

```text
tcp port 443
```

Display Filter:

```text
tcp.port == 443
```

### Key Concept

Capture Filters determine what traffic is collected.

Display Filters determine what traffic is shown.

---

## Performance Considerations

The HTB material notes that Wireshark can have difficulty processing very large capture files.

As the number of packets increases, applying display filters and performing analysis may require more processing time.

In some cases, it may be useful to divide a large capture into smaller files before performing additional analysis.

---

## Ports vs. Protocols

An important concept emphasized in the HTB section is that filtering by a port is not the same as filtering by an application protocol.

For example:

```text
tcp.port == 80
```

This displays TCP traffic involving port 80.

However, it does not necessarily mean that every packet contains HTTP.

A service can be configured to use a different port from its conventional one.

Similarly:

```text
http
```

This filter displays packets recognized by Wireshark as HTTP.

### Key Difference

- Port filters match numerical port values.
- Protocol filters match traffic based on protocol decoding.

### Example

A web server might operate on port 8080 instead of port 80.

Therefore, filtering only port 80 could miss relevant web traffic.

**Important:** Port numbers provide useful clues, but protocol identification requires examining the actual communication.

---

## Command and Filter Reference

### TShark Commands

| Purpose | Command |
|---|---|
| Check installation | `which tshark` |
| Display help | `tshark -h` |
| List interfaces | `tshark -D` |
| Capture from interface | `tshark -i eth0` |
| Save capture | `tshark -i eth0 -w capture.pcap` |
| Read capture | `tshark -r capture.pcap` |
| Capture by host | `tshark -i eth0 -f "host 172.16.146.2"` |

### Wireshark Capture Filters

```text
host 192.168.1.10
net 192.168.1.0/24
src net 192.168.1.0/24
dst net 192.168.1.0/24
tcp port 443
not port 80
portrange 1000-2000
tcp
```

### Wireshark Display Filters

```text
ip.addr == 192.168.1.10
ip.src == 192.168.1.10
ip.dst == 192.168.1.10
tcp.port == 443
dns
tcp
arp
ip.addr == 192.168.1.10 and tcp.port == 443
dns or http
not tcp
```

---

## Key Takeaways

- Wireshark is a graphical packet analyzer used for network capture and protocol inspection.
- TShark provides similar packet analysis capabilities through the terminal.
- Termshark provides an interactive terminal-based interface for packet analysis.
- Wireshark contains three primary panes: Packet List, Packet Details, and Packet Bytes.
- The Packet List pane provides a summary of captured packets.
- The Packet Details pane displays protocol headers and individual fields.
- The Packet Bytes pane displays the raw packet contents in hexadecimal and ASCII.
- TShark uses `-i` to select an interface and `-w` to save captures.
- Capture Filters use BPF syntax and are applied before capture.
- Display Filters use Wireshark's filtering syntax and operate on captured traffic.
- Capture Filters can prevent nonmatching packets from being collected.
- Display Filters do not modify the original capture data.
- Port-based filtering is not equivalent to application protocol identification.
- Understanding the difference between Capture and Display Filters is essential for effective network traffic analysis.
