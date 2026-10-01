# Service Enumeration

---

**Service enumeration** helps us determine which application is listening on a port, identify its version, and examine the information it exposes.

These findings help us research known vulnerabilities and understand whether they might apply to the target.

**A version match alone does not prove vulnerability.** Configuration, operating system, and installed security patches also matter.

---

## Service Version Detection

We can start with a smaller port scan, investigate the services we discover, and then expand our coverage to all TCP ports.

For example, we can investigate selected ports with:

```bash
sudo nmap 10.129.2.28 -p 22,25,80 -sV
```

**`-sV` enables service and version detection.** Nmap interacts with services and compares their responses against known patterns.

To scan all TCP ports and perform service detection:

```bash
sudo nmap 10.129.2.28 -p- -sV
```

| Option | Purpose |
|---|---|
| `-p-` | Scans TCP ports 1 through 65535 in this command. |
| `-sV` | Attempts to identify the services and their versions. |

The duration depends on filtering, network conditions, and how services respond.

### Checking Progress Manually

During an interactive scan, we can press the **Space Bar** to request a status update.

Example:

```text
Stats: 0:00:03 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 3.64% done; ETC: 19:45 (0:00:53 remaining)
```

This tells us:

- How much time has elapsed.
- Which scanning phase is running.
- The estimated progress of that phase.
- The estimated completion time.

**The estimate can change**, and finishing the port scan does not necessarily mean service detection has finished.

### Automatic Progress Updates

We can request updates at regular intervals:

```bash
sudo nmap 10.129.2.28 -p- -sV --stats-every=5s
```

| Option | Purpose |
|---|---|
| `--stats-every=5s` | Prints a status update every five seconds. |

This changes how frequently progress is displayed, not how fast the scan runs.

### Increasing Verbosity

We can use `-v` to display more information while Nmap runs:

```bash
sudo nmap 10.129.2.28 -p- -sV -v
```

For example:

```text
Discovered open port 80/tcp on 10.129.2.28
Discovered open port 25/tcp on 10.129.2.28
Discovered open port 22/tcp on 10.129.2.28
```

We can use `-vv` for additional verbosity.

**`-v` and `-sV` have different purposes:**

| Option | Meaning |
|---|---|
| `-v` | Displays more information about the scan. |
| `-sV` | Performs service and version detection. |

---

## Banner Grabbing

A **banner** is identifying information provided by a service. It may reveal:

- The application name.
- A software version.
- A hostname.
- Operating system or distribution clues.

Some services send a greeting immediately after a connection is established. Others require us to send a valid protocol request first.

A service scan may produce results such as:

```text
PORT    STATE SERVICE VERSION
22/tcp  open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3
25/tcp  open  smtp    Postfix smtpd
80/tcp  open  http    Apache httpd 2.4.29 ((Ubuntu))
110/tcp open  pop3    Dovecot pop3d
```

We should distinguish between identifying an application and identifying an exact version:

- `Apache httpd 2.4.29` includes a version.
- `Postfix smtpd` identifies the application but provides no numeric version.

Nmap analyzes greetings and responses to service-specific probes. Its summary may omit details present in the raw response.

For example, the summary might show:

```text
25/tcp open smtp Postfix smtpd
```

While the actual greeting contains:

```text
220 inlane ESMTP Postfix (Ubuntu)
```

| Banner component | Interpretation |
|---|---|
| `220` | SMTP greeting indicating that the service is ready. |
| `inlane` | The server's advertised name. |
| `ESMTP` | Extended SMTP. |
| `Postfix` | The advertised mail server software. |
| `(Ubuntu)` | A clue about the system or package distribution. |

**Banners can be customized, hidden, or misleading.** We use them as evidence to investigate, rather than unquestionable proof.

We can inspect additional scan details with:

```bash
sudo nmap 10.129.2.28 -p 25 -sV -Pn -n --disable-arp-ping --packet-trace
```

| Option | Purpose |
|---|---|
| `-p 25` | Focuses on TCP port 25. |
| `-sV` | Investigates the service and version. |
| `-Pn` | Skips normal host discovery and proceeds with scanning. |
| `-n` | Disables DNS resolution. |
| `--disable-arp-ping` | Disables automatic ARP-based discovery. |
| `--packet-trace` | Displays packet and connection details. |

### Tcpdump

We can capture our traffic with `tcpdump` while connecting to the service in another terminal.

Using the example addresses:

```bash
sudo tcpdump -i eth0 -nn 'host 10.10.14.2 and host 10.129.2.28'
```

| Component | Purpose |
|---|---|
| `-i eth0` | Captures on the specified network interface. |
| `-nn` | Displays numeric addresses and ports instead of resolving names. |
| `host 10.10.14.2 and host 10.129.2.28` | Selects packets exchanged between these two hosts. |

We must replace the addresses and interface with those used by our actual connection. A routed VPN connection may use `tun0` instead of `eth0`.

**Tcpdump observes traffic; it does not initiate the service connection.**

### Nc

In another terminal, we connect to the SMTP service using Netcat:

```bash
nc -nv 10.129.2.28 25
```

| Component | Purpose |
|---|---|
| `nc` | Starts Netcat. |
| `-n` | Disables name resolution. |
| `-v` | Enables verbose connection information. |
| `10.129.2.28` | The target address. |
| `25` | The destination TCP port. |

The supplied example returns:

```text
Connection to 10.129.2.28 port 25 [tcp/*] succeeded!
220 inlane ESMTP Postfix (Ubuntu)
```

We can read the full greeting directly, including information omitted from Nmap's summary.

For this SMTP connection, we can send `QUIT` to end the session, or use `Ctrl+C` to stop Netcat.

### Tcpdump — Captured Traffic

The supplied capture shows five steps:

| Step | Direction | Flags | Meaning |
|---|---|---|---|
| 1 | Our machine → server | `[S]` | SYN: request a connection. |
| 2 | Server → our machine | `[S.]` | SYN-ACK: acknowledge and accept the request. |
| 3 | Our machine → server | `[.]` | ACK: complete the handshake. |
| 4 | Server → our machine | `[P.]` | PSH-ACK: carry the SMTP greeting and acknowledge prior TCP data. |
| 5 | Our machine → server | `[.]` | ACK: acknowledge receipt of the greeting bytes. |

In tcpdump's notation:

- **`S`** means SYN.
- **`.`** means ACK.
- **`P`** means PSH.

The first three packets establish the TCP connection. The fourth carries application data:

```text
220 inlane ESMTP Postfix (Ubuntu)
```

**PSH is not a special banner flag.** It relates to delivering TCP data promptly to the receiving application, and application data can also arrive without PSH.

**ACK does not mean that the server has finished sending everything.** It acknowledges received bytes. The final ACK in this example confirms receipt of the banner data; it does not close the connection.

Manual banner grabbing helps us examine the service's actual response and interpret details that automated summaries may leave out.