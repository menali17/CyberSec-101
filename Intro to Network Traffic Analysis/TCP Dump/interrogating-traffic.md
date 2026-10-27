
# Interrogating Network Traffic With Capture and Display Filters

## Introduction

This lab focuses on using **tcpdump packet filters** to investigate network traffic stored in PCAP files.

The objective is to practice filtering network traffic and identifying relevant information about network communications.

Throughout this lab, we will use filters such as:

- `host`
- `port`
- Protocol filters
- Source and destination filters
- Logical operators

These filters allow us to reduce unnecessary traffic and focus on the packets relevant to an investigation.

### Lab Scenario

After successfully capturing network traffic for the corporation, management has requested an analysis of traffic collected during a network survey.

Our goal is to determine:

- Which servers respond to DNS requests.
- Which servers respond to HTTP and HTTPS requests.
- Which clients communicate with these servers.
- Which protocols and ports are being used.
- What additional information can be obtained from the captured traffic.

### Required Resource

The lab uses the following resource:

`TCPDump-lab-2.zip`

This archive contains the packet capture that will be analyzed throughout the exercises.

---

## Task 1 - Read a Capture Without Filters

The first task is to examine the PCAP file without applying any filters.

### Command

```bash
tcpdump -r file.pcap
```

The `-r` switch reads packets from a previously saved capture file.

This allows us to examine the traffic without performing a new capture.

### Objective

Observe the overall traffic and identify:

- Source and destination IP addresses.
- Protocols.
- Source and destination ports.
- Connection attempts.
- Communication patterns.

At this stage, the purpose is to become familiar with the captured traffic before narrowing the analysis.

---

## Task 2 - Identify the Type of Traffic

After opening the capture, determine which protocols and ports are present.

### Expected Protocols

According to the HTB lab, the capture contains significant amounts of:

- DNS
- HTTP
- HTTPS

### Common Ports

| Protocol | Port | Purpose |
|---|---|---|
| DNS | 53 | Domain name resolution |
| HTTP | 80 | Web communication |
| HTTPS | 443 | Encrypted web communication |

### Identifying Protocols

Tcpdump can display service names associated with common ports.

For example:

```text
192.168.1.10.52341 > 192.168.1.1.domain
```

The service name `domain` is associated with DNS port 53.

Alternatively, we can disable name resolution:

```bash
tcpdump -nnr file.pcap
```

This displays numerical IP addresses and port numbers.

### Filtering by Port

To focus on DNS:

```bash
tcpdump -nnr file.pcap 'port 53'
```

To focus on HTTP:

```bash
tcpdump -nnr file.pcap 'tcp port 80'
```

To focus on HTTPS over TCP:

```bash
tcpdump -nnr file.pcap 'tcp port 443'
```

**Important:** Port numbers indicate which service may be in use, but they do not guarantee the actual application protocol.

---

## Task 3 - Identify Conversations

The next task is to identify communication patterns between hosts and servers.

The objective is to determine:

1. Which hosts communicate with each other?
2. Which TCP connections are established?
3. Which ports are used by the clients and servers?
4. Which devices act as servers?
5. Which devices act as clients?

### Identifying a TCP Three-Way Handshake

A TCP connection is established through:

```text
Client                          Server
   |                               |
   | -------- SYN ---------------> |
   |                               |
   | <------- SYN, ACK ----------- |
   |                               |
   | -------- ACK ---------------> |
   |                               |
```

When examining tcpdump output, look for:

```text
Flags [S]
Flags [S.]
Flags [.]
```

These represent:

| Flag | Meaning |
|---|---|
| `[S]` | SYN |
| `[S.]` | SYN + ACK |
| `[.]` | ACK |

### Identifying the Client

The client is the device initiating the TCP connection.

It sends the initial SYN packet.

Example:

```text
192.168.1.10.49152 > 192.168.1.20.80: Flags [S]
```

In this example:

- Client: `192.168.1.10`
- Client Port: `49152`
- Server: `192.168.1.20`
- Server Port: `80`

### Identifying the Server

Servers commonly listen on well-known ports.

Examples:

| Protocol | Common Server Port |
|---|---|
| DNS | 53 |
| HTTP | 80 |
| HTTPS | 443 |

Clients usually initiate connections using dynamically selected high-numbered source ports.

Therefore, the source and destination ports can help identify which endpoint is acting as the client and which is acting as the server.

### Sequence Numbers

The HTB material recommends experimenting with absolute sequence numbers using:

```bash
tcpdump -nnSr file.pcap
```

The `-S` switch displays absolute TCP sequence numbers.

Sequence and acknowledgment numbers help analysts follow the exchange of TCP segments.

---

## Task 4 - Interpret the Capture in Depth

Once the communication patterns have been identified, examine the capture more closely.

The HTB lab asks us to determine:

- The timestamp of the first established TCP conversation.
- The IP addresses returned for `apache.org`.
- The protocol used in the first conversation.

### Identifying the First TCP Conversation

Read the capture:

```bash
tcpdump -nnr file.pcap
```

Look for the first complete TCP Three-way Handshake:

```text
SYN
SYN, ACK
ACK
```

The first field of each tcpdump packet line contains its timestamp.

Example:

```text
14:30:02.234567 IP 192.168.1.10.49152 > 192.168.1.20.80: Flags [S]
```

The timestamp is:

```text
14:30:02.234567
```

The initial SYN identifies when the connection attempt began, and the completing ACK marks establishment of the connection.

### Identifying the IP Address of apache.org

DNS responses can reveal the IP addresses associated with a domain.

To examine DNS traffic:

```bash
tcpdump -nnr file.pcap 'port 53'
```

Look for DNS queries and responses involving:

```text
apache.org
```

A DNS A record response provides an IPv4 address associated with the requested domain.

### Identifying the Protocol

Inspect the ports used in the first conversation.

For example:

- Port 80 commonly indicates HTTP.
- Port 443 commonly indicates HTTPS.
- Port 53 commonly indicates DNS.

Disabling name resolution with `-nn` makes the numerical port values visible.

---

## Task 5 - Filter DNS Traffic

The next task is to remove unrelated traffic and focus on DNS communications.

### Command

```bash
tcpdump -r file.pcap 'udp and port 53'
```

This displays UDP packets involving port 53.

### Important Consideration

DNS can use both UDP and TCP.

Therefore, filtering only UDP may exclude some DNS traffic.

To examine traffic involving port 53 regardless of the transport protocol:

```bash
tcpdump -r file.pcap 'port 53'
```

### Identifying the DNS Server

The DNS server is the device responding to DNS queries.

Example:

```text
192.168.1.10.54321 > 192.168.1.1.53:
A? apache.org.

192.168.1.1.53 > 192.168.1.10.54321:
A 192.0.2.80
```

In this illustrative example:

- DNS Client: `192.168.1.10`
- DNS Server: `192.168.1.1`
- Requested Domain: `apache.org`
- Returned IPv4 Address: `192.0.2.80`

### Identifying Requested Domains

When examining DNS queries, look for domain names included in the protocol information.

For example:

```text
A? apache.org.
```

This indicates a DNS query requesting an A record for `apache.org`.

### DNS Record Types

The lab asks us to identify the record types present in the capture.

For example:

| Record | Purpose |
|---|---|
| A | Maps a domain name to an IPv4 address |
| AAAA | Maps a domain name to an IPv6 address |
| CNAME | Specifies an alias for another domain name |
| PTR | Used for reverse DNS lookups |

The actual record types present must be determined by examining the capture.

### Using Hexadecimal and ASCII Output

Tcpdump can display packet contents in hexadecimal and ASCII:

```bash
tcpdump -Xr file.pcap 'port 53'
```

This may help reveal readable domain names contained in the captured DNS packets.

### Questions to Answer

1. Who is the DNS server for the network segment?
2. Which domain names were requested?
3. Which DNS record types appear in the capture?
4. Which host requested an A record for `apache.org`?
5. What IPv4 addresses were returned?
6. Which server responded to the query?

---

## Task 6 - Filter HTTP and HTTPS Traffic

After identifying the DNS server, investigate the web traffic present in the capture.

The objective is to determine:

- Which web servers are present.
- Which web pages were requested.
- Which HTTP request methods were used.
- Which HTTP responses appear most frequently.

### Filtering HTTP and HTTPS Ports

```bash
tcpdump -r file.pcap 'tcp port 80 or tcp port 443'
```

This displays TCP traffic involving port 80 or 443.

### Important Consideration

Web services do not always operate on their default ports.

For example, an HTTP server may use:

- 80
- 8080
- 8001
- Other configured ports

Therefore, port-based filtering should be interpreted according to the environment.

### Identifying HTTP Requests

HTTP requests may contain methods such as:

- GET
- POST
- HEAD
- PUT
- DELETE

To examine readable HTTP traffic, we can use ASCII output:

```bash
tcpdump -Ar file.pcap 'tcp port 80'
```

For example, an HTTP request may contain:

```http
GET /index.html HTTP/1.1
Host: example.com
```

The method is:

```text
GET
```

The requested resource is:

```text
/index.html
```

### Identifying HTTP Responses

HTTP responses contain status codes that indicate how the server handled a request.

Examples:

| Code | Meaning |
|---|---|
| 200 | OK |
| 301 | Moved Permanently |
| 302 | Found |
| 403 | Forbidden |
| 404 | Not Found |
| 500 | Internal Server Error |

Example:

```http
HTTP/1.1 200 OK
```

This indicates that the request was handled successfully.

### HTTPS Considerations

HTTPS protects HTTP communication using TLS.

Consequently, the contents of HTTPS requests and responses are normally encrypted.

A packet capture can still reveal connection information such as:

- Source and destination IP addresses.
- TCP ports.
- Connection establishment.
- TLS handshake information.

However, encrypted HTTP request paths and response contents are not normally readable without the necessary decryption information.

### Questions to Answer

1. Which web servers appear in the capture?
2. Which pages were requested?
3. What are the most common HTTP request methods?
4. What is the most common HTTP response?

---

## Task 7 - Analyze the Web Server

The final task is to examine the server involved in the first conversation.

The objective is to determine what information can be learned about the web server.

The HTB lab specifically asks whether we can identify the application running the server.

### Inspecting HTTP Responses

HTTP responses may contain headers that reveal information about the server.

For example:

```http
HTTP/1.1 200 OK
Server: Apache
Content-Type: text/html
```

The `Server` header may identify the web server software.

However, this information is not guaranteed to be present or accurate.

### Displaying ASCII Output

To inspect readable HTTP responses:

```bash
tcpdump -Ar file.pcap 'tcp port 80'
```

This displays the captured packet contents in ASCII.

Look for:

- HTTP response headers.
- Server identification.
- Application-related information.
- Other readable response contents.

### Important Consideration

The HTB material notes that identifying web server information using tcpdump can be difficult.

It requires manually examining the ASCII representation of HTTP responses.

Wireshark provides a more convenient graphical interface for analyzing this information, which will be explored in later sections.

---

## Tips for Traffic Analysis

The HTB section provides a set of questions that can guide a packet capture investigation.

### Questions to Ask

1. **What type of traffic is present?**
   - Which protocols are being used?
   - Which ports appear in the capture?

2. **How many conversations exist?**
   - Which hosts communicate with each other?
   - Are there multiple established connections?

3. **How many unique hosts are present?**
   - Identify the different source and destination addresses.

4. **When did the first TCP conversation begin?**
   - Look for the first complete TCP Three-way Handshake.
   - Examine the timestamps.

5. **What traffic can be filtered out?**
   - Remove unrelated protocols or communications.
   - Focus on the relevant hosts and ports.

6. **Which hosts are acting as servers?**
   - Look for responses from common service ports.
   - Examples include ports 53, 80, and 443.

7. **Which records or methods were requested?**
   - DNS A records.
   - HTTP GET and POST requests.
   - Other protocol-specific information.

These questions help organize the analysis and reduce the amount of irrelevant traffic.

---

## Command Reference

| Objective | Command |
|---|---|
| Read a PCAP | `tcpdump -r file.pcap` |
| Disable name resolution | `tcpdump -nnr file.pcap` |
| Display absolute TCP sequence numbers | `tcpdump -Sr file.pcap` |
| Filter DNS | `tcpdump -r file.pcap 'port 53'` |
| Filter UDP DNS | `tcpdump -r file.pcap 'udp and port 53'` |
| Filter HTTP | `tcpdump -r file.pcap 'tcp port 80'` |
| Filter HTTPS over TCP | `tcpdump -r file.pcap 'tcp port 443'` |
| Filter HTTP and HTTPS | `tcpdump -r file.pcap 'tcp port 80 or tcp port 443'` |
| Display ASCII output | `tcpdump -Ar file.pcap` |
| Display Hex and ASCII | `tcpdump -Xr file.pcap` |

---

## Key Takeaways

- Packet filters allow analysts to isolate relevant traffic within a PCAP.
- Reading a capture without filters helps establish an initial understanding of the traffic.
- Common ports such as 53, 80, and 443 help identify DNS and web communications.
- The TCP Three-way Handshake helps identify connection establishment and distinguish clients from servers.
- DNS requests and responses reveal queried domains and associated address records.
- HTTP traffic can reveal request methods, requested resources, and response codes when transmitted in plaintext.
- HTTPS traffic normally protects HTTP content using encryption.
- ASCII output can help identify readable information within captured packets.
- HTTP response headers may reveal web server information.
- Effective traffic analysis involves filtering, interpreting communication patterns, and documenting relevant observations.
