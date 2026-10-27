
# Fundamentals Lab

## Introduction

The purpose of this lab is to practice the fundamental features of **tcpdump** and become familiar with capturing, displaying, saving, and reading network traffic through the terminal.

The lab focuses on:

- Verifying that tcpdump is installed.
- Listing available network interfaces.
- Starting a network traffic capture.
- Using basic tcpdump switches.
- Modifying packet output.
- Saving captured traffic to PCAP files.
- Reading and inspecting existing PCAP files.

Network traffic capture is commonly used to examine communications involving specific hosts or servers.

By monitoring the packets sent and received, analysts can investigate network behavior, establish baselines, and identify potentially suspicious activity.

Filters and traffic patterns can then be used to identify relevant packets during an investigation.

---

## Lab Scenario

As the new network administrator for a corporation, we have been tasked with capturing network traffic to help establish and validate the organization's network baseline.

The objective is to perform a small capture of traffic from the local broadcast domain and verify that the capture device works correctly.

Before beginning, we must:

1. Ensure tcpdump is installed.
2. Identify available capture interfaces.
3. Verify our ability to capture network traffic.
4. Modify how captured packets are displayed.
5. Save the traffic to a PCAP file.
6. Read and analyze the saved capture.

---

## Task 1 - Validate Tcpdump Installation

Before capturing network traffic, verify that tcpdump is installed.

### Command

```bash
which tcpdump
```

The `which` command searches for the specified executable in the directories listed in the system's PATH.

If tcpdump is installed and available through the PATH, the command displays its location.

Example:

```text
/usr/sbin/tcpdump
```

---

## Task 2 - Start a Capture

Before starting a capture, identify the available network interfaces.

### Step 1 - List Available Interfaces

```bash
tcpdump -D
```

The `-D` switch displays the interfaces available for packet capture.

### Step 2 - Select an Interface

```bash
tcpdump -i <interface>
```

The `-i` switch specifies which interface tcpdump should monitor.

Example:

```bash
sudo tcpdump -i eth0
```

This starts capturing network traffic from the `eth0` interface.

Tcpdump displays the captured packets in the terminal until the capture is interrupted or a configured limit is reached.

---

## Task 3 - Utilize Basic Capture Switches

The next task involves modifying how captured packets are displayed.

The objective is to:

- Enable verbose output.
- Display packet contents in ASCII.
- Display packet contents in hexadecimal.

### Command

```bash
sudo tcpdump -i eth0 -vX
```

### Switches

| Switch | Description |
|---|---|
| `-i eth0` | Selects the network interface |
| `-v` | Enables verbose output |
| `-X` | Displays packet contents in hexadecimal and ASCII |

### Additional Challenge

The HTB section also suggests experimenting with:

- Disabling name resolution.
- Displaying relative TCP sequence numbers.

Example:

```bash
sudo tcpdump -i eth0 -nnvX
```

The `-nn` switch disables hostname and port-name resolution.

Tcpdump normally displays relative TCP sequence numbers for established connections unless absolute numbering is requested using `-S`.

---

## Task 4 - Save a Capture to a PCAP File

The next task is to capture network traffic and save it to a PCAP file.

PCAP files allow traffic to be stored for later examination.

### Command

```bash
sudo tcpdump -i eth0 -nvw ~/capture.pcap
```

### Switches

| Switch | Description |
|---|---|
| `-i eth0` | Selects the network interface |
| `-n` | Disables address-to-name resolution |
| `-v` | Requests verbose output |
| `-w` | Writes captured packets to a file |

### Output File

```text
~/capture.pcap
```

The `-w` option saves the captured packets to the specified file.

Unlike a normal terminal capture, tcpdump does not continuously display packet summaries when writing packets directly to a file.

The capture can be stopped using:

```text
Ctrl + C
```

The resulting PCAP file can be used to examine the captured network traffic.

**Important:** The saved PCAP contains captured packet data, not a text copy of the verbose terminal output.

---

## Task 5 - Read a PCAP File

The final task involves reading an existing PCAP file.

The objective is to:

- Disable hostname resolution.
- Disable port-name resolution.
- Display absolute TCP sequence numbers.
- Display packet contents in hexadecimal and ASCII.

For this lab, use the PCAP file created in Task 4.

### Command

```bash
tcpdump -nnSXr ~/capture.pcap
```

### Switches

| Switch | Description |
|---|---|
| `-nn` | Disables hostname and port-name resolution |
| `-S` | Displays absolute TCP sequence numbers |
| `-X` | Displays hexadecimal and ASCII output |
| `-r` | Reads packets from a PCAP file |

### Absolute Sequence Numbers

The `-S` switch instructs tcpdump to display absolute TCP sequence numbers instead of relative values.

For example:

```text
Relative:
seq 1:135

Absolute:
seq 2387613954:2387614088
```

Both representations describe positions in the TCP byte stream.

Relative sequence numbers simplify analysis by using smaller values, while absolute sequence numbers show the actual values used by TCP.

### Reading the Capture

After executing the command, tcpdump reads the saved packets and displays the information using the selected output options.

The capture can be inspected multiple times using different switches.

---

## Lab Command Reference

| Task | Command |
|---|---|
| Check installation | `which tcpdump` |
| List interfaces | `tcpdump -D` |
| Start capture | `sudo tcpdump -i eth0` |
| Verbose, Hex and ASCII | `sudo tcpdump -i eth0 -vX` |
| Disable name resolution | `sudo tcpdump -i eth0 -nn` |
| Save to PCAP | `sudo tcpdump -i eth0 -nvw ~/capture.pcap` |
| Read PCAP | `tcpdump -r ~/capture.pcap` |
| Read with absolute sequences, Hex and ASCII | `tcpdump -nnSXr ~/capture.pcap` |

---

## Key Takeaways

- `which tcpdump` verifies whether the tcpdump executable is available.
- `-D` lists available network interfaces.
- `-i` selects the network interface for packet capture.
- `-v` increases output verbosity.
- `-X` displays packet data in hexadecimal and ASCII.
- `-nn` disables hostname and port-name resolution.
- `-S` displays absolute TCP sequence numbers.
- `-w` saves captured packets to a PCAP file.
- `-r` reads packets from an existing PCAP file.
- Tcpdump switches can be combined to customize the capture and output.
- PCAP files allow captured network traffic to be examined later.
- Familiarity with these commands is essential for analyzing network communications and establishing network baselines.
