
# Networking Primer - Layers 1-4

## Introduction

Understanding network communication is essential for performing **Network Traffic Analysis (NTA)**.

Before analyzing captured packets, we need to understand:

- How network communication is organized into layers.
- How protocols encapsulate and transmit data.
- How devices are identified using MAC and IP addresses.
- How TCP and UDP transport application data.
- How TCP connections are established and terminated.

These concepts allow us to interpret network traffic captured using tools such as **Wireshark** and **tcpdump**.

---

## OSI and TCP/IP Models

The **OSI (Open Systems Interconnection)** and **TCP/IP (Transmission Control Protocol/Internet Protocol)** models describe how network communication takes place between devices.

The main difference between them is how they organize networking functions into layers.

### Model Comparison

| Characteristic | OSI Model | TCP/IP Model |
|---|---|---|
| Number of Layers | 7 | 4 |
| Structure | More strictly separated | More flexible |
| Purpose | Conceptual reference model | Practical networking model |
| Protocol Dependency | Protocol-independent | Based on commonly used networking protocols |

The OSI model separates networking functions into seven layers, while the TCP/IP model groups similar functions into four layers.

### Layer Mapping

| OSI Model | TCP/IP Model | Examples |
|---|---|---|
| 7. Application | Application | HTTP, FTP |
| 6. Presentation | Application | Data encoding, encryption |
| 5. Session | Application | Session management, NetBIOS |
| 4. Transport | Transport | TCP, UDP |
| 3. Network | Internet | IPv4, IPv6 |
| 2. Data Link | Link | Ethernet, MAC |
| 1. Physical | Link | Physical transmission media |

In the OSI model:

- **Layers 1–4:** Handle the transmission and delivery of data between hosts.
- **Layers 5–7:** Handle session management, data representation, and application communication.

The TCP/IP model combines OSI layers 5–7 into its Application layer and layers 1–2 into its Link layer.

**Key Concept:** The OSI model provides a conceptual framework for understanding networking, while the TCP/IP model more closely represents the protocol architecture used by modern networks.

---

## Protocol Data Units (PDUs)

A **Protocol Data Unit (PDU)** is the unit of information exchanged at a particular networking layer.

As data travels through the network stack, each layer processes the data according to its responsibilities.

Different layers use different PDU names.

### PDU Types

| OSI Layer | TCP/IP Layer | PDU |
|---|---|---|
| Application | Application | Data |
| Presentation | Application | Data |
| Session | Application | Data |
| Transport | Transport | Segment (TCP) / Datagram (UDP) |
| Network | Internet | Packet |
| Data Link | Link | Frame |
| Physical | Link | Bits |

### Encapsulation

**Encapsulation** is the process of adding protocol information to data as it moves down the networking stack.

Each layer adds the information necessary to perform its function, typically using protocol headers.

For example:

```text
Application Layer
    |
    | Application Data
    v
Transport Layer
    |
    | TCP Header + Application Data
    v
Network Layer
    |
    | IP Header + TCP Segment
    v
Data Link Layer
    |
    | Ethernet Header + IP Packet + Trailer
    v
Physical Layer
    |
    | Bits transmitted over the medium
```

The encapsulation process can include information such as:

- Source and destination MAC addresses.
- Source and destination IP addresses.
- Source and destination ports.
- Protocol identifiers.
- TCP flags.
- Sequence and acknowledgment numbers.
- Protocol-specific options.

### Decapsulation

**Decapsulation** is the reverse process.

When the receiving host processes incoming traffic, it interprets the headers and passes the encapsulated data to the appropriate higher-layer protocol.

For example:

```text
Ethernet Frame
      |
      v
    IP Packet
      |
      v
   TCP Segment
      |
      v
Application Data
```

### PDUs in Wireshark

Wireshark displays captured packet information in the order that the protocol headers are encountered during packet dissection.

For an Ethernet frame carrying IPv4 and UDP traffic, the Packet Details pane may show:

```text
Frame
 |
 +-- Ethernet II
      |
      +-- Internet Protocol Version 4
           |
           +-- User Datagram Protocol
                |
                +-- Data
```

This allows analysts to inspect protocol information at different layers.

For example, the HTB capture shows:

```text
Ethernet II
    Src: 64:e7:d8:be:c8:70
    Dst: ff:ff:ff:ff:ff:ff

IPv4
    Src: 192.168.86.140
    Dst: 192.168.86.255

UDP
    Src Port: 55864
    Dst Port: 15600

Data
    35 bytes
```

From this information, we can identify:

1. The source and destination MAC addresses.
2. The source and destination IPv4 addresses.
3. The transport protocol used (UDP).
4. The source and destination UDP ports.
5. The application payload length.

**Important:** Wireshark provides access to multiple layers of information within the same captured frame.

---

## Addressing Mechanisms

Network devices rely on addressing mechanisms to identify communication endpoints and deliver data to the appropriate destinations.

The two fundamental addressing mechanisms discussed in this section are:

- **MAC Addressing:** Used primarily for local network communication.
- **IP Addressing:** Used for logical addressing and routing between networks.

### MAC Addressing

A **Media Access Control (MAC) address** is a link-layer address used to identify network interfaces on technologies such as Ethernet.

A traditional Ethernet MAC address consists of:

- **48 bits**
- **6 octets**
- Represented in hexadecimal notation

Example:

```text
88:66:5a:11:bb:36
```

Each octet contains 8 bits and is represented by two hexadecimal digits.

MAC addresses operate at:

- **OSI Layer 2 — Data Link**
- **TCP/IP Layer 1 — Link**

They are used for local delivery of frames within a Layer 2 network.

### MAC Addresses and Routing

MAC addresses are generally used for communication within the local network.

When a host needs to communicate with a device outside its local network, the traffic is typically sent to a router or default gateway.

For example:

```text
Host A
192.168.1.10
    |
    | Ethernet frame addressed to the gateway
    v
Router
192.168.1.1
    |
    | New link-layer encapsulation
    v
Remote Host
10.0.0.20
```

The process works as follows:

1. Host A determines that the destination IP belongs to another network.
2. Host A sends the frame to the MAC address of its default gateway.
3. The router receives the frame and examines the destination IP address.
4. The router determines the next hop.
5. The router removes the incoming link-layer encapsulation.
6. The packet is encapsulated again for the outgoing interface.

**Important:** The link-layer source and destination addresses normally change when packets are forwarded through routers. The original IP source and destination addresses generally remain unchanged unless mechanisms such as NAT modify them.

---

## IP Addressing

The **Internet Protocol (IP)** is responsible for logical addressing and delivering packets across interconnected networks.

IP provides:

- Logical addressing.
- Packet forwarding and routing.
- Encapsulation of upper-layer data.
- Support for packet fragmentation, depending on the IP version.

IP is a **connectionless protocol**.

This means that IP does not establish a session before transmitting packets and does not guarantee their delivery.

Reliability, when required, is provided by upper-layer protocols such as TCP.

There are two major versions of IP:

- IPv4
- IPv6

### IPv4

**Internet Protocol Version 4 (IPv4)** uses 32-bit addresses.

An IPv4 address consists of:

- **32 bits**
- **4 octets**
- Decimal representation separated by periods

Example:

```text
192.168.86.243
```

Each octet can represent a value between `0` and `255`.

IPv4 operates at:

- **OSI Layer 3 — Network**
- **TCP/IP Layer 2 — Internet**

IPv4 addresses allow routers to determine where packets should be forwarded.

#### IPv4 Example

The HTB example shows the following network interface configuration:

```text
inet 192.168.86.243
netmask 0xffffff00
broadcast 192.168.86.255
```

In this case:

| Field | Value |
|---|---|
| IPv4 Address | 192.168.86.243 |
| Subnet Mask | 255.255.255.0 |
| CIDR Prefix | /24 |
| Broadcast Address | 192.168.86.255 |

The subnet mask determines which portion of the address identifies the network and which portion identifies the host.

### IPv6

**Internet Protocol Version 6 (IPv6)** was developed to address limitations of IPv4, particularly the limited IPv4 address space.

IPv6 uses:

- **128-bit addresses**
- **16 octets**
- Hexadecimal representation

Example:

```text
fe80::49f:e3c:bf36:9bb1
```

IPv6 significantly increases the number of available addresses.

The section highlights several IPv6 characteristics:

- A much larger address space.
- Improved multicast support.
- Support for globally routable addressing.
- IPsec support.
- A simplified base packet header.

**Note:** IPv6 supports IPsec, but this does not mean that IPv6 traffic is automatically encrypted.

### IPv6 Address Types

IPv6 uses three primary delivery types: Unicast, Anycast, and Multicast.

Unlike IPv4, IPv6 does not use broadcast addressing.

| Type | Description |
|---|---|
| Unicast | Communication directed to a single interface |
| Anycast | An address assigned to multiple interfaces, with traffic delivered to one of them |
| Multicast | Communication directed to a group of interfaces |
| Broadcast | Not supported in IPv6; multicast provides the relevant group-delivery functionality |

#### Unicast

Unicast communication involves sending data to one destination.

```text
Host A ----------> Host B
```

#### Multicast

Multicast allows a sender to transmit traffic to a group of interested receivers.

```text
                +--> Host B
                |
Host A ---------+--> Host C
                |
                +--> Host D
```

#### Anycast

With Anycast, the same address can be assigned to multiple interfaces.

Network routing determines which destination receives the traffic, usually based on the routing path.

```text
                Server A
               /
Client -------+--- Server B
               \
                Server C
```

Only one of the available destinations receives a particular packet.

Anycast is frequently associated with distributed services and load distribution.

### IPv4 vs. IPv6

| Characteristic | IPv4 | IPv6 |
|---|---|---|
| Address Size | 32 bits | 128 bits |
| Octets | 4 | 16 |
| Representation | Decimal | Hexadecimal |
| Address Example | 192.168.1.10 | 2001:db8::1 |
| Broadcast | Supported | Not supported |
| Multicast | Supported | Supported |
| Fragmentation | Routers and hosts can fragment packets | Routers do not fragment packets |

The HTB section also discusses IPv6 adoption and references Google's IPv6 adoption statistics.

The approximate 40% adoption figure mentioned in the material reflects the time when that section was written and should not be treated as a current measurement.

---

## TCP and UDP - Transport Mechanisms

The **Transport Layer** is responsible for carrying application data between communicating endpoints.

It determines how application data is prepared for transmission and how the receiving host processes it.

Two major transport protocols are:

- **TCP — Transmission Control Protocol**
- **UDP — User Datagram Protocol**

Both operate at:

- **OSI Layer 4 — Transport**
- **TCP/IP Layer 3 — Transport**

### TCP vs. UDP

| Characteristic | TCP | UDP |
|---|---|---|
| Communication | Connection-oriented | Connectionless |
| Connection Setup | Three-way handshake | No handshake at the UDP layer |
| Data Model | Byte stream | Individual datagrams |
| Reliability | Reliable delivery mechanisms | No built-in delivery guarantee |
| Ordering | Maintains byte ordering | No built-in ordering |
| Acknowledgments | Uses ACKs | No transport-layer ACKs |
| Retransmission | Supported | Not provided by UDP |
| Protocol Overhead | Higher | Lower |
| Common Examples | SSH, HTTP/1.1, FTP | DNS, VoIP, real-time traffic |

### TCP - Transmission Control Protocol

TCP is a connection-oriented transport protocol designed to provide reliable and ordered delivery of data.

Its main features include:

- Connection establishment.
- Sequence numbers.
- Acknowledgments.
- Retransmission of lost data.
- Flow control.
- Ordered delivery.
- Connection termination.

TCP treats transmitted data as a continuous byte stream.

#### TCP Reliability

TCP uses sequence and acknowledgment numbers to keep track of transmitted data.

- **Sequence Number (SEQ):** Identifies positions in the TCP byte stream.
- **Acknowledgment Number (ACK):** Identifies the next byte expected from the remote host.

If data is lost, TCP can retransmit it.

TCP also ensures that applications receive the byte stream in the correct order.

#### TCP Example - SSH

SSH uses TCP to provide an interactive remote session.

Example:

```bash
ssh user@192.168.1.10
```

When a user executes commands through SSH, TCP provides the underlying reliable byte stream needed for the session.

If TCP segments are lost during transmission, TCP can retransmit the missing data.

This prevents the application from receiving an incorrectly ordered or incomplete byte stream as though it were successfully delivered.

### UDP - User Datagram Protocol

UDP is a connectionless transport protocol.

Unlike TCP, UDP does not provide built-in mechanisms for:

- Connection establishment.
- Guaranteed delivery.
- Retransmission.
- Ordered delivery.
- Transport-layer acknowledgment.

UDP has a smaller header and lower protocol overhead.

It is useful for applications where low latency or simple message exchange is important.

#### UDP Example - DNS

A DNS query commonly uses UDP.

For example:

```text
Client ---- DNS Query ----> DNS Server

Client <--- DNS Reply ----- DNS Server
```

If a DNS query or response is lost, the application or resolver can retry the request.

The UDP protocol itself does not automatically retransmit the lost datagram.

#### UDP Example - Real-Time Communication

UDP is frequently used for real-time applications such as:

- Voice over IP.
- Online gaming.
- Live media transport.
- Other latency-sensitive communications.

In these applications, waiting for retransmission may be less desirable than continuing to process newer data.

**Important:** UDP does not guarantee that an application is unreliable. Applications can implement their own reliability and recovery mechanisms above UDP.

Also, not all video streaming uses UDP. The transport protocol depends on the specific streaming technology.

---

## TCP Three-Way Handshake

The **TCP Three-Way Handshake** is the process used to establish a TCP connection between two endpoints.

It synchronizes the initial sequence numbers and exchanges connection parameters before normal data transmission.

The process consists of three steps:

1. SYN
2. SYN, ACK
3. ACK

### TCP Flags

TCP uses control flags within its header to manage connections and data transmission.

Two important flags during connection establishment are:

- **SYN (Synchronize):** Used when establishing a connection and synchronizing sequence numbers.
- **ACK (Acknowledgment):** Indicates that the acknowledgment field is valid.

Other relevant TCP flags include:

- **FIN:** Requests an orderly shutdown of one direction of a TCP connection.
- **RST:** Resets a TCP connection.
- **PSH:** Requests prompt delivery of received data to the application.

### Step 1 - SYN

The client initiates the connection by sending a TCP segment with the SYN flag set.

```text
Client -----------------> Server
             SYN
```

This segment includes the client's initial sequence number and may advertise TCP options such as:

- Maximum Segment Size (MSS).
- Window scaling.
- Selective Acknowledgment support (SACK).
- TCP timestamps.

### Step 2 - SYN, ACK

The server responds with both the SYN and ACK flags set.

```text
Client <----------------- Server
           SYN, ACK
```

The server:

- Acknowledges the client's SYN.
- Sends its own initial sequence number.
- Advertises its TCP options.

### Step 3 - ACK

The client acknowledges the server's SYN.

```text
Client -----------------> Server
             ACK
```

The TCP connection is now established, and application data can be exchanged.

### Three-Way Handshake Diagram

```text
Client                          Server
   |                               |
   | -------- SYN ---------------> |
   |                               |
   | <------- SYN, ACK ----------- |
   |                               |
   | -------- ACK ---------------> |
   |                               |
   |       Connection Established  |
   |                               |
   | <------ Application Data ---> |
```

### Example from Wireshark

The HTB capture shows a TCP connection between:

```text
Client: 192.168.1.140
Server: 174.143.213.184
```

The client uses the source port `57678`, while the server listens on port `80` (HTTP).

The first three packets demonstrate the handshake:

```text
192.168.1.140:57678 -> 174.143.213.184:80
[SYN] Seq=0

174.143.213.184:80 -> 192.168.1.140:57678
[SYN, ACK] Seq=0 Ack=1

192.168.1.140:57678 -> 174.143.213.184:80
[ACK] Seq=1 Ack=1
```

Wireshark typically displays relative TCP sequence numbers, which explains why the initial values appear as zero in this example.

After the handshake, the client sends an HTTP request:

```http
GET /images/layout/logo.png HTTP/1.0
```

The server then sends the requested content using the established TCP connection.

### Understanding TCP Sequence and Acknowledgment Numbers

Consider this simplified example:

```text
Client                       Server

SYN Seq=0 ------------------>

       <------------------ SYN,ACK Seq=0 Ack=1

ACK Seq=1 Ack=1 ------------>
```

The SYN consumes one sequence number, even though it does not carry application data.

The acknowledgment number indicates the next sequence number the receiver expects.

During data transmission, acknowledgment numbers advance according to the number of bytes received.

**Important:** TCP acknowledgments are generally cumulative. TCP does not require one separate ACK for every individual data segment.

---

## TCP Session Teardown

After communication is complete, a TCP connection can be closed gracefully.

TCP uses the **FIN (Finish)** flag to indicate that an endpoint has finished sending data.

A graceful termination allows both endpoints to finish transmitting and receiving any remaining data.

### FIN and ACK Flags

- **FIN:** Indicates that the sender has no more data to transmit.
- **ACK:** Acknowledges previously received data or connection-control information.

TCP is full-duplex, meaning each direction of communication can be closed independently.

### TCP Connection Termination

A common graceful termination uses four logical steps:

```text
Client                          Server
   |                               |
   | -------- FIN, ACK ----------> |
   |                               |
   | <------- ACK ---------------- |
   |                               |
   | <------- FIN, ACK ----------- |
   |                               |
   | -------- ACK ---------------> |
   |                               |
   |       Connection Closed       |
```

However, the middle ACK and FIN may be combined into a single segment.

### Three-Packet Teardown

The HTB capture demonstrates the following pattern:

```text
1. FIN, ACK
2. FIN, ACK
3. ACK
```

The exchange appears as:

```text
Client                          Server
   |                               |
   | -------- FIN, ACK ----------> |
   |                               |
   | <------- FIN, ACK ----------- |
   |                               |
   | -------- ACK ---------------> |
   |                               |
```

The second packet both acknowledges the first FIN and indicates that the server has finished sending data.

This is a valid example of graceful TCP connection termination.

### Example from Wireshark

The same HTTP communication from the previous example eventually terminates.

The capture shows:

```text
192.168.1.140:57678 -> 174.143.213.184:80
[FIN, ACK]

174.143.213.184:80 -> 192.168.1.140:57678
[FIN, ACK]

192.168.1.140:57678 -> 174.143.213.184:80
[ACK]
```

This pattern indicates that both endpoints are completing the connection shutdown.

### FIN vs. RST

Another important TCP flag is RST.

| Flag | Purpose |
|---|---|
| FIN | Gracefully closes one direction of a connection |
| RST | Resets or aborts a connection |

A FIN-based teardown is associated with orderly connection termination.

An RST may appear when a connection is rejected, unexpectedly terminated, or otherwise reset.

**Important:** A TCP reset is not automatically evidence of malicious activity. It must be interpreted in the context of the communication.

---

## Analyzing TCP Traffic in Wireshark

Understanding the TCP handshake and session termination makes it easier to interpret captured traffic.

Some important fields include:

| Field | Description |
|---|---|
| Source | Sender's IP address |
| Destination | Receiver's IP address |
| Protocol | Protocol identified by Wireshark |
| Length | Captured frame length |
| Source Port | Sending application endpoint |
| Destination Port | Receiving application endpoint |
| SYN | Connection establishment flag |
| ACK | Acknowledgment flag |
| FIN | Graceful connection termination |
| RST | Connection reset |
| Seq | TCP sequence number |
| Ack | TCP acknowledgment number |
| Win | Advertised TCP receive window |
| Len | TCP payload length |

### Recognizing a Normal TCP Connection

A typical HTTP/1.0 communication might follow this sequence:

```text
1. Client -> Server    SYN
2. Server -> Client    SYN, ACK
3. Client -> Server    ACK

4. Client -> Server    HTTP GET
5. Server -> Client    ACK
6. Server -> Client    HTTP Response
7. Client -> Server    ACK

8. Client -> Server    FIN, ACK
9. Server -> Client    FIN, ACK
10. Client -> Server   ACK
```

Actual captures may contain additional packets, retransmissions, different acknowledgment patterns, or a different teardown sequence.

Understanding normal TCP behavior provides a baseline for identifying potentially suspicious communication.

---

## Key Takeaways

- The **OSI model** consists of seven layers, while the **TCP/IP model** consists of four.
- A **PDU** represents the unit of data handled by a networking layer.
- **Encapsulation** adds protocol information as data moves down the network stack.
- **Decapsulation** processes encapsulated information as received data moves up the stack.
- **MAC addresses** are used for link-layer communication.
- **IP addresses** provide logical addressing and enable routing between networks.
- **IPv4** uses 32-bit addresses, while **IPv6** uses 128-bit addresses.
- **TCP** provides reliable, ordered, connection-oriented communication.
- **UDP** provides connectionless, datagram-based communication without built-in reliability guarantees.
- The **TCP Three-Way Handshake** follows `SYN → SYN, ACK → ACK`.
- **TCP sequence and acknowledgment numbers** allow endpoints to track the transmitted byte stream.
- **FIN** is used for graceful TCP shutdown, while **RST** resets a connection.
- Understanding TCP connection establishment, data transfer, and termination is essential for analyzing packet captures in Wireshark.
