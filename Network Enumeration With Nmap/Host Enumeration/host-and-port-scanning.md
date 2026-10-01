# Host and Port Scanning

---

After discovering a target, we investigate its exposed ports and services to understand what the system offers.

We want to identify:

- **Open ports and services.**
- **Service versions.**
- **Information exposed by those services.**
- **Clues about the operating system.**

To interpret results correctly, we need to understand how each scanning technique works. **The same response can have different meanings depending on the scan type.**

Nmap reports six possible port states:

| State | Meaning |
|---|---|
| `open` | An application is accepting TCP connections, UDP datagrams, or SCTP associations on the port. |
| `closed` | The port is reachable, but no application appears to be listening. |
| `filtered` | Filtering or inconclusive responses prevent Nmap from determining whether the port is open or closed. |
| `unfiltered` | An ACK scan finds the port reachable but cannot determine whether it is open or closed. |
| `open\|filtered` | The scan cannot distinguish an open port from a filtered one. |
| `closed\|filtered` | An IP ID idle scan cannot distinguish a closed port from a filtered one. |

**`Open` does not necessarily mean a complete connection was established.** For example, a SYN scan can identify an open TCP port without completing the handshake.

These states describe what Nmap can infer from our scanning position and the selected technique.

---

## Discovering Open TCP Ports

By default, Nmap scans the **1,000 most common TCP ports**.

With the necessary raw-packet privileges, it normally uses a **TCP SYN scan (`-sS`)**. Without those privileges, it normally uses a **TCP connect scan (`-sT`)**.

We can select the ports explicitly:

| Option | Ports scanned |
|---|---|
| `-p 22,25,80,139,445` | Only the listed ports. |
| `-p 22-445` | Every port from 22 through 445, inclusive. |
| `--top-ports=10` | The 10 most common ports for the selected protocol. |
| `-F` | The 100 most common ports for the selected protocol. |
| `-p-` | Ports 1 through 65535. |

**The most common ports are not the lowest-numbered ports.** Nmap uses frequency information from its database.

### Scanning Top 10 TCP Ports

```bash
sudo nmap 10.129.2.28 --top-ports=10
```

Output from the supplied example:

```text
PORT     STATE    SERVICE
21/tcp   closed   ftp
22/tcp   open     ssh
23/tcp   closed   telnet
25/tcp   open     smtp
80/tcp   open     http
110/tcp  open     pop3
139/tcp  filtered netbios-ssn
443/tcp  closed   https
445/tcp  filtered microsoft-ds
3389/tcp closed   ms-wbt-server
```

We identify:

- **Open:** 22, 25, 80, and 110.
- **Closed:** 21, 23, 443, and 3389.
- **Filtered:** 139 and 445.

Without service detection, the `SERVICE` names generally come from port mappings. They do not confirm which application is actually listening.

### Nmap — Trace the Packets

We can inspect a SYN scan against a single port:

```bash
sudo nmap 10.129.2.28 -sS -p 21 --packet-trace -Pn -n --disable-arp-ping
```

| Option | Purpose |
|---|---|
| `-sS` | Selects a TCP SYN scan. |
| `-p 21` | Scans TCP port 21 only. |
| `--packet-trace` | Shows packet details. |
| `-Pn` | Skips normal host discovery and attempts the requested scan against each target. |
| `-n` | Disables DNS resolution. |
| `--disable-arp-ping` | Disables automatic ARP-based host discovery. |

**`-Pn` does more than disable ICMP Echo.** It tells Nmap to proceed without first requiring a successful host discovery result.

Compare:

- **`-sn`:** discover hosts without a subsequent port scan.
- **`-Pn`:** skip normal host discovery and proceed with the requested scanning.

A shortened trace from the example shows:

```text
SENT (...) TCP 10.10.14.2:63090 > 10.129.2.28:21 S ...
RCVD (...) TCP 10.129.2.28:21 > 10.10.14.2:63090 RA ...
```

### Request

```text
10.10.14.2:63090 > 10.129.2.28:21 S
```

| Field | Meaning |
|---|---|
| `10.10.14.2` | Our source IP address. |
| `63090` | Our source port. |
| `10.129.2.28` | The target IP address. |
| `21` | The target port. |
| `S` | The SYN flag. |

We send a SYN probe to TCP port 21.

### Response

```text
10.129.2.28:21 > 10.10.14.2:63090 RA
```

| Field | Meaning |
|---|---|
| `10.129.2.28:21` | The responding target and port. |
| `10.10.14.2:63090` | Our receiving IP address and port. |
| `R` | RST: rejects or resets the attempted connection. |
| `A` | ACK: acknowledges the received SYN. |

The reset response causes Nmap to classify the port as **closed**. No established TCP session was required.

Other trace fields include both IP and TCP information:

| Field | Meaning |
|---|---|
| `ttl` | IP Time to Live. |
| `id` | IPv4 identification field. |
| `iplen` | Total IP packet length. |
| `seq` | TCP sequence number. |
| `win` | TCP receive window. |
| `mss` | TCP Maximum Segment Size option. |

### Connect Scan

A **TCP connect scan (`-sT`)** asks the operating system to establish a connection using its normal connection API.

For an open port, the TCP handshake completes:

1. We send **SYN**.
2. The target sends **SYN-ACK**.
3. Our system sends **ACK**.

Nmap then closes the connection.

| TCP SYN scan (`-sS`) | TCP connect scan (`-sT`) |
|---|---|
| Uses crafted packets. | Uses the operating system's connection API. |
| Does not complete the handshake. | Completes the handshake for open ports. |
| Normally requires elevated raw-packet privileges. | Can generally run without those privileges. |
| May avoid some application-level connection logs. | More likely to generate application connection logs. |

**Both techniques can be detected.** A SYN scan is not invisible, and a connect scan does not guarantee an unambiguous result when filtering prevents communication.

### Connect Scan on TCP Port 443

```bash
sudo nmap 10.129.2.28 -sT -p 443 --packet-trace --disable-arp-ping -Pn -n --reason
```

Relevant output from the example:

```text
CONN (...) TCP localhost > 10.129.2.28:443 => Operation now in progress
CONN (...) TCP localhost > 10.129.2.28:443 => Connected

PORT    STATE SERVICE REASON
443/tcp open  https   syn-ack
```

`Connected` indicates that the connection succeeded.

With a connect scan, packet tracing may show **connection events** rather than individual raw packets.

If we see:

```text
Host is up, received user-set
```

The host status comes from our use of `-Pn`, rather than a successful discovery probe. We should inspect the actual port responses for evidence of reachability.

---

## Filtered Ports

A firewall can handle unwanted traffic in different ways:

| Behavior | What happens |
|---|---|
| **Drop** | The packet is silently discarded. |
| **Reject** | An error or reset is returned. |

### Dropped Packets

```bash
sudo nmap 10.129.2.28 -sS -p 139 --packet-trace -n --disable-arp-ping -Pn
```

The supplied example shows two SYN probes and no replies:

```text
SENT (...) TCP 10.10.14.2:60277 > 10.129.2.28:139 S ...
SENT (...) TCP 10.10.14.2:60278 > 10.129.2.28:139 S ...
```

Result:

```text
139/tcp filtered netbios-ssn
```

Nmap waits and may retry because a missing response could result from filtering or packet loss.

**`--max-retries` sets an upper limit on probe retransmissions, not a fixed number sent to every port.** Nmap adapts its behavior, so a scan can use fewer retries than the configured maximum.

Waiting for unanswered probes can make scans slower.

### Rejected Packets

```bash
sudo nmap 10.129.2.28 -sS -p 445 --packet-trace -n --disable-arp-ping -Pn
```

In the supplied example, Nmap receives:

```text
ICMP Port unreachable (type=3/code=3)
```

For this **TCP SYN scan**, the response results in:

```text
445/tcp filtered microsoft-ds
```

A rejection provides immediate feedback, potentially reducing the waiting time.

However, **not every rejection produces `filtered`**. If a firewall rejects a SYN probe with a TCP RST, Nmap can report `closed`.

We interpret the actual response and scan type, rather than assuming that every firewall rejection looks the same.

---

## Discovering Open UDP Ports

UDP does not establish connections through a TCP-style handshake. An application may ignore a probe even when its port is open.

We select UDP scanning with `-sU`:

```bash
sudo nmap 10.129.2.28 -F -sU
```

This checks the **100 most common UDP ports**.

Output from the supplied example:

```text
PORT     STATE         SERVICE
68/udp   open|filtered dhcpc
137/udp  open          netbios-ns
138/udp  open|filtered netbios-dgm
631/udp  open|filtered ipp
5353/udp open          zeroconf
```

Nmap may send empty datagrams or protocol-specific payloads.

| Response to a UDP probe | State |
|---|---|
| UDP reply from the target port | `open` |
| ICMP type 3, code 3: port unreachable | `closed` |
| ICMP type 3, codes 1, 2, 9, 10, or 13 | `filtered` |
| No response after retries | `open\|filtered` |

**Silence is ambiguous:** an open application may ignore the probe, or filtering may prevent a response.

UDP scans can be slow because of timeouts, retransmissions, and rate limiting of ICMP errors.

### Open UDP Port

```bash
sudo nmap 10.129.2.28 -sU -Pn -n --disable-arp-ping --packet-trace -p 137 --reason
```

Relevant output:

```text
137/udp open netbios-ns udp-response ttl 64
```

The `udp-response` reason shows that a UDP reply was received.

### Closed UDP Port

```bash
sudo nmap 10.129.2.28 -sU -Pn -n --disable-arp-ping --packet-trace -p 100 --reason
```

Relevant output:

```text
100/udp closed unknown port-unreach ttl 64
```

An ICMP **type 3, code 3** response leads to `closed` for this UDP scan.

Notice the difference from the earlier TCP SYN example: **the scan type changes how we interpret the response**.

### Open or Filtered UDP Port

```bash
sudo nmap 10.129.2.28 -sU -Pn -n --disable-arp-ping --packet-trace -p 138 --reason
```

Relevant output:

```text
138/udp open|filtered netbios-dgm no-response
```

We have no reply, so we cannot distinguish an open port from filtering.

### Version Scan

We use **`-sV`** to investigate the service behind a port and identify its version where possible:

```bash
sudo nmap 10.129.2.28 -Pn -n --disable-arp-ping --packet-trace -p 445 --reason -sV
```

Output from the supplied example:

```text
PORT    STATE SERVICE     REASON         VERSION
445/tcp open  netbios-ssn syn-ack ttl 63 Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
Service Info: Host: Ubuntu
```

This command scans **TCP**, despite appearing after the UDP examples. It does not include `-sU`.

The trace shows that Nmap:

1. Receives a SYN-ACK from port 445.
2. Connects to the service.
3. Initially waits for a banner.
4. Sends an SMB-specific probe.
5. Matches the response to **Samba smbd**.

We learn more than a port-to-service association: the response identifies a service implementation and an estimated version range.

**`3.X - 4.X` is a range, not an exact version.** Also, `Host: Ubuntu` is a reported host identifier; that line alone does not confirm the operating system.

To combine UDP scanning with service detection, we include both options:

```bash
sudo nmap 10.129.2.28 -sU -sV -p 137
```

Service-specific probes may obtain a reply from a UDP port previously reported as `open|filtered`, allowing Nmap to classify it as `open`.

The examples show port 445 as filtered in one scan and open in another. They represent different scan conditions or snapshots: **adding `-sV` does not itself bypass filtering**.