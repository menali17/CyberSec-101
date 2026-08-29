# Network Foundations - Skills Assessment

This document summarizes the command-line tools and practical networking concepts used during the **Network Foundations Skills Assessment**.

The assessment introduces tools for inspecting the local machine, understanding network routes, testing connectivity, enumerating remote hosts, and manually interacting with network services.

---

# 1. ifconfig

`ifconfig` is a command-line tool used to display and configure network interfaces.

## Display all interfaces

```bash
ifconfig -a
```

The `-a` option displays all interfaces, including inactive ones.

During the assessment, the Pwnbox contains three important interfaces:

| Interface | Purpose |
|---|---|
| `lo` | Loopback interface |
| `ens3` | Network interface used for external connectivity |
| `tun0` | VPN tunnel used to communicate with HTB lab machines |

---

## Loopback Interface

The `lo` interface represents the **loopback interface**.

Its IPv4 address is:

```text
127.0.0.1
```

This address allows the machine to communicate with itself.

It is commonly associated with:

```text
localhost
```

Therefore:

```text
127.0.0.1 → localhost → local machine
```

---

# 2. netstat

`netstat` is used to inspect network connections, listening ports, routing information, and network statistics.

During the assessment, it is used to identify services listening on the Pwnbox.

## Display listening services

```bash
netstat -tulnp4
```

### Options

| Option | Meaning |
|---|---|
| `-t` | Show TCP connections |
| `-u` | Show UDP connections |
| `-l` | Show listening sockets |
| `-n` | Display numerical addresses and ports |
| `-p` | Show the PID and program name |
| `-4` | Show IPv4 connections |

---

## Example

```text
tcp  0  0  127.0.0.1:5901  0.0.0.0:*  LISTEN  2294/Xtigervnc
```

This output can be interpreted as:

| Value | Meaning |
|---|---|
| `tcp` | TCP protocol |
| `127.0.0.1` | Service is bound to localhost |
| `5901` | Listening port |
| `LISTEN` | Waiting for incoming connections |
| `2294` | Process ID |
| `Xtigervnc` | Program listening on the port |

Therefore:

```text
127.0.0.1:5901
       │
       └── Xtigervnc
```

---

## Name Resolution

Without the `-n` option:

```bash
netstat -tulp4
```

`netstat` can resolve numerical addresses and known ports into names.

For example:

```text
127.0.0.1
```

may appear as:

```text
localhost
```

---

## Understanding 0.0.0.0

A service listening on:

```text
0.0.0.0
```

is listening on **all available IPv4 interfaces**.

This is different from:

```text
127.0.0.1
```

which restricts communication to the local host.

---

# 3. ip route

The `ip` command provides tools for inspecting and configuring networking on Linux.

During the assessment, `ip route` is used to determine how traffic will reach an HTB target.

## Check the route to a target

```bash
ip route get <TARGET_IP>
```

Example:

```bash
ip route get 10.129.233.197
```

The output may contain:

```text
dev tun0
```

This means traffic destined for the target will be sent through the `tun0` interface.

Conceptually:

```text
Target IP
    │
    ▼
Routing Table
    │
    ▼
tun0
    │
    ▼
HTB VPN
    │
    ▼
Target Machine
```

This is useful for confirming that traffic to HTB lab machines is being routed through the VPN.

---

# 4. ping

`ping` is used to test whether another host is reachable over an IP network.

It sends **ICMP Echo Request** packets and waits for **ICMP Echo Reply** responses.

## Send four requests

```bash
ping -c 4 <TARGET_IP>
```

The `-c` option specifies how many packets should be sent.

Without it, `ping` normally continues until manually interrupted.

```text
Ctrl + C
```

---

## Example Output

```text
64 bytes from 10.129.233.197: icmp_seq=1 ttl=127 time=71.6 ms
```

Important information includes:

| Field | Meaning |
|---|---|
| `icmp_seq` | Sequence number of the ICMP packet |
| `ttl` | Time To Live |
| `time` | Round-trip latency |

---

## Packet Loss

At the end of the command, `ping` displays statistics such as:

```text
4 packets transmitted
4 received
0% packet loss
```

This indicates that all four requests successfully received responses.

---

## TTL

`TTL` stands for:

**Time To Live**

It prevents IP packets from circulating indefinitely through networks.

As a packet passes through routers, its TTL is reduced.

---

# 5. Nmap

`Nmap` is a network scanning and enumeration tool.

During the assessment, it is used to discover open TCP ports on the target machine.

## Basic Scan

```bash
nmap <TARGET_IP>
```

Example output:

```text
PORT      STATE SERVICE
21/tcp    open  ftp
80/tcp    open  http
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
```

---

## Understanding the Output

The three main columns are:

| Column | Meaning |
|---|---|
| `PORT` | Port number and transport protocol |
| `STATE` | Whether the port is open, closed, filtered, etc. |
| `SERVICE` | Service commonly associated with that port |

For example:

```text
21/tcp open ftp
```

means that TCP port `21` is open and is associated with FTP.

---

## Common Ports Seen in the Assessment

| Port | Service |
|---:|---|
| `21` | FTP |
| `80` | HTTP |
| `135` | Microsoft RPC |
| `139` | NetBIOS |
| `445` | Microsoft-DS / SMB |
| `3389` | Remote Desktop Protocol |

The presence of multiple Microsoft-related services can also provide clues about the target operating system.

---

# 6. Advanced Nmap Enumeration

The optional part of the assessment introduces a more detailed scan:

```bash
nmap -p21,80 -sC -sV <TARGET_IP>
```

## Options

| Option | Meaning |
|---|---|
| `-p21,80` | Scan only ports 21 and 80 |
| `-sC` | Run Nmap default scripts |
| `-sV` | Detect service versions |

A basic scan answers questions such as:

```text
Is port 21 open?
Is port 80 open?
```

A more detailed scan can answer:

```text
What FTP server is running?
What HTTP server is running?
Which version is being used?
Does the service expose additional information?
```

---

## Layer 4 vs Layer 7

A basic TCP port scan primarily investigates the **Transport Layer (Layer 4)**.

For example:

```text
Is TCP port 21 open?
```

Service detection interacts more deeply with the protocol running on that port, involving the **Application Layer (Layer 7)**.

For example:

```text
Port 21 is open
       ↓
FTP is running
       ↓
Microsoft FTP Service
```

---

# 7. Netcat

`Netcat`, commonly invoked using `nc`, is a command-line utility for creating network connections.

It can communicate directly with TCP services.

This makes it useful for understanding how application protocols actually communicate.

---

## Basic Connection

```bash
nc <TARGET_IP> <PORT>
```

For example:

```bash
nc <TARGET_IP> 21
```

connects directly to the FTP service.

Similarly:

```bash
nc <TARGET_IP> 80
```

connects directly to the HTTP service.

Unlike specialized applications such as browsers or FTP clients, Netcat allows us to interact with these protocols manually.

---

# 8. FTP with Netcat

FTP uses TCP port:

```text
21
```

for its control connection.

After connecting:

```bash
nc <TARGET_IP> 21
```

the FTP server may return a banner.

Example:

```text
220 Microsoft FTP Service
```

This indicates that the TCP connection to the FTP service was successful.

---

## FTP Authentication

FTP commands can be entered manually.

For anonymous authentication:

```text
USER anonymous
PASS anything
```

These are actual commands understood by the FTP protocol.

---

## FTP Control and Data Connections

FTP is interesting because it uses separate connections for:

```text
Control Connection
        │
        └── Commands and authentication

Data Connection
        │
        └── Files and directory listings
```

The control connection normally uses:

```text
TCP 21
```

The data connection may use another port.

---

## Passive Mode

The command:

```text
PASV
```

asks the FTP server to enter passive mode.

The server responds with information containing a dynamically selected port.

The final two numbers can be used to calculate the port:

```text
PORT = p1 × 256 + p2
```

For example:

```text
194 × 256 + 40
```

results in:

```text
49704
```

A second Netcat connection can then be created:

```bash
nc -v <TARGET_IP> 49704
```

This becomes the FTP **data connection**.

---

## Listing Files

The FTP command:

```text
LIST
```

requests a directory listing.

The command is sent through the control connection:

```text
TCP 21
```

while the actual directory listing is returned through the data connection.

---

## Retrieving Files

The FTP command:

```text
RETR <FILENAME>
```

requests a file from the server.

Again:

```text
Control Channel → RETR command
Data Channel    → File contents
```

---

# 9. HTTP with Netcat

Netcat can also communicate directly with an HTTP server.

Connect to TCP port `80`:

```bash
nc -v <TARGET_IP> 80
```

Unlike a browser, Netcat does not automatically generate an HTTP request.

We must construct it ourselves.

---

## Manual HTTP Request

Example:

```http
GET / HTTP/1.1
Host: <TARGET_IP>
User-Agent: Server Administrator
```

The empty line after the headers indicates the end of the HTTP request headers.

---

## GET

```text
GET /
```

asks the server for the root resource.

For example:

```text
GET /login.php
```

would request `/login.php`.

---

## Host

The `Host` header identifies which host the client wants to access.

```http
Host: <TARGET_IP>
```

This is especially important when multiple websites are hosted by the same server.

---

## User-Agent

The `User-Agent` header identifies information about the client making the request.

Example:

```http
User-Agent: Server Administrator
```

The assessment demonstrates that servers can inspect HTTP headers and change their behavior depending on their contents.

---

# 10. HTTP Response

After receiving a valid request, the server returns an HTTP response.

For example:

```text
HTTP/1.1 200 OK
```

`200 OK` indicates that the request succeeded.

The response may contain headers such as:

```text
Content-Type
Content-Length
Server
Date
```

followed by the webpage content.

---

# 11. Tool Comparison

Each tool answers a different networking question.

| Tool | Main Question |
|---|---|
| `ifconfig` | What network interfaces does my machine have? |
| `netstat` | What connections, ports, and services exist on my machine? |
| `ip route` | Which route/interface will my traffic use? |
| `ping` | Can I reach the target? |
| `nmap` | Which ports/services are exposed by the target? |
| `nc` | Can I communicate directly with a network service? |

A useful way to visualize the workflow is:

```text
LOCAL MACHINE

ifconfig
   │
   └── What interfaces do I have?
            │
            ▼
netstat
   │
   └── What is listening locally?
            │
            ▼
ip route
   │
   └── How will I reach the target?
            │
            ▼
          tun0
            │
            ▼
         HTB VPN
            │
            ▼
ping
   │
   └── Is the target reachable?
            │
            ▼
nmap
   │
   └── What ports/services are exposed?
            │
            ▼
netcat
   │
   └── How does the service communicate?
```

---

# 12. Commands Cheat Sheet

## Interfaces

```bash
ifconfig -a
```

Display all network interfaces.

## Local Services

```bash
netstat -tulnp4
```

Display listening TCP/UDP IPv4 services and their processes.

```bash
netstat -tulp4
```

Display similar information with hostname/service resolution.

## Routing

```bash
ip route get <TARGET_IP>
```

Determine which route and interface will be used to reach a target.

## Connectivity

```bash
ping -c 4 <TARGET_IP>
```

Send four ICMP Echo Requests to test reachability.

## Port Enumeration

```bash
nmap <TARGET_IP>
```

Scan common TCP ports.

## Service Enumeration

```bash
nmap -p21,80 -sC -sV <TARGET_IP>
```

Scan specific ports using default scripts and service/version detection.

## Raw TCP Connection

```bash
nc <TARGET_IP> <PORT>
```

Connect directly to a TCP service.

---

# Key Takeaways

- `ifconfig` is useful for inspecting the machine's network interfaces.
- `lo` represents the loopback interface and uses `127.0.0.1`.
- `tun0` provides connectivity to HTB lab machines through the VPN.
- `netstat` can identify listening ports and the processes associated with them.
- A service bound to `127.0.0.1` is listening locally.
- A service bound to `0.0.0.0` listens on all available IPv4 interfaces.
- `ip route get` reveals how traffic will be routed toward a destination.
- `ping` tests basic reachability using ICMP.
- `nmap` discovers open ports and can perform service enumeration.
- `-sC` runs Nmap's default scripts.
- `-sV` performs service/version detection.
- `nc` allows direct interaction with TCP services.
- FTP separates control traffic from data traffic.
- HTTP requests can be manually constructed to understand application-layer communication.
- Each tool provides visibility into a different stage of network communication.