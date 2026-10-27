
# Decrypting RDP Connections

## Introduction

This lab demonstrates how Wireshark can use cryptographic key material to inspect encrypted Remote Desktop Protocol (RDP) traffic.

The investigation focuses on RDP communications captured during an incident response investigation.

The lab objectives are to:

- Open and analyze an RDP packet capture.
- Identify encrypted RDP communications.
- Filter traffic using TCP port 3389.
- Import the provided RSA server key into Wireshark.
- Inspect the decrypted RDP traffic.
- Identify the host that initiated the connection.
- Investigate the user account associated with the RDP session.

---

## Lab Scenario

During the investigation of Bob's computer, the Incident Response team captured RDP traffic between Bob's host and another computer on the corporate network.

During forensic examination, an RDP-related key was discovered on Bob's host.

The team wants to determine whether this key can be used to inspect the encrypted network communication.

### Provided Resources

The optional resource archive is:

`RDP-analysis.zip`

The lab uses two important files:

| File | Purpose |
|---|---|
| `rdp.pcapng` | Contains the captured RDP communication |
| `server.key` | RSA key provided for TLS decryption |

---

## Remote Desktop Protocol (RDP)

RDP allows users to access and interact with a remote computer through a graphical desktop session.

### Common Port

```text
TCP 3389
```

RDP communication can be protected using TLS encryption.

When TLS is used, the application data is encrypted during transmission.

This prevents direct inspection of the protected contents unless suitable decryption information is available.

---

## Task 1 - Open the RDP Capture

The first task is to open the provided packet capture in Wireshark.

### Procedure

1. Extract `RDP-analysis.zip`.
2. Open Wireshark.
3. Select **File → Open**.
4. Locate `rdp.pcapng`.
5. Open the capture.

The Packet List pane displays the captured network traffic.

---

## Task 2 - Analyze the Traffic

The next task is to identify RDP communication within the capture.

### Initial Display Filter

```text
rdp
```

The HTB example shows no matching packets when this filter is first applied.

### Explanation

The RDP communication is protected by TLS.

Wireshark identifies the encrypted traffic but cannot initially display the encapsulated RDP protocol data.

### Filtering by Port

RDP commonly uses TCP port 3389.

To locate the relevant communication, apply:

```text
tcp.port == 3389
```

This displays TCP packets where port 3389 appears as the source or destination port.

### HTB Findings

The capture shows communication between:

```text
10.129.43.27
10.129.43.29
```

The traffic includes TLSv1.2 packets.

### Important Concept

A port-based filter can reveal a connection even when its application data is encrypted.

However, identifying the connection does not automatically reveal its contents.

---

## Task 3 - Import the RDP Key

The next task is to configure Wireshark to use the provided RSA server key.

### Procedure

Open:

**Edit → Preferences → Protocols → TLS**

Locate the **RSA keys list** and select **Edit**.

Add a new key entry.

### RSA Key Configuration

| Setting | Value |
|---|---|
| IP Address | `10.129.43.29` |
| Port | `3389` |
| Protocol | `tpkt` or blank |
| Key File | `server.key` |

### Configuration Steps

1. Click the `+` button to add a new key.
2. Enter the RDP server IP address.
3. Enter TCP port `3389`.
4. Set the protocol field to `tpkt` or leave it blank.
5. Browse to the `server.key` file.
6. Save the configuration.
7. Refresh the packet capture.

---

## Inspecting the Decrypted RDP Traffic

After importing the key, apply the RDP display filter again:

```text
rdp
```

### Expected Result

In the HTB lab, Wireshark can now recognize and display the RDP traffic.

The decrypted information allows the analyst to inspect protocol details that were previously unavailable.

### Available Analysis Techniques

Once the relevant traffic has been decrypted and decoded, Wireshark can be used to:

- Examine RDP protocol information.
- Analyze network conversations.
- Follow TCP streams.
- Inspect available packet contents.
- Investigate relevant session activity.

### Important Consideration

Successful TLS decryption depends on the cryptographic properties of the captured session and the available key material.

The RSA key method demonstrated in this lab is not applicable to every TLS connection.

---

## Analyzing the RDP Session

After configuring decryption, the lab asks us to determine information about the communication.

### Question 1 - Which Host Initiated the RDP Session?

The HTB walkthrough identifies packet number `8` as the first packet of the TCP Three-way Handshake.

The connection was initiated by:

```text
10.129.43.27
```

### Server Address

The RDP server is:

```text
10.129.43.29
```

### Identifying the Initiator

The host that sends the initial TCP SYN packet is the connection initiator.

A typical TCP Three-way Handshake is:

```text
Client                        Server
  |                              |
  | ---------- SYN ------------> |
  |                              |
  | <------- SYN, ACK ---------- |
  |                              |
  | ---------- ACK ------------> |
  |                              |
```

The initiating host is identified by examining the source address of the initial SYN packet.

---

## Question 2 - Which User Account Was Used?

The lab asks us to identify the user account associated with the RDP connection.

### Display Filter

```text
tcp.port == 3389
```

### Investigation Procedure

1. Apply the TCP port 3389 display filter.
2. Examine the relevant packets.
3. Locate the record identified as **Ignored Unknown Record** in the HTB example.
4. Examine its ASCII representation.
5. Identify the username present in the captured data.

### Important Note

The written lab instructions do not explicitly provide the username.

The answer must be obtained by examining the relevant packet contents or the original capture.

---

## Key Wireshark Filters

| Filter | Purpose |
|---|---|
| `rdp` | Displays traffic recognized as RDP |
| `tcp.port == 3389` | Displays TCP packets associated with port 3389 |
| `tcp` | Displays TCP traffic |

---

## Investigation Workflow

The process demonstrated by this lab can be summarized as follows:

1. **Open the capture**
   - Load `rdp.pcapng`.

2. **Identify the relevant traffic**
   - Attempt the `rdp` filter.
   - Use `tcp.port == 3389` to locate the encrypted communication.

3. **Identify the endpoints**
   - Determine the RDP client and server.

4. **Import the provided key**
   - Configure Wireshark's TLS RSA keys list.

5. **Inspect the decrypted traffic**
   - Reapply the `rdp` display filter.

6. **Analyze the connection**
   - Inspect the TCP handshake.
   - Determine the initiating host.
   - Examine available protocol and authentication information.

7. **Document the findings**
   - Record relevant hosts, packets, and observations.

---

## Key Takeaways

- Wireshark can analyze encrypted network traffic when compatible decryption material is available.
- RDP commonly uses TCP port 3389.
- RDP communication may be protected by TLS.
- Encrypted application data may not initially be recognized by protocol-specific display filters.
- `tcp.port == 3389` helps identify RDP-associated connections even when their payloads are encrypted.
- Wireshark supports configuring RSA private keys for compatible TLS sessions.
- The HTB lab demonstrates importing a `server.key` file to decode captured RDP traffic.
- The initial TCP SYN identifies the host that initiated the connection.
- The RDP client identified in this lab is `10.129.43.27`.
- The RDP server identified in this lab is `10.129.43.29`.
- Packet contents can provide information about user accounts involved in remote connections.
- Traffic decryption can reveal additional evidence during incident response investigations.
