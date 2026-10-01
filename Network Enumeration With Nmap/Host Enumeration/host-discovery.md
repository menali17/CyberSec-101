# Host Discovery

---

**Host discovery** helps us identify which systems appear to be online before we investigate their ports and services.

During an internal penetration test, this gives us an initial overview of the network and helps us select targets for further enumeration.

We can discover hosts through different probes, including **ICMP, TCP, and ARP**. Their effectiveness depends on our network position, filtering rules, and how each target responds.

**A missing response does not prove that a host is offline.** The target may be active but unable or configured not to respond to our probes.

We should save scan results for comparison, documentation, and reporting. Keeping the original output also allows us to revisit information that terminal filters may hide.

---

## Scan Network Range

We can scan an entire network range using CIDR notation:

```bash
sudo nmap 10.129.2.0/24 -sn -oA tnet
```

| Argument | Description |
|---|---|
| `10.129.2.0/24` | Targets the address range from `10.129.2.0` to `10.129.2.255`. |
| `-sn` | Performs host discovery without a subsequent port scan. |
| `-oA tnet` | Saves results using `tnet` as the filename prefix. |

The `/24` prefix fixes the first 24 bits of the IPv4 address, leaving the final 8 bits to vary. This describes a range containing **256 addresses**.

The `-oA` option creates three output files:

| File | Format |
|---|---|
| `tnet.nmap` | Normal, human-readable output. |
| `tnet.xml` | XML output for processing by other tools. |
| `tnet.gnmap` | Grepable output for text filtering. |

We should use different prefixes for scans we want to preserve separately, because reusing a prefix normally overwrites the existing files.

The material filters the terminal output to display discovered addresses:

```bash
sudo nmap 10.129.2.0/24 -sn -oA tnet | grep for | cut -d" " -f5
```

Example output:

```text
10.129.2.4
10.129.2.10
10.129.2.11
10.129.2.18
10.129.2.19
10.129.2.20
10.129.2.28
```

The pipeline works as follows:

- **`|`** passes one command's standard output to the next command.
- **`grep for`** selects lines containing `for`, such as `Nmap scan report for 10.129.2.4`.
- **`cut -d" " -f5`** splits each line using spaces and extracts the fifth field.

The files created by `-oA` retain Nmap's scan results; the pipeline only filters what we display in the terminal.

**This filter assumes a particular output layout.** If Nmap displays a hostname before the IP address, the fifth field may be the hostname instead.

---

## Scan IP List

When we receive a predefined list of targets, we can store them in a text file:

```shellsession
menali@htb[/htb]$ cat hosts.lst

10.129.2.4
10.129.2.10
10.129.2.11
10.129.2.18
10.129.2.19
10.129.2.20
10.129.2.28
```

We use `-iL` to read the targets from that file:

```bash
sudo nmap -sn -oA tnet-list -iL hosts.lst
```

| Argument | Description |
|---|---|
| `-sn` | Performs host discovery without port scanning. |
| `-oA tnet-list` | Saves results in the three output formats. |
| `-iL hosts.lst` | Reads targets from `hosts.lst`. |

We can apply the same terminal filter:

```bash
sudo nmap -sn -oA tnet-list -iL hosts.lst | grep for | cut -d" " -f5
```

Example output:

```text
10.129.2.18
10.129.2.19
10.129.2.20
```

Here, **three of the seven listed hosts were detected as online**.

We cannot conclude that the remaining four are definitely offline. Possible explanations include filtering, packet loss, connectivity problems, or hosts that do not respond to the selected probes.

---

## Scan Multiple IPs

We can specify individual targets by separating their IP addresses with spaces:

```bash
sudo nmap -sn -oA tnet-multiple 10.129.2.18 10.129.2.19 10.129.2.20
```

For consecutive addresses, we can use a range within an octet:

```bash
sudo nmap -sn -oA tnet-range 10.129.2.18-20
```

Both commands target:

- `10.129.2.18`
- `10.129.2.19`
- `10.129.2.20`

The range includes both endpoints.

---

## Scan Single IP

We can also perform discovery against a single target:

```bash
sudo nmap 10.129.2.18 -sn -oA host
```

An excerpt from the supplied example shows:

```text
Nmap scan report for 10.129.2.18
Host is up (0.087s latency).
MAC Address: DE:AD:00:00:BE:EF
```

- **`Host is up`** means Nmap received evidence that the target is online.
- **Latency** indicates the observed response delay.
- **`MAC Address`** shows a link-layer address when Nmap can obtain it, typically for a directly connected Ethernet target.

**`-sn` does not select ICMP exclusively.** With sufficient privileges, default discovery for routed targets normally combines ICMP echo, ICMP timestamp, TCP SYN to port 443, and TCP ACK to port 80. Local Ethernet targets normally use ARP discovery instead.

We can explicitly select ICMP echo probes with `-PE`, but local ARP discovery still takes precedence unless disabled.

To inspect the discovery traffic:

```bash
sudo nmap 10.129.2.18 -sn -oA host-trace -PE --packet-trace
```

| Option | Description |
|---|---|
| `-PE` | Selects ICMP echo requests for IP-based discovery. |
| `--packet-trace` | Displays details of packets Nmap sends and receives. |

For a local Ethernet target, the trace may show:

```text
SENT (...) ARP who-has 10.129.2.18 tell 10.10.14.2
RCVD (...) ARP reply 10.129.2.18 is-at DE:AD:00:00:BE:EF
```

The exchange means:

1. **ARP request:** we ask which device has the target IPv4 address.
2. **ARP reply:** a device responds with the MAC address associated with it.
3. Nmap uses that reply as evidence that the target is online.

ARP operates on the local network link. It is not forwarded across routers to discover remote hosts. When accessing a target through a routed VPN, we should not expect the same ARP behavior shown in a local Ethernet example.

We can ask Nmap to explain its conclusion with `--reason`:

```bash
sudo nmap 10.129.2.18 -sn -oA host-reason -PE --reason
```

The relevant result may look like:

```text
Host is up, received arp-response.
```

**`--reason` explains the result; `--packet-trace` shows packet details.** Adding `--reason` alone does not enable packet tracing.

To disable automatic ARP discovery and observe ICMP echo probes instead:

```bash
sudo nmap 10.129.2.18 -sn -oA host-icmp -PE --packet-trace --disable-arp-ping
```

The relevant exchange from the supplied example is:

```text
SENT (...) ICMP [10.10.14.2 > 10.129.2.18 Echo request (type=8/code=0) ...]
RCVD (...) ICMP [10.129.2.18 > 10.10.14.2 Echo reply (type=0/code=0) ...]
```

| Message | ICMP type | Meaning |
|---|---|---|
| Echo request | `8` | We ask the target to respond. |
| Echo reply | `0` | The target answers our request. |

Here, the echo reply provides evidence that the target is online and reachable.

**`--disable-arp-ping` disables ARP-based host discovery, not necessarily all ARP traffic.** Ethernet communication may still require address resolution to deliver IP packets.

If no echo reply arrives, we have not confirmed that the target is offline. We have only failed to discover it using this method.