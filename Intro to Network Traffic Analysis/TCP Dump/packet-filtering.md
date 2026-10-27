
# Tcpdump Packet Filtering

## Introduction

**Tcpdump** provides packet filtering capabilities that allow analysts to examine specific network communications without having to inspect every captured packet.

Packet filters help identify traffic based on characteristics such as:

- Source and destination IP addresses.
- Network ranges.
- Protocols.
- Port numbers.
- Packet sizes.
- TCP header fields.
- Logical conditions.

Using filters allows us to reduce unnecessary traffic and focus on the information relevant to an investigation.

Filters can also reduce the amount of data written to a capture file and help the capture system process network traffic more efficiently.

---

## Filtering and Advanced Syntax Options

Tcpdump uses filtering expressions to determine which packets should be captured or displayed.

Filters can be used individually or combined to create more specific conditions.

### Common Tcpdump Filters

| Filter | Description |
|---|---|
| `host` | Matches traffic involving a specified host, in either direction |
| `src` | Specifies the source address, network, or port |
| `dst` | Specifies the destination address, network, or port |
| `net` | Matches traffic involving a specified network |
| `proto` | Matches a specific protocol number |
| `port` | Matches a source or destination port |
| `portrange` | Matches ports within a specified range |
| `less` | Matches packets smaller than a specified size |
| `greater` | Matches packets larger than a specified size |
| `and` / `&&` | Requires both conditions to match |
| `or` / `||` | Requires at least one condition to match |
| `not` / `!` | Excludes traffic matching a condition |

These filters can be applied when capturing live network traffic or reading existing PCAP files.

---

## Host Filter

The `host` filter displays traffic involving a specific IP address.

It is **bidirectional**, meaning that the specified host can appear as either the source or destination.

### Syntax

```bash
host <IP>
```

### Example

```bash
sudo tcpdump -i eth0 host 172.16.146.2
```

This command captures traffic involving `172.16.146.2`.

Example output:

```text
172.16.146.2.55606 > 172.67.1.1.https:
Flags [P.], length 92

172.67.1.1.https > 172.16.146.2.55606:
Flags [.], ack 92, length 0
```

Notice that the specified host appears in both directions.

This filter is useful for:

- Monitoring a particular workstation or server.
- Identifying the hosts it communicates with.
- Examining inbound and outbound connections.
- Investigating potentially suspicious communications.

**Important:** The `host` filter matches both source and destination addresses.

---

## Source and Destination Filters

The `src` and `dst` modifiers allow us to filter traffic based on the direction of communication.

Unlike `host`, which matches both directions, these modifiers restrict the matching to a specific endpoint.

### Syntax

```bash
src host <IP>
dst host <IP>
```

### Source Host Filter

```bash
sudo tcpdump -i eth0 src host 172.16.146.2
```

This command displays packets originating from `172.16.146.2`.

Example:

```text
172.16.146.2.48766 > 52.31.199.148.443
172.16.146.2.55606 > 172.67.1.1.443
172.16.146.2.36424 > 172.16.146.1.53
```

Only packets sent by the specified host are displayed.

### Destination Host Filter

```bash
sudo tcpdump -i eth0 dst host 172.16.146.2
```

This command displays packets whose destination is `172.16.146.2`.

### Comparison

| Filter | Matching Traffic |
|---|---|
| `host 172.16.146.2` | Source or destination |
| `src host 172.16.146.2` | Source only |
| `dst host 172.16.146.2` | Destination only |

### Filtering by Source Port

The same modifiers can be used with ports.

```bash
sudo tcpdump -i eth0 tcp src port 80
```

This command captures TCP packets whose **source port is 80**.

For example:

```text
65.208.228.223.http > 145.254.160.237.3372
```

This indicates traffic sent from the server's HTTP port toward the client.

Because we are filtering by source port, we only see the server-to-client side of this communication.

**Important:** Directional filters are useful when we want to examine only one direction of a network conversation.

---

## Network Filter

The `net` filter allows us to capture traffic involving an entire network instead of a single host.

It supports CIDR notation.

### Syntax

```bash
net <network>/<prefix>
```

### Example

```bash
sudo tcpdump -i eth0 net 172.16.146.0/24
```

This matches traffic with a source or destination IPv4 address in the specified network.

### Combining Network and Destination Filters

```bash
sudo tcpdump -i eth0 dst net 172.16.146.0/24
```

This command captures packets destined for the `172.16.146.0/24` network.

Example:

```text
64.233.177.103.443 > 172.16.146.2.36050
```

The destination address belongs to the specified network.

### Source Network Filter

```bash
sudo tcpdump -i eth0 src net 172.16.146.0/24
```

This captures packets originating from addresses within the specified network.

**Important:** Network filters are useful when investigating communication involving an entire subnet rather than individual hosts.

---

## Protocol Filters

Tcpdump can filter packets according to their protocol.

Common examples include:

- TCP
- UDP
- ICMP

Protocols can be selected using their names or, in appropriate filter expressions, their numerical identifiers.

### Filtering by Protocol Name

```bash
sudo tcpdump -i eth0 udp
```

This captures UDP packets.

Example output:

```text
145.254.160.237.3009 > 145.253.2.203.domain:
35+ A? pagead2.googlesyndication.com.

145.253.2.203.domain > 145.254.160.237.3009:
35 4/0/0 CNAME pagead2.google.com.
```

The output shows DNS communication over UDP.

### Filtering by Protocol Number

The HTB material demonstrates an equivalent filter using the protocol number.

```bash
sudo tcpdump -i eth0 proto 17
```

This matches IPv4 packets whose protocol field identifies UDP.

### Common Protocol Numbers

| Protocol | Number |
|---|---|
| ICMP | 1 |
| TCP | 6 |
| UDP | 17 |

### Additional Examples

Capture TCP traffic:

```bash
sudo tcpdump -i eth0 tcp
```

Capture ICMP traffic:

```bash
sudo tcpdump -i eth0 icmp
```

Capture UDP traffic using its protocol number:

```bash
sudo tcpdump -i eth0 proto 17
```

**Important:** Protocol filtering helps isolate specific types of network communication.

---

## Port Filter

The `port` filter selects packets that use a specified source or destination port.

Like the `host` filter, it is bidirectional.

### Syntax

```bash
port <port_number>
```

### Example

```bash
sudo tcpdump -i eth0 port 443
```

This captures traffic involving port `443`, regardless of whether it appears as the source or destination port.

The filter does not restrict the transport protocol unless one is specified.

### Combining Protocol and Port

To capture TCP traffic involving port 443:

```bash
sudo tcpdump -i eth0 tcp port 443
```

This limits the capture to TCP packets associated with port `443`.

### DNS Example

DNS commonly uses both TCP and UDP port 53.

To capture all traffic involving that port:

```bash
sudo tcpdump -i eth0 port 53
```

To capture only UDP traffic involving port 53:

```bash
sudo tcpdump -i eth0 udp port 53
```

To capture only TCP traffic involving port 53:

```bash
sudo tcpdump -i eth0 tcp port 53
```

### Understanding the Difference

| Filter | Result |
|---|---|
| `port 53` | TCP or UDP traffic involving port 53 |
| `udp port 53` | UDP traffic involving port 53 |
| `tcp port 53` | TCP traffic involving port 53 |
| `tcp port 443` | TCP traffic involving port 443 |

**Important:** Port numbers help identify likely services, but they do not guarantee which application protocol is actually being used.

---

## Port Range Filter

The `portrange` filter allows us to select packets using ports within a specified range.

### Syntax

```bash
portrange <start>-<end>
```

### Example

```bash
sudo tcpdump -i eth0 portrange 0-1024
```

This captures traffic where a source or destination port falls within the specified range.

The HTB example shows communication involving services such as:

- DNS.
- HTTP.

### Why Use Port Ranges?

Port range filtering is useful when:

- Investigating multiple services.
- Monitoring common service ports.
- Examining traffic associated with unexpected ports.
- Identifying communication outside an expected service configuration.

For example, if a web server normally uses TCP ports 80 and 443 but begins communicating over unexpected ports, additional investigation may be necessary.

**Important:** Unusual port usage is not automatically malicious. It should be evaluated against the environment's expected behavior.

---

## Packet Size Filters - Less and Greater

Tcpdump allows us to filter packets according to their size.

The two modifiers are:

- `less`
- `greater`

### Less Filter

The `less` filter matches packets smaller than the specified size.

### Syntax

```bash
less <size>
```

### Example

```bash
sudo tcpdump -i eth0 less 64
```

This captures packets smaller than 64 bytes.

The HTB example shows that many matching packets are associated with:

- TCP SYN.
- TCP FIN.
- TCP ACK.
- Keepalive traffic.

These packets frequently contain little or no application payload.

### Greater Filter

The `greater` filter matches packets larger than a specified size.

### Syntax

```bash
greater <size>
```

### Example

```bash
sudo tcpdump -i eth0 greater 500
```

This captures packets larger than 500 bytes.

### Why Filter by Packet Size?

Packet size filters can help reduce irrelevant traffic during an investigation.

For example, if we are examining communications involving file transfers, we may want to focus on larger packets carrying application data.

Filtering by packet size can help remove some of the smaller packets associated with connection management.

### Example from HTB

The HTB section demonstrates a capture containing Telnet traffic.

The output includes readable information such as:

```text
Warning: no Kerberos tickets issued.

OpenBSD 2.6-beta (OOF) #4

Welcome to OpenBSD:
The proactively secure Unix-like operating system.
```

The example demonstrates how packet size filters can help identify packets containing larger amounts of application data.

**Important:** Filtering by packet size alone does not confirm that a file transfer is occurring.

---

## Logical Operators

Tcpdump supports logical operators that allow several filtering conditions to be combined.

The main operators are:

- AND
- OR
- NOT

### AND Operator

The `and` operator requires both conditions to match.

### Syntax

```bash
<condition_1> and <condition_2>
```

### Example

```bash
sudo tcpdump -i eth0 host 192.168.0.1 and port 23
```

This captures packets that:

1. Involve the host `192.168.0.1`.
2. Use port `23` as their source or destination port.

Both conditions must be satisfied.

Since port 23 is commonly associated with Telnet, the capture may show Telnet communication involving the specified host.

### Another Example

```bash
sudo tcpdump -i eth0 host 10.12.1.122 and tcp port 80
```

This captures TCP port 80 traffic involving `10.12.1.122`.

### OR Operator

The `or` operator matches packets satisfying either condition.

### Syntax

```bash
<condition_1> or <condition_2>
```

### Example

```bash
sudo tcpdump -r sus.pcap icmp or host 172.16.146.1
```

This displays:

- ICMP traffic.
- Traffic involving `172.16.146.1`.

A packet only needs to satisfy one of the conditions.

The HTB output includes ICMP echo requests, echo replies, and ARP communication involving the specified host.

### NOT Operator

The `not` operator excludes packets matching a condition.

### Syntax

```bash
not <condition>
```

### Example

```bash
sudo tcpdump -r sus.pcap not icmp
```

This displays all traffic that does not match the ICMP filter.

The HTB example shows that removing ICMP traffic reveals other communications, including:

- ARP.
- DNS.
- HTTPS.

### Logical Operator Comparison

| Operator | Behavior | Example |
|---|---|---|
| `and` | Both conditions must match | `host 192.168.0.1 and port 23` |
| `or` | Either condition can match | `icmp or host 172.16.146.1` |
| `not` | Excludes matching traffic | `not icmp` |

These operators can be used to create more specific filters during an investigation.

---

## Pre-Capture Filters vs. Post-Capture Processing

Tcpdump filters can be applied at different stages of the capture and analysis process.

### Pre-Capture Filtering

Pre-capture filters are applied while collecting live network traffic.

Example:

```bash
sudo tcpdump -i eth0 -w capture.pcap 'tcp port 443'
```

Only packets matching the filter are saved to the capture file.

### Advantages

- Reduces the amount of traffic collected.
- Requires less storage space.
- Helps focus on a specific investigation objective.
- Can reduce the amount of data processed during capture.

### Disadvantages

Packets that do not match the filter are not saved.

This means potentially useful traffic may be unavailable during later analysis.

**Important:** Capture filters should be used carefully when the full context of an incident may be needed.

### Post-Capture Filtering

Post-capture filtering is performed when reading an existing PCAP file.

Example:

```bash
tcpdump -r capture.pcap 'tcp port 443'
```

This displays only matching packets from the existing file.

Unlike pre-capture filtering, it does not permanently remove the other packets from the original capture.

### Advantages

- Preserves the original capture.
- Allows different filters to be applied.
- Supports repeated analysis.
- Helps maintain the original evidence.

### Comparison

| Characteristic | Pre-Capture | Post-Capture |
|---|---|---|
| Applied During | Live capture | PCAP analysis |
| Reduces Saved Data | Yes | No |
| Removes Nonmatching Packets from New Capture | Yes | No |
| Changes Existing PCAP | Not applicable | No |
| Allows Repeated Filtering | Only on captured data | Yes |

**Key Concept:** Filtering during capture determines what is collected. Filtering during analysis determines what is displayed from the data already collected.

---

## Interpreting Tips and Tricks

Tcpdump offers several additional switches that can make packet analysis easier.

### Displaying Absolute Sequence Numbers

By default, tcpdump generally uses relative TCP sequence numbers when displaying established connections.

The `-S` switch displays absolute sequence numbers.

```bash
sudo tcpdump -i eth0 -S
```

Relative numbers are easier to read, while absolute numbers can be useful when comparing packet information with other tools or logs.

### Additional Output Switches

| Switch | Description |
|---|---|
| `-v` | Increases verbosity |
| `-X` | Displays hexadecimal and ASCII |
| `-e` | Includes Ethernet header information |
| `-c` | Limits the number of captured packets |
| `-n` | Disables address-name resolution |
| `-s` | Defines snapshot length |
| `-S` | Displays absolute TCP sequence numbers |
| `-q` | Reduces protocol information |
| `-A` | Displays packet contents in ASCII |
| `-l` | Enables line-buffered output |

---

## ASCII Output with -A

The `-A` switch displays packet contents in ASCII without the hexadecimal representation.

### Example

```bash
sudo tcpdump -Ar telnet.pcap
```

This reads the `telnet.pcap` file and displays its packet contents in ASCII.

The HTB example reveals readable Telnet session information, including:

```text
Last login: Sat Nov 27 20:11:43

Warning: no Kerberos tickets issued.

Welcome to OpenBSD:
The proactively secure Unix-like operating system.
```

This is particularly useful when examining plaintext communication.

### Difference Between -A and -X

| Switch | Output |
|---|---|
| `-A` | ASCII |
| `-X` | Hexadecimal and ASCII |

---

## Piping Tcpdump Output to Grep

The `-l` switch enables line-buffered output.

This is useful when sending tcpdump output to another command through a pipe.

### Example

```bash
sudo tcpdump -Ar http.cap -l | grep 'mailto:*'
```

### Command Breakdown

| Component | Description |
|---|---|
| `tcpdump` | Reads and interprets captured packets |
| `-A` | Displays ASCII contents |
| `-r http.cap` | Reads the specified capture file |
| `-l` | Enables line buffering |
| `\|` | Sends the output to another command |
| `grep 'mailto:*'` | Searches for matching text |

The HTB example searches captured HTTP content for text associated with email links.

Example output:

```text
<a href="mailto:ethereal-web[AT]ethereal.com">
<a href="mailto:free-support[AT]thewrittenword.com">
<a href="mailto:ethereal-users[AT]ethereal.com">
```

### Why Use Pipes?

Piping tcpdump output into tools such as `grep` allows analysts to search for specific patterns within readable traffic.

This can help locate:

- Email addresses.
- Specific strings.
- Naming conventions.
- Other readable information in captured traffic.

**Important:** The `-l` switch controls line buffering. The pipe operator `|` is what connects the output to another command.

---

## Filtering TCP Flags

Tcpdump can inspect individual fields within protocol headers.

This allows analysts to filter for packets containing specific TCP flags.

The HTB section demonstrates how to identify TCP segments with the SYN flag set.

### TCP SYN Filter

```bash
sudo tcpdump -i eth0 'tcp[13] & 2 != 0'
```

### Understanding the Expression

| Component | Meaning |
|---|---|
| `tcp` | Examines TCP packets |
| `[13]` | Accesses the TCP flags byte at offset 13 |
| `&` | Performs a bitwise AND operation |
| `2` | Bitmask identifying the SYN flag |
| `!= 0` | Checks whether the selected bit is set |

### How It Works

The expression checks whether the SYN flag is present in the TCP header.

The TCP flags field contains bits representing different control flags.

The SYN flag corresponds to the bitmask:

```text
0x02
```

If the SYN bit is set, the packet matches the filter.

### Example Output

```text
172.16.146.2.56244 > 172.67.1.1.https:
Flags [S]

172.67.1.1.https > 172.16.146.2.56244:
Flags [S.]

172.16.146.2.60202 > 23.54.168.81.https:
Flags [S]
```

Notice that both `SYN` and `SYN, ACK` packets appear.

This happens because both types of segments have the SYN flag set.

### Why Filter SYN Packets?

Identifying TCP SYN packets allows analysts to examine connection establishment attempts.

This can help recognize patterns involving:

- New TCP connections.
- Repeated connection attempts.
- Communications with multiple destinations.
- Potential port scanning activity.

**Important:** The filter matches any TCP segment with the SYN bit set, including SYN/ACK packets.

---

## Protocol RFC References

The HTB section provides references for studying protocol structures and behavior in greater detail.

| Protocol | Reference | Description |
|---|---|---|
| IPv4 | RFC 791 | Internet Protocol |
| ICMP | RFC 792 | Internet Control Message Protocol |
| TCP | RFC 793 | Transmission Control Protocol |
| UDP | RFC 768 | User Datagram Protocol |

These references describe protocol structures, header fields, and behavior.

Understanding protocol headers is useful when creating more advanced tcpdump filters.

---

## Command Reference

| Purpose | Command |
|---|---|
| Filter by host | `tcpdump -i eth0 host 172.16.146.2` |
| Filter by source host | `tcpdump -i eth0 src host 172.16.146.2` |
| Filter by destination host | `tcpdump -i eth0 dst host 172.16.146.2` |
| Filter by network | `tcpdump -i eth0 net 172.16.146.0/24` |
| Filter by destination network | `tcpdump -i eth0 dst net 172.16.146.0/24` |
| Filter UDP | `tcpdump -i eth0 udp` |
| Filter by protocol number | `tcpdump -i eth0 proto 17` |
| Filter by port | `tcpdump -i eth0 port 443` |
| Filter TCP port | `tcpdump -i eth0 tcp port 443` |
| Filter a port range | `tcpdump -i eth0 portrange 0-1024` |
| Filter smaller packets | `tcpdump -i eth0 less 64` |
| Filter larger packets | `tcpdump -i eth0 greater 500` |
| Combine with AND | `tcpdump -i eth0 'host 192.168.0.1 and port 23'` |
| Combine with OR | `tcpdump -i eth0 'icmp or host 172.16.146.1'` |
| Exclude ICMP | `tcpdump -i eth0 'not icmp'` |
| Display ASCII | `tcpdump -Ar telnet.pcap` |
| Enable line buffering | `tcpdump -l` |
| Filter TCP SYN | `tcpdump -i eth0 'tcp[13] & 2 != 0'` |

---

## Key Takeaways

- Tcpdump supports filtering based on hosts, networks, ports, protocols, and packet sizes.
- The `host` and `port` filters match traffic in both directions.
- The `src` and `dst` modifiers restrict filters to a specific direction.
- The `net` filter allows entire network ranges to be selected.
- Protocols can be filtered by their names or protocol numbers.
- `portrange` selects traffic involving a range of ports.
- `less` and `greater` filter packets according to their sizes.
- `and` requires both conditions to match.
- `or` requires at least one condition to match.
- `not` excludes packets matching a condition.
- Pre-capture filtering affects which packets are collected.
- Post-capture filtering displays matching packets without changing the original PCAP.
- `-A` displays packet contents in ASCII.
- `-l` enables line-buffered output for piping data to other commands.
- Tcpdump can inspect TCP header fields and filter on specific flags.
- The expression `tcp[13] & 2 != 0` identifies TCP segments with the SYN flag set.
- Understanding protocol structures helps analysts construct more advanced packet filters.
