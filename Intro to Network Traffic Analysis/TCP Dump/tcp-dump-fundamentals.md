
# Tcpdump Fundamentals

## Introduction

**Tcpdump** is a command-line packet sniffer used to capture and analyze network traffic from network interfaces or previously saved capture files.

It is commonly available on Unix-like operating systems, including:

- Linux
- BSD
- Solaris
- AIX

Tcpdump does not require a graphical interface, making it useful for analyzing traffic directly from a terminal or through remote connections such as SSH.

### How Tcpdump Works

Tcpdump uses packet capture libraries such as **libpcap** to collect traffic from network interfaces.

A network interface can be configured in **promiscuous mode** to receive frames that are visible to the interface, including frames not addressed to its own MAC address.

This allows tcpdump to inspect network traffic available at the capture point.

### Requirements

Capturing live network traffic generally requires elevated privileges because the application needs access to network interfaces.

For this reason, tcpdump is commonly executed using `sudo`.

### Checking the Installation

To verify whether tcpdump is installed:

```bash
which tcpdump
```

The command returns the executable's location if it is available.

A common installation path is:

```text
/usr/sbin/tcpdump
```

If tcpdump is not installed, it can be installed on Debian-based distributions using:

```bash
sudo apt install tcpdump
```

### Checking the Version

To display the installed tcpdump version:

```bash
sudo tcpdump --version
```

This command displays information such as:

- Tcpdump version.
- Libpcap version.
- OpenSSL version, when applicable.

### Tcpdump on Windows

The HTB material mentions **WinDump**, a Windows port of tcpdump whose development and support have ceased.

An alternative is to run a Linux distribution through **Windows Subsystem for Linux (WSL)**.

This provides access to Linux command-line tools, including tcpdump, although packet capture capabilities depend on the environment.

---

## Traffic Captures with Tcpdump

Tcpdump provides numerous command-line options that control how packets are captured, displayed, and stored.

These options can be combined to customize the output.

### Basic Capture Options

| Switch | Description |
|---|---|
| `-D` | Lists available network interfaces |
| `-i` | Selects the network interface used for capture |
| `-n` | Disables address-to-name resolution |
| `-nn` | Disables hostname and port-name resolution |
| `-e` | Displays Ethernet header information |
| `-X` | Displays packet contents in hexadecimal and ASCII |
| `-XX` | Displays hexadecimal and ASCII output, including link-layer headers |
| `-v` | Increases output verbosity |
| `-vv` | Provides additional verbose information |
| `-vvv` | Provides maximum verbosity among these options |
| `-c` | Captures a specified number of packets before stopping |
| `-s` | Defines the number of bytes captured from each packet (snapshot length) |
| `-S` | Displays absolute TCP sequence numbers |
| `-q` | Displays less protocol information |
| `-r` | Reads packets from a capture file |
| `-w` | Writes captured packets to a file |

### Tcpdump Manual

To view the complete documentation and available options:

```bash
man tcpdump
```

The manual provides detailed information about tcpdump's capabilities and command-line arguments.

---

## Listing Available Interfaces

Before capturing traffic, we need to determine which network interfaces are available.

Use:

```bash
sudo tcpdump -D
```

Example output:

```text
1.eth0 [Up, Running, Connected]
2.any (Pseudo-device that captures on all interfaces) [Up, Running]
3.lo [Up, Running, Loopback]
4.bluetooth0 (Bluetooth adapter number 0) [Wireless]
5.bluetooth-monitor (Bluetooth Linux Monitor) [Wireless]
6.nflog (Linux netfilter log interface)
7.nfqueue (Linux netfilter queue interface)
```

The output identifies interfaces available for packet capture.

Some common examples include:

- `eth0`: Ethernet network interface.
- `any`: Pseudo-interface that captures traffic from multiple interfaces.
- `lo`: Loopback interface.

After identifying the appropriate interface, it can be selected using the `-i` option.

---

## Choosing an Interface to Capture From

The `-i` option specifies which network interface tcpdump should monitor.

### Syntax

```bash
sudo tcpdump -i <interface>
```

### Example

```bash
sudo tcpdump -i eth0
```

This command starts capturing traffic from `eth0`.

Example output:

```text
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes

10:58:33.719241 IP 172.16.146.2.55260 > 172.67.1.1.https:
Flags [P.], seq 1953742992:1953743073,
ack 2034210498, win 501, length 81
```

### Understanding the Output

The first lines indicate:

- Which interface tcpdump is monitoring.
- The link-layer type.
- The snapshot length used for capturing packets.

The captured packet contains information such as:

- Timestamp.
- Protocol.
- Source address and port.
- Destination address and port.
- TCP flags.
- Sequence and acknowledgment numbers.
- Window size.
- Payload length.

Tcpdump continues capturing packets until the process is stopped or a configured capture limit is reached.

---

## Disabling Name Resolution

By default, tcpdump may attempt to translate IP addresses and port numbers into recognizable names.

For example, TCP port `443` may appear as:

```text
https
```

And port `53` may appear as:

```text
domain
```

### Using -n and -nn

The `-n` option disables address-to-name resolution.

The `-nn` combination also prevents protocol port numbers from being converted into service names.

```bash
sudo tcpdump -i eth0 -nn
```

### Example Output

```text
11:02:35.580449 IP 172.16.146.2.48402 > 52.31.199.148.443:
Flags [P.], seq 988167196:988167233,
ack 1512376150, win 501, length 37
```

Instead of displaying a service name such as `https`, tcpdump displays the numerical port:

```text
443
```

### Why Disable Name Resolution?

Using `-nn` allows analysts to:

- View the original numerical addresses.
- Identify exact source and destination ports.
- Avoid unnecessary name resolution during analysis.
- Read the communication endpoints more directly.

### IP and Port Format

Tcpdump displays TCP and UDP endpoints using the following format:

```text
IP.PORT
```

For example:

```text
172.16.146.2.48402 > 52.31.199.148.443
```

This indicates:

- Source IP: `172.16.146.2`
- Source Port: `48402`
- Destination IP: `52.31.199.148`
- Destination Port: `443`

The `>` symbol indicates the direction of communication.

---

## Displaying the Ethernet Header

The `-e` option includes Ethernet header information in the packet output.

### Command

```bash
sudo tcpdump -i eth0 -e
```

Normally, tcpdump begins a packet summary with a timestamp followed by information about the network-layer protocol.

With `-e`, Ethernet header information is also displayed.

### Example

```text
11:05:45.982115
00:0c:29:97:52:65 > 8a:66:5a:11:8d:64,
ethertype IPv4 (0x0800), length 103:
172.16.146.2.57142 > 99.80.22.207.https:
Flags [P.], length 37
```

### Information Displayed

The Ethernet header provides information including:

- Source MAC address.
- Destination MAC address.
- EtherType.
- Frame length.

In this example:

```text
Source MAC:
00:0c:29:97:52:65

Destination MAC:
8a:66:5a:11:8d:64

EtherType:
IPv4 (0x0800)
```

The `-e` option is useful when analyzing communication at the Data Link Layer.

---

## Displaying Hexadecimal and ASCII Output

The `-X` option displays packet contents using hexadecimal and ASCII representations.

### Command

```bash
sudo tcpdump -i eth0 -X
```

The output includes:

1. The normal packet summary.
2. The packet bytes in hexadecimal.
3. An ASCII representation of those bytes.

### Example

```text
11:10:34.984582 IP 172.16.146.2.38732 > 172.16.146.1.domain:
22938+ A? app.hackthebox.eu. (35)

0x0000: 4500 003f 2e6b 4000 4011 901e ac10 9202  E..?.k@.@.......
0x0010: ac10 9201 974c 0035 002b 7c61 599a 0100  .....L.5.+|aY...
0x0020: 0001 0000 0000 0000 0361 7070 0a68 6163  .........app.hac
0x0030: 6b74 6865 626f 7802 6575 0000 0100 01    kthebox.eu.....
```

### Understanding the Output

The hexadecimal representation shows the raw packet bytes.

The ASCII column allows analysts to recognize readable characters when the packet contains plaintext information.

In the example, the ASCII representation reveals part of a DNS query involving:

```text
app.hackthebox.eu
```

The `-X` option is useful for examining packet contents in greater detail.

### Difference Between -X and -XX

| Option | Description |
|---|---|
| `-X` | Displays hexadecimal and ASCII, normally starting at the network-layer header |
| `-XX` | Includes link-layer header bytes in the hexadecimal and ASCII output |

The `-XX` option provides additional visibility into lower-layer packet information.

---

## Increasing Output Verbosity

Tcpdump allows analysts to increase the amount of information displayed for each packet.

The available verbosity levels include:

```bash
-v
-vv
-vvv
```

Each additional `v` increases the level of detail.

### Example

```bash
sudo tcpdump -i eth0 -v
```

Verbose output may include additional information about:

- IP headers.
- Time to Live (TTL).
- Fragmentation information.
- Protocol identifiers.
- TCP options.
- Other protocol-specific fields.

The amount of information displayed depends on the protocols present in the captured packets.

---

## Combining Tcpdump Switches

Tcpdump options can be combined to customize the capture output.

The HTB material demonstrates the following command:

```bash
sudo tcpdump -i eth0 -nnvXX
```

### Command Breakdown

| Option | Function |
|---|---|
| `sudo` | Runs with elevated privileges |
| `tcpdump` | Starts the packet capture tool |
| `-i eth0` | Selects the `eth0` interface |
| `-nn` | Disables hostname and service-name resolution |
| `-v` | Enables verbose output |
| `-XX` | Displays hexadecimal and ASCII, including Ethernet headers |

### Example Output

```text
11:13:59.149599 IP
(tos 0x0, ttl 64, id 24075, offset 0, flags [DF],
proto TCP (6), length 89)

172.16.146.2.42454 > 54.77.251.34.443:
Flags [P.], seq 671020720:671020757,
ack 3699222968, win 501, length 37

0x0000: 8a66 5a11 8d64 000c 2997 5265 0800 4500
0x0010: 0059 5e0b 4000 4006 6d11 ac10 9202 364d
```

This combination provides information from multiple protocol layers.

By adjusting the switches, analysts can control how much detail they want to inspect.

**Important:** Combining switches is useful when a packet capture requires more detailed examination.

---

## Tcpdump Output

Understanding tcpdump's output format is essential for interpreting network packets.

The information displayed depends on the protocols involved and the verbosity options selected.

### Main Output Fields

| Field | Description |
|---|---|
| Timestamp | Time when the packet was captured |
| Protocol | Network-layer protocol identified by tcpdump |
| Source IP and Port | Address and port of the sending endpoint |
| Destination IP and Port | Address and port of the receiving endpoint |
| Flags | TCP control flags present in the packet |
| Sequence Number | TCP sequence information |
| Acknowledgment Number | TCP acknowledgment information |
| Protocol Options | Additional TCP or protocol-specific information |
| Notes / Next Header | Additional information identified during protocol decoding |

### Timestamp

The timestamp normally appears at the beginning of each captured packet.

Example:

```text
11:02:35.580449
```

It indicates the time at which the packet was captured.

### Protocol

The protocol field identifies the network protocol being displayed.

Example:

```text
IP
```

### Source and Destination

The source and destination fields identify the communicating endpoints.

Example:

```text
172.16.146.2.48402 > 52.31.199.148.443
```

This indicates traffic originating from `172.16.146.2` on port `48402` and traveling toward `52.31.199.148` on port `443`.

### TCP Flags

The flags field shows TCP control flags present in a segment.

Example:

```text
Flags [P.]
```

Other examples include:

```text
Flags [S]
Flags [S.]
Flags [.]
Flags [F.]
```

These flags indicate different TCP connection states or operations.

### Sequence and Acknowledgment Numbers

Tcpdump displays sequence and acknowledgment numbers used by TCP to track transmitted data.

Example:

```text
seq 988167196:988167233
ack 1512376150
```

Sequence numbers identify positions in the transmitted TCP byte stream.

Acknowledgment numbers indicate the next expected sequence number.

Tcpdump may display relative sequence numbers for established connections.

Using `-S` requests absolute sequence numbers instead.

### Protocol Options

Tcpdump may also display negotiated TCP options and other protocol information.

Examples include:

- Window size.
- Maximum Segment Size (MSS).
- Selective Acknowledgments (SACK).
- Window scaling.
- TCP timestamps.

### Notes and Additional Protocol Information

Depending on the packet contents, tcpdump may identify additional protocol information.

For example, when examining FTP traffic, tcpdump may recognize application-layer messages within the captured communication.

The amount of information displayed depends on the protocol decoder and verbosity level.

---

## File Input/Output with Tcpdump

Tcpdump can save captured network traffic to a file and read it later.

This is useful when traffic needs to be examined after the original capture has ended.

### Saving a Capture to a PCAP File

The `-w` option writes captured packets to a file.

### Syntax

```bash
sudo tcpdump -i <interface> -w <filename.pcap>
```

### Example

```bash
sudo tcpdump -i eth0 -w ~/output.pcap
```

This command:

1. Captures traffic from the `eth0` interface.
2. Saves the captured packets to `output.pcap`.
3. Continues capturing until the process is stopped.

When writing packets to a file, tcpdump does not display the usual packet summaries in the terminal.

### Example Capture Statistics

```text
10 packets captured
131 packets received by filter
0 packets dropped by kernel
```

These statistics provide information about the capture operation.

| Statistic | Description |
|---|---|
| Packets captured | Number of packets processed by tcpdump |
| Packets received by filter | Packets reported by the capture mechanism |
| Packets dropped by kernel | Packets dropped due to capture-buffer limitations |

The precise meaning of the counters can depend on the operating system and capture mechanism.

### Storage Considerations

Captured traffic can consume significant disk space.

The amount of storage required depends on factors such as:

- Capture duration.
- Network traffic volume.
- Number of monitored devices.
- Amount of packet data captured.

Capturing traffic from a large or busy network segment may generate large PCAP files.

Tcpdump options can help control the amount of information collected.

---

## Reading a PCAP File

The `-r` option allows tcpdump to read an existing capture file.

### Syntax

```bash
tcpdump -r <filename.pcap>
```

### Example

```bash
sudo tcpdump -r ~/output.pcap
```

This command reads the packets previously stored in `output.pcap`.

Example output:

```text
reading from file /home/trey/output.pcap,
link-type EN10MB (Ethernet),
snapshot length 262144

11:15:40.321509 IP 172.16.146.2.57236 > 99.80.22.207.https:
Flags [P.], seq 2751910362:2751910399,
ack 946558143, win 501, length 37
```

The packet information is displayed using tcpdump's standard output format.

### Applying Additional Switches

When reading an existing PCAP file, output options can be applied again.

Example:

```bash
tcpdump -nnvXX -r ~/output.pcap
```

This command:

- Reads the existing PCAP file.
- Disables name resolution.
- Enables verbose output.
- Displays hexadecimal and ASCII information.
- Includes link-layer headers in the hexadecimal output.

This allows analysts to examine the same capture with different levels of detail.

### Difference Between -w and -r

| Option | Purpose |
|---|---|
| `-w` | Saves captured network packets to a file |
| `-r` | Reads packets from an existing capture file |

Example:

```bash
# Capture traffic and save it
sudo tcpdump -i eth0 -w ~/output.pcap

# Read the saved capture
tcpdump -r ~/output.pcap

# Read with additional details
tcpdump -nnvXX -r ~/output.pcap
```

---

## Key Takeaways

- **Tcpdump** is a command-line packet capture and analysis tool commonly used on Unix-like operating systems.
- It uses packet capture libraries such as **libpcap**.
- Live traffic capture generally requires elevated privileges.
- `tcpdump -D` lists available capture interfaces.
- `-i` selects the interface used for packet capture.
- `-nn` disables hostname and port-name resolution.
- `-e` displays Ethernet header information.
- `-X` displays packet contents in hexadecimal and ASCII.
- `-XX` includes link-layer headers in the hexadecimal and ASCII output.
- `-v`, `-vv`, and `-vvv` increase output verbosity.
- `-c` limits the number of packets captured.
- `-s` controls the snapshot length.
- `-S` displays absolute TCP sequence numbers.
- Tcpdump output includes timestamps, protocols, addresses, ports, flags, and other packet information.
- `-w` saves captured traffic to a PCAP file.
- `-r` reads previously captured traffic from a file.
- Tcpdump switches can be combined to customize packet output and analysis.
- Large captures may require considerable storage and processing resources.
