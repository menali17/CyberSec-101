# Introduction to Nmap

---

**Network Mapper (`Nmap`)** is an open-source tool used for network discovery and security auditing.

It sends probes to targets and analyzes their responses to help us identify:

- **Available hosts** on a network.
- **Open ports** and accessible network services.
- **Service names and versions**, when detectable.
- **Operating systems**, based on characteristics of network responses.
- **Signs of packet filtering**, which help us evaluate firewall behavior.

Nmap results require interpretation. Some findings are estimates, and filtering or limited responses can prevent accurate identification.

---

## Use Cases

Network administrators and security professionals use Nmap to:

- **Map networks:** discover hosts and exposed services.
- **Audit security:** identify unnecessary exposure and potential weaknesses.
- **Support penetration tests:** gather information that guides further investigation.
- **Evaluate firewall and IDS configurations:** observe traffic filtering and, with access to monitoring systems, whether scans trigger detection.
- **Investigate connectivity:** determine which ports and protocols are accessible.
- **Analyze responses:** infer how hosts and network controls handle probes.
- **Support vulnerability assessment:** use service information and relevant scripts to investigate potential vulnerabilities.

An open port alone does not prove that a service is vulnerable. It identifies an opportunity for further enumeration.

---

## Nmap Architecture

We can organize Nmap's capabilities into five main areas:

| Capability | Purpose |
|---|---|
| **Host discovery** | Identify which hosts appear to be available. |
| **Port scanning** | Determine the state of ports, such as open, closed, or filtered. |
| **Service enumeration and detection** | Investigate the applications listening on ports and identify their versions where possible. |
| **OS detection** | Estimate the target's operating system using network fingerprinting. |
| **Nmap Scripting Engine (NSE)** | Run scripts to interact with services and perform additional discovery or security checks. |

These capabilities complement each other. Discovering an open port gives us a starting point; identifying the service behind it helps us decide how to investigate further.

---

## Syntax

The general command structure is:

```bash
nmap <scan types> <options> <target>
```

| Component | Meaning |
|---|---|
| `nmap` | Starts the tool. |
| `<scan types>` | Selects the scanning technique, such as `-sS` for a TCP SYN scan. |
| `<options>` | Controls additional behavior, such as which ports to scan. |
| `<target>` | Specifies a hostname, IP address, or supported target range. |

The angle brackets indicate placeholders. We replace them with actual arguments.

For example:

```bash
sudo nmap -sS localhost
```

- **`sudo`** gives Nmap the privileges needed for this raw-packet scan on Linux.
- **`-sS`** selects a TCP SYN scan.
- **`localhost`** targets our own machine.

---

## Scan Techniques

We can display Nmap's help with:

```bash
nmap --help
```

The scan techniques section includes:

```text
SCAN TECHNIQUES:
  -sS/sT/sA/sW/sM: TCP SYN/Connect()/ACK/Window/Maimon scans
  -sU: UDP Scan
  -sN/sF/sX: TCP Null, FIN, and Xmas scans
  --scanflags <flags>: Customize TCP scan flags
  -sI <zombie host[:probeport]>: Idle scan
  -sY/sZ: SCTP INIT/COOKIE-ECHO scans
  -sO: IP protocol scan
  -b <FTP relay host>: FTP bounce scan
```

Different techniques use different probes and interpret responses differently. They do not all answer the same question: for example, a TCP ACK scan helps investigate filtering rather than directly identify open ports.

**TCP SYN scanning (`-sS`)** is a common technique and is Nmap's default TCP scan when the necessary raw-packet privileges are available. Without those privileges, Nmap normally defaults to a **TCP connect scan (`-sT`)**.

A SYN scan does not complete the normal TCP three-way handshake:

1. We send a packet with the **`SYN`** flag.
2. If the port is open, the target usually responds with **`SYN-ACK`**.
3. The exchange is reset with **`RST`** instead of completing the connection with the final handshake ACK.

This is often called a **half-open scan**. It can be fast, but its speed depends on network conditions, target behavior, and scan settings. It can still be detected and logged.

Nmap interprets the responses as follows:

| Response to the SYN probe | Port state | Interpretation |
|---|---|---|
| `SYN-ACK` | `open` | An application is accepting TCP connections on the port. |
| `RST` | `closed` | The port is reachable, but no application appears to be listening. |
| No response after retries | `filtered` | Nmap cannot determine whether the port is open because probes or responses may be filtered or lost. |
| Certain ICMP unreachable errors | `filtered` | The error indicates that the probe is being blocked or cannot reach the destination as required. |

**`Filtered` does not mean `closed`.** It means that Nmap cannot establish whether the port is open.

For example:

```shellsession
menali@htb[/htb]$ sudo nmap -sS localhost

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-11 22:50 UTC
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000010s latency).
Not shown: 996 closed ports
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
5432/tcp open  postgresql
5901/tcp open  vnc-1

Nmap done: 1 IP address (1 host up) scanned in 0.18 seconds
```

Without an explicit port selection, this scan checks the **1,000 most common TCP ports**, rather than every possible TCP port.

The output shows four open ports and summarizes the remaining 996 as closed.

| Column | Meaning | Example |
|---|---|---|
| `PORT` | Port number and transport protocol. | `22/tcp` |
| `STATE` | The port state inferred from the scan. | `open` |
| `SERVICE` | The service name associated with the port. | `ssh` |

**The `SERVICE` column does not necessarily confirm the application actually running.** In this scan, the names are based on Nmap's port-to-service mappings. A service can run on a nonstandard port, so we need further enumeration or service detection with `-sV` to investigate what is actually listening.