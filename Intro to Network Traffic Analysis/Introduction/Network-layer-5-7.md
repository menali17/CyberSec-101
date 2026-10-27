
# Networking Primer - Layers 5-7

## Introduction

The upper layers of the OSI model are responsible for managing communication sessions, handling data representation, and providing network services to applications.

These layers include:

- **Layer 5 - Session:** Manages communication sessions between applications.
- **Layer 6 - Presentation:** Handles data representation, encoding, and encryption.
- **Layer 7 - Application:** Provides network services used by applications.

In the TCP/IP model, these functions are grouped into the **Application Layer**.

Understanding application-layer protocols is essential for analyzing network traffic because they define how applications exchange information.

This section focuses on four important protocols:

- HTTP
- HTTPS
- FTP
- SMB

---

## HTTP - Hypertext Transfer Protocol

**HTTP (Hypertext Transfer Protocol)** is a stateless application-layer protocol used for communication between web clients and servers.

HTTP traditionally uses TCP as its transport protocol and transfers data without encryption.

### Characteristics

- **Layer:** Application Layer (OSI Layer 7).
- **Default Port:** TCP 80.
- **Alternative Port:** TCP 8000 and other configured ports.
- **Communication:** Request-response model.
- **Encryption:** None in standard HTTP.
- **State:** Stateless.

HTTP allows clients to request resources such as:

- HTML pages.
- Images.
- Documents.
- Videos.
- Other web resources.

### Stateless Communication

HTTP is considered **stateless** because each request is processed independently.

The protocol itself does not require the server to remember previous requests from the same client.

Applications can maintain state through mechanisms such as cookies and session tokens.

### HTTP Request-Response Model

HTTP follows a client-server communication model:

```text
Client                              Server
   |                                   |
   | -------- HTTP Request ----------> |
   |                                   |
   | <------- HTTP Response ---------- |
   |                                   |
```

For example, a client requesting a webpage might send:

```http
GET /index.html HTTP/1.1
Host: example.com
```

The server can respond with:

```http
HTTP/1.1 200 OK
Content-Type: text/html

<html>
    <body>Hello World</body>
</html>
```

Because regular HTTP does not encrypt the communication, an analyst with access to the relevant packet capture may be able to inspect the request and response contents.

### HTTP Methods

HTTP uses methods to indicate the action a client wants to perform on a resource.

| Method | Description |
|---|---|
| GET | Requests a resource from the server |
| HEAD | Requests response headers without the response body |
| POST | Submits data to the server for processing |
| PUT | Creates or replaces a resource at the specified URI |
| DELETE | Requests the removal of a resource |
| TRACE | Requests that the server echo the received request for diagnostic purposes |
| OPTIONS | Requests information about supported communication options |
| CONNECT | Establishes a tunnel through a proxy |

#### GET

The `GET` method retrieves information or resources from a server.

Example:

```http
GET /Webserver/index.html HTTP/1.1
Host: 10.1.1.1
```

This requests the `index.html` resource from the specified web server.

GET is one of the most commonly used HTTP methods.

#### HEAD

The `HEAD` method functions similarly to GET, but the server does not include the response body.

Example:

```http
HEAD /index.html HTTP/1.1
Host: example.com
```

It is useful for inspecting:

- Response headers.
- Content type.
- Resource metadata.
- Server responses and availability.

#### POST

The `POST` method submits data to a server.

Example:

```http
POST /login HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded

username=admin&password=example
```

POST is commonly used for:

- Form submissions.
- Authentication requests.
- Uploading data.
- Creating resources.
- Submitting information to applications.

The actual operation depends on the server implementation.

#### PUT

The `PUT` method creates or replaces the representation of a resource at a specified URI.

Example:

```http
PUT /files/report.txt HTTP/1.1
Host: example.com
Content-Type: text/plain

Updated report contents
```

If the resource does not exist, the server may create it.

If the resource already exists, the server may replace its representation.

The key difference between POST and PUT is that PUT targets the resource identified by the request URI, while POST asks the server to process the submitted data according to that resource's behavior.

#### DELETE

The `DELETE` method requests the removal of a resource.

Example:

```http
DELETE /files/report.txt HTTP/1.1
Host: example.com
```

The server determines whether the requested operation is permitted.

#### TRACE

The `TRACE` method can be used for diagnostic purposes.

When supported, the server returns information about the request it received.

This can help identify how requests are processed by intermediate systems.

TRACE is frequently disabled due to security considerations.

#### OPTIONS

The `OPTIONS` method requests information about the communication options supported by a server or resource.

Example:

```http
OPTIONS / HTTP/1.1
Host: example.com
```

A response may contain:

```http
Allow: GET, HEAD, POST, OPTIONS
```

This indicates the methods advertised as supported for the requested resource.

#### CONNECT

The `CONNECT` method is primarily associated with HTTP proxies.

It allows a client to establish a tunnel through a proxy to another server.

This mechanism is commonly used when accessing HTTPS destinations through an HTTP proxy.

### Required and Optional Methods

The HTB material distinguishes two categories:

**Required methods:**

- GET
- HEAD

**Optional methods:**

- POST
- PUT
- DELETE
- TRACE
- OPTIONS
- CONNECT

Not every web server or resource supports every method.

For example, a read-only website may allow users to retrieve information without permitting resource creation, modification, or deletion.

---

## HTTPS - HTTP Secure

**HTTPS (Hypertext Transfer Protocol Secure)** is HTTP communication protected by **TLS (Transport Layer Security)**.

TLS provides cryptographic protection for data exchanged between a client and server.

### Characteristics

- **Layer:** Application Layer.
- **Default Port:** TCP 443.
- **Alternative Port:** TCP 8443 and other configured ports.
- **Encryption:** TLS.
- **Purpose:** Secure HTTP communication.

HTTPS provides three important security properties:

1. **Confidentiality:** Protects the content of communications from unauthorized observation.
2. **Integrity:** Helps detect unauthorized modification of transmitted data.
3. **Authentication:** Allows clients to verify the server's identity through certificate validation.

### HTTP vs. HTTPS

| Characteristic | HTTP | HTTPS |
|---|---|---|
| Default Port | 80/TCP | 443/TCP |
| Encryption | None | TLS |
| Data Visibility | Plaintext | Encrypted application data |
| Confidentiality | Not provided | Provided by TLS |
| Integrity Protection | Not provided by HTTP | Provided by TLS |
| Server Authentication | Not provided by HTTP | Certificate-based authentication |

HTTPS protects web communications from passive eavesdropping and helps prevent man-in-the-middle attacks when TLS is correctly configured and certificates are properly validated.

### TLS Handshake

Before encrypted application data can be exchanged, the client and server must establish the necessary cryptographic parameters.

For traditional HTTPS over TCP, the process begins with a TCP connection.

```text
Client                              Server
   |                                   |
   | -------- SYN -------------------> |
   | <------- SYN, ACK --------------- |
   | -------- ACK -------------------> |
   |                                   |
   |        TCP Established            |
   |                                   |
   | -------- TLS ClientHello -------> |
   | <------- TLS ServerHello -------- |
   |                                   |
   |      TLS Handshake Continues      |
   |                                   |
   |       Secure Communication        |
   |                                   |
   | <------ Encrypted HTTP Data -----> |
```

The `ClientHello` message initiates the TLS handshake.

During the handshake, the endpoints establish or negotiate information such as:

- TLS protocol version.
- Supported cryptographic algorithms.
- Key exchange parameters.
- Session-related parameters.
- Server authentication information.
- Cryptographic keys for protecting communications.

The server usually presents an **X.509 certificate**, which the client validates to authenticate the server.

After the handshake completes, HTTP data is protected using the negotiated cryptographic parameters.

### TLS Handshake - HTB Overview

The HTB section summarizes TLS establishment through the following general operations:

1. Client and server exchange hello messages.
2. Cryptographic parameters are negotiated.
3. Certificate and authentication information is exchanged.
4. The endpoints establish shared cryptographic key material.
5. Negotiated security parameters are activated.
6. The endpoints verify that the handshake completed successfully.

**Important:** This is a conceptual overview. The HTB material also describes premaster and master secrets associated with older TLS handshake designs. Modern TLS 1.3 uses a different key schedule and handshake structure.

### HTTPS in Wireshark

In the HTB example, the client establishes a TCP connection to port `443`.

After the TCP three-way handshake, the packet capture shows TLS handshake messages, followed by encrypted application data.

Typical packet sequence:

```text
TCP     SYN
TCP     SYN, ACK
TCP     ACK

TLS     ClientHello
TLS     ServerHello
TLS     Handshake Messages
TLS     Application Data
```

Once encryption is established, Wireshark normally displays the protected content as TLS application data rather than readable HTTP messages.

However, some connection metadata can remain visible, such as:

- Source and destination IP addresses.
- TCP source and destination ports.
- Packet sizes.
- Communication timing.
- TCP flags.
- Some TLS handshake metadata.

**Important:** Encryption protects the application content, but it does not conceal every characteristic of a network connection.

### Additional Note - HTTP/3

Traditional HTTPS commonly uses HTTP over TLS over TCP.

However, **HTTP/3 uses QUIC over UDP**, with TLS 1.3 integrated into QUIC.

Therefore, HTTPS does not necessarily imply TCP in all modern implementations.

---

## FTP - File Transfer Protocol

**FTP (File Transfer Protocol)** is an application-layer protocol used to transfer files between clients and servers.

FTP supports file uploads, downloads, directory navigation, and other file management operations.

### Characteristics

- **Layer:** Application Layer.
- **Transport:** TCP.
- **Control Port:** TCP 21.
- **Data Port:** TCP 20 in traditional active-mode FTP.
- **Authentication:** Username/password or anonymous access, depending on configuration.
- **Encryption:** None in traditional FTP.

FTP is considered insecure because standard FTP transmits commands, credentials, and file contents without encryption.

Secure alternatives include SFTP and FTPS.

### FTP Connections

Unlike many other application protocols, FTP separates communication into two channels.

#### Control Connection

The control connection is used to exchange commands and responses.

Typically uses:

```text
TCP Port 21
```

Commands include:

- USER
- PASS
- LIST
- RETR
- CWD
- PWD

#### Data Connection

The data connection is used to transfer:

- Files.
- Directory listings.
- Other requested data.

The ports used for data transfers depend on whether FTP operates in active or passive mode.

### FTP Active Mode

In **Active Mode**, the client tells the server which address and port should receive the data connection.

The client sends a `PORT` command through the control connection.

The server then initiates the data connection back to the client.

```text
FTP Client                         FTP Server
    |                                   |
    | ------ Control (TCP 21) --------> |
    |                                   |
    | -------- PORT Command ---------> |
    |                                   |
    | <------- Data Connection -------- |
    |                                   |
```

The traditional server-side source port for active data connections is TCP 20.

#### Active Mode Characteristics

- The client establishes the control connection.
- The client advertises a listening port.
- The server initiates the data connection.
- Incoming data connections can be problematic when the client is behind a firewall or NAT.

### FTP Passive Mode

In **Passive Mode**, the client initiates both the control connection and the data connection.

The client sends a `PASV` command.

The server responds with an address and port where it will accept the data connection.

```text
FTP Client                         FTP Server
    |                                   |
    | ------ Control (TCP 21) --------> |
    |                                   |
    | -------- PASV Command ----------> |
    |                                   |
    | <------ Server IP and Port ------ |
    |                                   |
    | -------- Data Connection -------> |
    |                                   |
```

#### Passive Mode Characteristics

- The client establishes the control connection.
- The client requests passive mode.
- The server provides a listening endpoint.
- The client initiates the data connection.

Passive mode is commonly preferred because it works more conveniently with many client-side firewall and NAT configurations.

### Active vs. Passive FTP

| Characteristic | Active Mode | Passive Mode |
|---|---|---|
| Control Connection | Client initiates | Client initiates |
| Data Connection | Server initiates | Client initiates |
| Main Command | PORT | PASV |
| Traditional Server Data Port | TCP 20 | Server-selected port |
| Client-Side NAT/Firewall Compatibility | More difficult | Generally easier |

### Common FTP Commands

| Command | Description |
|---|---|
| USER | Specifies the username for authentication |
| PASS | Sends the user's password |
| PORT | Specifies the address and port for active-mode data transfer |
| PASV | Requests passive-mode data transfer |
| LIST | Requests a directory listing |
| CWD | Changes the current working directory |
| PWD | Displays the current working directory |
| SIZE | Requests the size of a specified file |
| RETR | Downloads a file from the server |
| QUIT | Terminates the FTP session |

### FTP Communication Example

A simplified FTP session might look like:

```text
Server: 220 FTP Server Ready

Client: USER admin
Server: 331 Password required

Client: PASS example123
Server: 230 Login successful

Client: PWD
Server: 257 "/" is current directory

Client: LIST
Server: 150 Opening data connection
Server: 226 Transfer complete

Client: QUIT
Server: 221 Goodbye
```

The server responds using numerical status codes.

Common examples:

| Code | Meaning |
|---|---|
| 220 | Service ready |
| 331 | Password required |
| 230 | Login successful |
| 150 | File status okay; about to open data connection |
| 226 | Transfer completed |
| 530 | Not logged in |

### FTP Security Considerations

Traditional FTP does not encrypt the control or data connections.

This means that an analyst capturing authorized FTP traffic may be able to observe:

- Usernames.
- Passwords.
- FTP commands.
- Requested filenames.
- Directory listings.
- File contents.

This makes FTP particularly relevant when studying network traffic analysis.

### SFTP vs. FTPS

FTP has two commonly encountered secure alternatives.

| Protocol | Description |
|---|---|
| FTP | Traditional, unencrypted file transfer |
| FTPS | FTP protected using TLS |
| SFTP | SSH File Transfer Protocol, operating over SSH |

**Important:** SFTP is not simply FTP with TLS encryption. It is a separate file transfer protocol typically transported through SSH.

---

## SMB - Server Message Block

**SMB (Server Message Block)** is an application-layer protocol commonly used for sharing resources between computers.

It is especially prevalent in Windows and enterprise network environments.

SMB supports access to:

- Shared files.
- Shared directories.
- Printers.
- Network resources.
- Remote file operations.

### Characteristics

- **Layer:** Application Layer.
- **Common Port:** TCP 445.
- **Legacy Transport:** NetBIOS over TCP/IP.
- **Communication:** Session-oriented.
- **Authentication:** Supports authenticated access and permission enforcement.
- **Environment:** Common in Windows enterprise networks.

### SMB Ports

| Port | Protocol | Purpose |
|---|---|---|
| 137/UDP | NetBIOS Name Service | Name registration and resolution |
| 138/UDP | NetBIOS Datagram Service | Connectionless NetBIOS communication |
| 139/TCP | NetBIOS Session Service | Legacy SMB transport |
| 445/TCP | Direct-hosted SMB | Modern SMB communication |

Modern SMB typically uses TCP port `445`.

Newer SMB implementations can also support SMB over QUIC.

### SMB Communication

When SMB uses TCP, the endpoints establish a TCP connection before exchanging SMB messages.

A simplified communication flow:

```text
Client                              SMB Server
   |                                    |
   | -------- TCP SYN ----------------> |
   | <------- TCP SYN, ACK ------------ |
   | -------- TCP ACK ----------------> |
   |                                    |
   | -------- SMB Negotiate ---------> |
   | <------- SMB Negotiate Response -- |
   |                                    |
   | -------- SMB Session Setup ------> |
   | <------- SMB Session Response ---- |
   |                                    |
   | -------- Resource Request -------> |
   | <------- Resource Response ------- |
```

The specific messages vary depending on the SMB version and the operation being performed.

### SMB Authentication

SMB commonly uses authentication to verify whether a user has permission to access shared resources.

In enterprise environments, SMB may integrate with Windows authentication mechanisms.

Access permissions determine which users can:

- Read files.
- Write files.
- Modify resources.
- Access shared directories.
- Perform administrative operations.

Some environments may also allow guest access, depending on the configuration.

### SMB in Wireshark

The HTB packet capture demonstrates SMB communication over TCP port `445`.

The capture includes:

- TCP connection establishment.
- SMB protocol negotiation.
- Authentication-related messages.
- Failed logon responses.

One particularly important indicator is repeated authentication failure.

For example:

```text
SMB2 Session Setup Request

SMB2 Session Setup Response
STATUS_LOGON_FAILURE
```

A single failed authentication attempt may be normal.

However, repeated failures could indicate:

- Incorrect credentials.
- Misconfigured applications.
- Password guessing.
- Brute-force attempts.
- Unauthorized access attempts.
- Potential lateral movement activity.

### SMB and Lateral Movement

SMB is especially relevant during security investigations because attackers may attempt to use compromised credentials to access other systems.

A possible attack scenario:

1. An attacker compromises a workstation.
2. The attacker obtains valid user credentials.
3. The attacker attempts to access SMB shares on other hosts.
4. The attacker searches for accessible files or systems.
5. The attacker attempts to access additional resources or move laterally.

From a network analysis perspective, suspicious indicators may include:

- One workstation connecting to many SMB servers.
- Repeated authentication failures.
- Access to previously unused file shares.
- Unexpected communication between workstations.
- Unusual volumes of SMB file transfers.

**Important:** These behaviors are not automatically malicious. Analysts should compare them against the environment's normal communication patterns.

---

## Network Traffic Analysis - Practical Observations

When analyzing application-layer protocols, the objective is not only to identify the protocol but also to understand what the communicating hosts are doing.

### HTTP Traffic

Look for:

- Request methods.
- Requested URIs.
- HTTP response codes.
- Unusual requests.
- Unexpected destinations.
- Potentially sensitive plaintext information.

### HTTPS Traffic

Look for:

- TLS handshake messages.
- Server certificate information when visible.
- TLS versions.
- Connection destinations.
- Unexpected connection patterns.
- Repeated failed TLS negotiations.

The HTTP content is normally encrypted, but network metadata can still provide valuable information.

### FTP Traffic

Look for:

- Authentication attempts.
- USER and PASS commands.
- Active or passive mode negotiation.
- File uploads and downloads.
- Directory listings.
- Unexpected file transfers.

FTP control traffic is particularly useful because standard FTP commands are transmitted in plaintext.

### SMB Traffic

Look for:

- SMB protocol negotiation.
- Session setup requests.
- Authentication failures.
- File share access.
- Unexpected communication between hosts.
- Possible lateral movement.

### Useful Wireshark Display Filters

The following are practical examples of Wireshark display filters. These supplement the concepts introduced in this section.

```text
# Display HTTP traffic
http

# Display TLS traffic
tls

# Display FTP control traffic
ftp

# Display SMB2/SMB3 traffic
smb2

# Display TCP traffic involving port 445
tcp.port == 445

# Display traffic involving a specific host
ip.addr == 192.168.1.10

# Display TCP connections to HTTP port 80
tcp.dstport == 80

# Display TCP SYN packets without the ACK flag
tcp.flags.syn == 1 && tcp.flags.ack == 0
```

These filters help isolate relevant packets and simplify network investigations.

---

## Key Takeaways

- **HTTP** is a stateless application-layer protocol that traditionally transmits data without encryption.
- HTTP methods determine how clients interact with server resources.
- **HTTPS** protects HTTP communication using TLS.
- A TLS handshake establishes the cryptographic parameters needed for secure communication.
- **FTP** uses separate control and data connections.
- FTP supports **Active Mode** and **Passive Mode**, which differ in how data connections are initiated.
- Traditional FTP sends credentials and other information in plaintext.
- **SMB** provides file and resource sharing, particularly in Windows environments.
- Modern SMB commonly uses TCP port `445`.
- Repeated SMB authentication failures may indicate suspicious activity or configuration problems.
- Application-layer analysis helps identify protocol behavior, security weaknesses, and potentially malicious communication.
- Understanding normal protocol behavior is essential for recognizing anomalies during packet analysis.
