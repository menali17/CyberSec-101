# Nmap - Firewall and IDS/IPS Evasion

Nmap provides several techniques that can help us understand and potentially bypass firewall rules and IDS/IPS filtering.

Some techniques include:

- ACK scans
- decoy scanning
- source IP manipulation
- source port manipulation
- DNS-related techniques

The main goal is to understand how the target network handles different types of traffic.

---

# Firewalls

A **firewall** controls network traffic according to predefined rules.

It analyzes packets and decides whether they should be:

- allowed
- dropped
- rejected

Firewalls can be implemented using:

- software
- hardware
- a combination of both

Their main purpose is to prevent unauthorized or potentially dangerous connections.

---

## Dropped vs Rejected Packets

When a firewall blocks traffic, it can behave mainly in two ways.

### Dropped Packet

A dropped packet is simply ignored.

```text
Packet sent
     ↓
Firewall drops it
     ↓
No response
```

From our perspective, it may look like the target is not responding.

---

### Rejected Packet

A rejected packet causes an explicit response.

Examples include:

```text
TCP RST
```

or ICMP errors such as:

- Network Unreachable
- Network Prohibited
- Host Unreachable
- Host Prohibited
- Port Unreachable
- Protocol Unreachable

This difference can help us understand how firewall rules are behaving.

---

# ACK Scan

One technique for analyzing firewall behavior is the TCP ACK scan.

```bash
-sA
```

Unlike a SYN scan, the ACK scan sends packets with only the:

```text
ACK
```

flag set.

Example:

```bash
sudo nmap <target> -p 21,22,25 -sA
```

The main purpose of an ACK scan is **not to determine whether a port is open**.

Instead, it helps us determine whether the port is:

```text
filtered
```

or:

```text
unfiltered
```

---

## Why ACK Scans Can Behave Differently

Normal TCP connection attempts begin using the:

```text
SYN
```

flag.

Firewalls commonly block unsolicited SYN packets coming from external networks.

However, an ACK packet may look like part of an already established connection.

Because of this, some firewall configurations may allow ACK packets through.

---

# SYN Scan vs ACK Scan

Consider:

```bash
sudo nmap 10.129.2.28 -p 21,22,25 -sS -Pn -n \
--disable-arp-ping --packet-trace
```

Results:

```text
21/tcp filtered
22/tcp open
25/tcp filtered
```

For port 22, the target responds with:

```text
SYN-ACK
```

which means the SYN scan identifies the port as open.

---

Now consider:

```bash
sudo nmap 10.129.2.28 -p 21,22,25 -sA -Pn -n \
--disable-arp-ping --packet-trace
```

Results:

```text
21/tcp filtered
22/tcp unfiltered
25/tcp filtered
```

For port 22, the target responds with:

```text
RST
```

This tells us the ACK packet reached the host.

Therefore:

```text
unfiltered
```

means the firewall allowed the probe through.

---

## Important

With an ACK scan:

```text
RST response
     ↓
Packet reached the target
     ↓
Port is unfiltered
```

No response or certain ICMP errors may indicate:

```text
filtered
```

The ACK scan is primarily useful for **firewall rule discovery**.

---

# Useful Options

| Option | Description |
|---|---|
| `-p 21,22,25` | Scans only the specified ports |
| `-sS` | Performs a SYN scan |
| `-sA` | Performs an ACK scan |
| `-Pn` | Skips host discovery |
| `-n` | Disables DNS resolution |
| `--disable-arp-ping` | Disables ARP ping |
| `--packet-trace` | Displays packets sent and received |

---

# Packet Trace

The option:

```bash
--packet-trace
```

allows us to inspect the packets Nmap sends and receives.

This is useful when analyzing firewall behavior because we can directly inspect TCP flags.

Examples:

```text
S
```

means:

```text
SYN
```

```text
SA
```

means:

```text
SYN-ACK
```

```text
R
```

means:

```text
RST
```

```text
A
```

means:

```text
ACK
```

---

# Detecting IDS/IPS

## IDS

An **Intrusion Detection System (IDS)** monitors network traffic for suspicious behavior.

It can:

- inspect connections
- detect attack patterns
- match known signatures
- notify administrators

An IDS usually does not automatically block traffic by itself.

---

## IPS

An **Intrusion Prevention System (IPS)** goes further.

It can automatically react when malicious or suspicious traffic is detected.

For example:

```text
Suspicious scan detected
        ↓
IPS identifies source IP
        ↓
Source IP is blocked
```

An IPS complements IDS functionality by actively taking defensive measures.

---

# IDS vs IPS

| IDS | IPS |
|---|---|
| Detects suspicious activity | Detects suspicious activity |
| Alerts administrators | Can automatically block traffic |
| Primarily monitoring | Monitoring + prevention |
| Passive response | Active response |

---

# Detecting Defensive Systems

Detecting an IDS can be difficult because it may operate passively.

One possible indication is that:

```text
we perform aggressive scanning
        ↓
administrator notices the activity
        ↓
defensive measures are taken
```

An IPS may be easier to notice because it can automatically block our source address.

For example:

```text
Initial scans work
        ↓
Repeated aggressive scans
        ↓
Our IP loses access
```

This behavior may indicate automated defensive controls.

---

# Decoy Scanning

Nmap supports decoy scans using:

```bash
-D
```

The objective is to mix our real source address with additional apparent source addresses.

Example:

```bash
sudo nmap 10.129.2.28 -p 80 -sS -Pn -n \
--disable-arp-ping --packet-trace -D RND:5
```

The option:

```bash
-D RND:5
```

causes Nmap to generate five random decoy IP addresses.

The target therefore receives packets that appear to originate from multiple IP addresses.

Example:

```text
102.52.161.59
10.10.14.2        ← our real IP
210.120.38.29
191.6.64.171
184.178.194.209
43.21.121.33
```

The actual source address is mixed among the decoys.

---

## Purpose of Decoys

The goal is to make it harder for the target to identify which IP address is performing the scan.

Conceptually:

```text
Our IP
   +
Fake source IPs
   ↓
Target receives similar probes from many addresses
```

---

## Important Limitation

The decoys should ideally correspond to live hosts.

Using unavailable or unsuitable decoys may create problems because the target can receive many SYN packets without proper follow-up traffic.

Spoofed packets may also be filtered by:

- routers
- ISPs
- upstream network controls

Therefore, decoy scanning is not guaranteed to work in every network.

---

# Source IP Manipulation

We can manually specify a source IP using:

```bash
-S <IP>
```

Example:

```bash
sudo nmap 10.129.2.28 -n -Pn -p 445 -O \
-S 10.129.2.200 -e tun0
```

Options:

```text
-S 10.129.2.200
```

sets the source IP.

```text
-e tun0
```

specifies the network interface.

---

# Testing Firewall Rules

Consider a normal scan:

```bash
sudo nmap 10.129.2.28 -n -Pn -p445 -O
```

Result:

```text
445/tcp filtered
```

Now using another source IP:

```bash
sudo nmap 10.129.2.28 -n -Pn -p445 -O \
-S 10.129.2.200 -e tun0
```

Result:

```text
445/tcp open
```

This can indicate that the firewall applies different rules depending on the source address.

Conceptually:

```text
Source A
   ↓
Firewall blocks traffic

Source B
   ↓
Firewall allows traffic
```

This reveals information about firewall trust rules or network segmentation.

---

# Relevant Options

| Option | Description |
|---|---|
| `-S <IP>` | Specifies a different source IP |
| `-e <interface>` | Specifies the network interface |
| `-O` | Performs OS detection |
| `-Pn` | Skips host discovery |
| `-n` | Disables DNS resolution |
| `-p 445` | Scans port 445 |

---

# DNS Proxying

By default, Nmap performs reverse DNS resolution unless disabled.

DNS normally uses:

```text
UDP/53
```

TCP port 53 has traditionally been used for situations such as:

- DNS zone transfers
- responses larger than 512 bytes

Modern technologies such as:

- IPv6
- DNSSEC

have increased the use of TCP port 53.

---

# Specifying DNS Servers

Nmap allows us to specify DNS servers using:

```bash
--dns-server <server>
```

This can be useful in environments such as a:

```text
DMZ
```

Internal DNS servers may be trusted more than external systems and may provide information about internal hosts.

---

# Using DNS as a Source Port

We can also specify the source port of our Nmap packets:

```bash
--source-port <port>
```

For example:

```bash
--source-port 53
```

The reasoning is that some firewall rules may trust traffic originating from DNS-related ports.

---

# Example: Filtered Port

Normal SYN scan:

```bash
sudo nmap 10.129.2.28 -p50000 -sS -Pn -n \
--disable-arp-ping --packet-trace
```

Result:

```text
50000/tcp filtered
```

The target does not respond to the SYN packets.

---

# Using Source Port 53

Now:

```bash
sudo nmap 10.129.2.28 -p50000 -sS -Pn -n \
--disable-arp-ping --packet-trace \
--source-port 53
```

Result:

```text
50000/tcp open
```

Packet flow:

```text
Our host:53
     ↓
Target:50000
     ↓
SYN-ACK
```

This suggests that the firewall allows traffic originating from TCP port 53.

---

# Why This Happens

A poorly configured firewall may contain rules similar to:

```text
Traffic from source port 53 → trusted
Other traffic → filtered
```

Since DNS is necessary for normal network operation, DNS-related traffic is often allowed through firewalls.

If the firewall trusts traffic based only on the source port, this configuration can potentially be abused.

---

# Source Port Option

The syntax is:

```bash
--source-port <port>
```

Example:

```bash
sudo nmap <target> -p 50000 -sS --source-port 53
```

or using the short form:

```bash
-g 53
```

---

# Testing With Ncat

Once we discover that the firewall accepts connections using source port 53, we can test the service using Ncat.

Example:

```bash
ncat -nv --source-port 53 10.129.2.28 50000
```

Result:

```text
Ncat: Connected to 10.129.2.28:50000.
220 ProFTPd
```

This confirms that the service behind the previously filtered port is reachable when our connection originates from port 53.

---

# Key Commands

## SYN Scan

```bash
sudo nmap <target> -p <ports> -sS
```

---

## ACK Scan

```bash
sudo nmap <target> -p <ports> -sA
```

---

## Show Packet Flow

```bash
sudo nmap <target> --packet-trace
```

---

## Decoy Scan

```bash
sudo nmap <target> -D RND:5
```

---

## Change Source IP

```bash
sudo nmap <target> -S <source-ip> -e <interface>
```

---

## Change Source Port

```bash
sudo nmap <target> --source-port 53
```

Short form:

```bash
sudo nmap <target> -g 53
```

---

## Specify DNS Server

```bash
sudo nmap <target> --dns-server <dns-server>
```

---

## Ncat With Custom Source Port

```bash
ncat -nv --source-port 53 <target> <port>
```

---

# Main Takeaways

- Firewalls can either **drop** or **reject** packets.
- Dropped packets produce no response.
- Rejected packets usually generate TCP RST or ICMP errors.
- SYN scans are useful for determining whether ports are open.
- ACK scans are useful for understanding firewall filtering rules.
- An ACK response with `RST` generally indicates that the probe reached the host and the path is `unfiltered`.
- `IDS` detects and reports suspicious activity.
- `IPS` can automatically block suspicious traffic.
- Decoy scans use multiple apparent source IP addresses to obscure the real scanning source.
- `-S` allows us to specify a different source IP.
- `-e` selects the network interface.
- Firewall rules may behave differently depending on the source IP.
- DNS traffic is often trusted by firewalls.
- `--source-port 53` can test whether a firewall trusts traffic originating from the DNS port.
- `--packet-trace` is especially useful when studying firewall behavior because it lets us inspect TCP flags directly.