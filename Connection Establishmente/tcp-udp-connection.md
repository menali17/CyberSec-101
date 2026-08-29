# TCP/UDP Connections

**TCP (Transmission Control Protocol)** and **UDP (User Datagram Protocol)** are transport protocols used to transmit data over networks.

The main difference is the trade-off between **reliability and speed**.

| TCP                 | UDP                          |
| ------------------- | ---------------------------- |
| Connection-oriented | Connectionless               |
| Reliable            | No delivery guarantee        |
| Error recovery      | No retransmission by default |
| Slower              | Faster                       |
| Uses segments       | Uses datagrams               |
| Web pages, email    | Streaming, gaming            |

---

# TCP

**TCP (Transmission Control Protocol)** is a **connection-oriented** protocol.

Before transmitting application data, a connection is established between the sender and receiver.

TCP focuses on **reliable and ordered delivery**.

Main characteristics:

* Connection-oriented.
* Reliable data transmission.
* Detects missing data.
* Can retransmit missing data.
* Maintains the correct order of transmitted data.
* Uses acknowledgments.
* More overhead than UDP.

TCP data units are called **segments**.

### TCP Segment

A TCP segment contains a **header** and a **payload**.

Important TCP header fields include:

* **Source Port** → identifies the sending application.
* **Destination Port** → identifies the receiving application.
* **Sequence Number** → identifies the order of transmitted data.
* **Acknowledgment Number** → confirms received data.
* **Control Flags** → control the state and behavior of the connection.
* **Window Size** → indicates how much data the receiver can accept.
* **Checksum** → detects transmission errors.
* **Urgent Pointer** → identifies urgent data.

The TCP segment is encapsulated inside an **IP packet**.

`Application Data → TCP Segment → IP Packet`

---

# UDP

**UDP (User Datagram Protocol)** is a **connectionless** protocol.

Unlike TCP, UDP does not establish a connection before transmitting data.

UDP data units are called **datagrams**.

Main characteristics:

* Connectionless.
* No connection establishment.
* No guarantee of delivery.
* No guarantee that packets arrive in order.
* No retransmission of missing data by UDP itself.
* Lower overhead.
* Faster than TCP.

Common use cases include:

* Video streaming.
* Online gaming.
* Other real-time communication.

The idea is:

`TCP → Reliability over speed`

`UDP → Speed over reliability`

---

# IP Packet

An **IP packet** is the data unit used by the network layer to transmit information between hosts.

It consists of:

`IP Packet = Header + Payload`

A useful analogy is a letter:

* **Header** → Envelope containing addressing and routing information.
* **Payload** → The actual contents of the letter.

The payload can contain data from protocols such as **TCP or UDP**.

---

## IP Header

The IP header contains information required to deliver and process the packet.

| Field                            | Purpose                                                  |
| -------------------------------- | -------------------------------------------------------- |
| **Version**                      | Specifies the IP version                                 |
| **Internet Header Length (IHL)** | Specifies header size                                    |
| **Class of Service**             | Indicates transmission priority                          |
| **Total Length**                 | Total packet size                                        |
| **Identification (ID)**          | Identifies packet fragments                              |
| **Flags**                        | Controls fragmentation                                   |
| **Fragment Offset**              | Indicates fragment position                              |
| **Time to Live (TTL)**           | Limits how long the packet can travel                    |
| **Protocol**                     | Identifies the encapsulated protocol, such as TCP or UDP |
| **Checksum**                     | Detects errors in the IP header                          |
| **Source Address**               | Sender's IP address                                      |
| **Destination Address**          | Receiver's IP address                                    |
| **Options**                      | Optional routing information                             |
| **Padding**                      | Ensures proper header length                             |

---

# IP Identification (IP ID)

The **IP ID** field is used to identify fragments belonging to the same IP packet.

It is a **16-bit field**, meaning its value ranges from:

`0 – 65535`

IP IDs can also provide useful information during network analysis.

Example:

```text
10.129.1.100 → ID 1337
10.129.1.100 → ID 1338
10.129.1.100 → ID 1339

10.129.2.200 → ID 1340
10.129.2.200 → ID 1341
10.129.2.200 → ID 1342
```

Even though the source IP addresses are different, the continuous IP IDs can strongly indicate that the packets originate from the **same host using multiple IP addresses**.

This can be useful during **network sniffing and host identification**.

---

# Time to Live (TTL)

**TTL (Time to Live)** limits how long an IP packet can remain in the network.

Each router that forwards the packet decreases the TTL:

`TTL = TTL - 1`

Example:

```text
Host
TTL 3
  ↓
Router 1
TTL 2
  ↓
Router 2
TTL 1
  ↓
Router 3
TTL 0 → Packet dropped
```

When TTL reaches `0`, the router drops the packet and typically sends an:

`ICMP Time Exceeded`

message back to the sender.

This behavior is important for tools such as **traceroute**.

---

# IP Record-Route

The **Record-Route (RR)** field can record IP addresses of devices that a packet passes through.

Example command:

```bash
ping -c 1 -R 10.129.143.158
```

The resulting output can contain the route followed by the packet:

```text
10.10.14.38
10.129.0.1
10.129.143.158
...
```

This provides information about devices involved in the route between the source and destination.

---

# Traceroute

**Traceroute** is used to discover the path packets take to reach a destination.

The process described in this section relies on manipulating the **TTL** field.

### Basic Process

**1. Send a packet with TTL = 1**

```text
Source → Router 1
          TTL = 0
```

Router 1 drops the packet and returns:

`ICMP Time Exceeded`

The sender now knows Router 1's IP address.

**2. Send another packet with TTL = 2**

```text
Source → Router 1 → Router 2
                       TTL = 0
```

Router 2 responds with:

`ICMP Time Exceeded`

**3. Increase TTL again**

The process continues:

```text
TTL 1 → Router 1
TTL 2 → Router 2
TTL 3 → Router 3
TTL 4 → ...
```

until the destination is reached.

For a TCP-based traceroute, reaching the target may produce:

`TCP SYN/ACK`

or:

`TCP RST`

At this point, the route to the destination has been traced.

---

## UDP Traceroute

Traceroute can also use **UDP datagrams**.

According to the HTB material, UDP is generally used by traceroute on Unix hosts.

When the UDP datagram reaches the target, the target can respond with:

`ICMP Destination Unreachable`

specifically:

`Port Unreachable`

This indicates that the packet successfully reached the destination.

---

# IP Payload

The **IP Payload**, also called **IP Data**, contains the actual data transported by the IP packet.

For example:

```text
IP Packet
│
├── IP Header
│
└── Payload
     │
     └── TCP Segment
```

or:

```text
IP Packet
│
├── IP Header
│
└── Payload
     │
     └── UDP Datagram
```

This represents **encapsulation** between the network and transport layers.

---

# Blind Spoofing

**Blind Spoofing** is a data manipulation attack in which an attacker sends forged network information **without seeing the responses returned by the target**.

The attacker can manipulate fields such as:

* Source IP address.
* Destination information.
* Source/Destination ports.
* TCP sequence information.

A particularly important TCP value is the:

**ISN — Initial Sequence Number**

The ISN specifies the sequence number of the first TCP packet in a connection.

Conceptually:

```text
Attacker
   │
   │ Forged TCP packet
   │ Fake source information
   │ Manipulated ISN
   ↓
Target
```

Because the attacker cannot see the responses, the attacker must predict or manipulate the information necessary for the attack.

Blind spoofing can be used to interfere with the integrity of network connections or disrupt communication between devices.

---

# Quick Reference

**TCP**
→ Connection-oriented, reliable, ordered transmission.

**UDP**
→ Connectionless, faster, no delivery guarantee.

**Segment**
→ TCP data unit.

**Datagram**
→ UDP data unit.

**IP Packet**
→ Header + Payload.

**IP ID**
→ 16-bit field used to identify packet fragments.

**TTL**
→ Limits packet lifetime; decreases at each router.

**ICMP Time Exceeded**
→ Usually generated when TTL reaches zero.

**Record-Route**
→ Records IP addresses along a packet's route.

**Traceroute**
→ Discovers network paths by progressively increasing TTL.

**UDP Traceroute**
→ Destination can respond with ICMP Port Unreachable.

**ISN**
→ Initial Sequence Number of a TCP connection.

**Blind Spoofing**
→ Forging network information without seeing the target's responses.

---

## Key Takeaway

**TCP prioritizes reliable and ordered communication, while UDP prioritizes speed and low overhead. Both can be encapsulated inside IP packets, whose header fields—such as IP ID and TTL—provide important information for network communication and analysis. Understanding these fields also explains how tools such as traceroute work and how techniques such as IP spoofing can manipulate network traffic.**
