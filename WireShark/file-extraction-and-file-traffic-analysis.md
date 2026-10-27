
# Wireshark Lab - File Extraction and Live Traffic Analysis

## Introduction

This lab focuses on using Wireshark to investigate network traffic, follow TCP streams, and extract transferred files.

The lab is divided into two parts:

1. Analyzing a previously captured PCAP file.
2. Performing live network traffic analysis.

The main objectives are to:

- Open an existing PCAP file.
- Apply display filters.
- Identify HTTP requests and responses.
- Follow TCP streams.
- Identify JPEG/JFIF files.
- Export HTTP objects.
- Capture live network traffic.
- Investigate FTP and HTTP communications.
- Reconstruct files transferred over FTP.

---

# Part 1 - HTTP File Extraction

## Lab Scenario

The first part of the lab uses the following capture:

`Wireshark-lab-2.pcap`

According to the HTB material, this capture contains approximately:

- 1,171 total packets.
- Fewer than 20 packets specifically identified as HTTP.

Our objective is to investigate the HTTP traffic and extract images that may contain evidence.

---

## Task 1 - Open a Pre-Captured File

Open Wireshark and load the provided PCAP file.

### Procedure

1. Open Wireshark.
2. Select **File → Open**.
3. Locate `Wireshark-lab-2.pcap`.
4. Open the file.

Wireshark displays the captured packets in the Packet List pane.

---

## Task 2 - Filter HTTP Traffic

The next task is to isolate HTTP traffic from the rest of the capture.

### Display Filter

```text
http
```

This displays packets recognized as HTTP.

### What to Look For

Examine the captured HTTP requests and responses.

The HTB material highlights:

- `GET` requests.
- `200 OK` responses.

### HTTP GET

The GET method is used to request a resource from a web server.

Example:

```http
GET /image.jpg HTTP/1.1
```

### HTTP 200 OK

A response containing:

```http
HTTP/1.1 200 OK
```

indicates that the HTTP request was handled successfully.

The lab uses these responses to identify resources returned by the web server.

---

## Task 3 - Follow TCP Streams

After identifying HTTP responses, select a packet associated with a successful response.

### Procedure

1. Select an HTTP packet containing `200 OK`.
2. Right-click the packet.
3. Select **Follow → TCP Stream**.
4. Examine the reconstructed communication.

Wireshark opens a new window showing the TCP stream.

### TCP Stream Filter

Following a TCP stream also applies a display filter similar to:

```text
tcp.stream eq 0
```

The stream number identifies the selected TCP conversation.

### Purpose

Following the stream allows us to examine the communication between the client and server.

This helps confirm that data was transferred.

---

## Identifying JPEG Files

The lab focuses on extracting possible image files from HTTP traffic.

JPEG images may use the **JPEG File Interchange Format (JFIF)**.

JFIF information can help identify JPEG content transferred during an HTTP conversation.

### Display Filter

```text
http && image-jfif
```

### Purpose

This expression displays packets matching both:

- HTTP protocol traffic.
- JPEG/JFIF content recognized by Wireshark.

According to the HTB material, this should reduce the results to approximately three matching packets.

These packets indicate where JPEG content may be present in the capture.

---

## Exporting HTTP Objects

Once image transfers have been identified, Wireshark can export objects from HTTP traffic.

### Procedure

1. Open the **File** menu.
2. Select **Export Objects → HTTP**.
3. Locate the image files.
4. Select the desired object.
5. Save the file locally.

### Example

```text
file.JPG
```

### Purpose

The exported image can be examined separately from the PCAP.

In the lab scenario, the images are collected as potential evidence.

The security team can then investigate whether the image files contain hidden information.

---

# Part 2 - Live Capture and Analysis

## Lab Scenario

After examining the PCAP, the Security Manager confirms that a user has been smuggling data outside the network through images.

The next objective is to investigate the user's network activity and determine whether additional suspicious communications are occurring.

### Hosts of Interest

| Host | IP Address |
|---|---|
| Investigated User | `172.16.10.2` |
| Other Network Host | `172.16.10.20` |

The lab focuses on network communications involving these hosts.

---

## Connecting to the Academy Lab

The lab uses a remote desktop connection to access a virtual machine running Wireshark.

Connection can be established through:

- HTB Pwnbox.
- A personal VM connected using the HTB Academy VPN.

### FreeRDP Command

```bash
xfreerdp /v:<target IP> /u:htb-student /p:'HTB_@cademy_stdnt!'
```

The target IP address is provided when the lab machine is spawned.

### Lab Credentials

- Username: `htb-student`
- Password: Provided by the HTB lab.
- Target IP: Provided by the lab environment.

---

## Starting a Live Capture

After connecting to the lab machine:

1. Open Wireshark.
2. Select interface `ens224`.
3. Start capturing network traffic.
4. Allow the capture to run for several minutes.
5. Stop the capture.
6. Begin analyzing the results.

### Investigation Objective

Determine whether the user's computer communicates with other internal or external systems in an unusual manner.

---

## Initial Traffic Analysis

Before applying specific filters, examine the capture to understand the communication patterns.

### Questions to Investigate

- How many network conversations are present?
- Which devices act as clients?
- Which devices act as servers?
- Which protocols are being used?
- Are unexpected ports involved?
- Is plaintext communication present?
- Are credentials visible?
- Are files being transferred?

The HTB material indicates that FTP and HTTP traffic should be present in the lab environment.

---

# FTP Analysis

## Identifying FTP Communications

The first step is to locate FTP traffic in the capture.

### Display Filter

```text
ftp
```

This helps identify the FTP clients and servers participating in the communication.

### Questions

- Which host is the FTP server?
- Which host initiated the FTP connection?
- Was the user authenticated?
- Was anonymous access used?

---

## Examining FTP Commands

To examine FTP control commands, use:

```text
ftp.request.command
```

This filter helps identify commands sent by the FTP client.

### Information to Look For

- Authentication.
- Directory navigation.
- File listing.
- File downloads.
- File uploads.
- Requested filenames.

### Common FTP Commands

| Command | Purpose |
|---|---|
| `USER` | Specifies the username |
| `PASS` | Sends the password |
| `LIST` | Requests directory information |
| `RETR` | Requests a file download |
| `STOR` | Requests a file upload |

FTP control traffic can reveal authentication details and file transfer requests.

---

## Extracting Files From FTP

After identifying the relevant file transfer, investigate the FTP data channel.

### Step 1 - Identify FTP Traffic

```text
ftp
```

### Step 2 - Examine Commands

```text
ftp.request.command
```

Identify which files were requested or transferred.

### Step 3 - Locate Data Traffic

```text
ftp-data
```

This filter displays FTP data transfer packets.

### Step 4 - Follow the TCP Stream

1. Select a packet belonging to the desired file transfer.
2. Right-click it.
3. Select **Follow → TCP Stream**.
4. Examine the reconstructed data.

### Step 5 - Save Raw Data

In the Follow TCP Stream window, change:

**Show and save data as → Raw**

Save the reconstructed data using the original filename.

### Step 6 - Validate the File

Verify that the extracted file matches the expected file type.

On Linux, this can be checked using:

```bash
file filename
```

### Important Concept

FTP separates control commands from the actual data transfer.

The control channel helps us identify what was transferred, while the data channel contains the transferred file contents.

---

# HTTP Analysis

## Identifying HTTP Traffic

The next objective is to investigate web traffic between the relevant hosts.

### Display Filter

```text
http
```

### Questions

- Which host is acting as the web server?
- Which application is running on the web server?
- Which HTTP request methods appear most frequently?
- Are files being transferred?

---

## Analyzing HTTP Requests and Responses

Examine HTTP requests and server responses.

### HTTP Request Methods

Examples include:

- GET
- POST

### HTTP Responses

Look for successful server responses such as:

```http
HTTP/1.1 200 OK
```

The captured HTTP data may provide information about the server and transferred resources.

---

## Following HTTP Streams

To examine a particular HTTP conversation:

1. Select the relevant packet.
2. Right-click it.
3. Choose **Follow → TCP Stream**.
4. Examine the reconstructed communication.

This helps identify the data exchanged between the client and server.

---

## Extracting HTTP Images

The lab again focuses on JPEG/JFIF content.

### Display Filter

```text
http && image-jfif
```

Use this filter to locate HTTP packets containing recognized JPEG/JFIF data.

### Exporting the Images

1. Open **File**.
2. Select **Export Objects → HTTP**.
3. Identify the transferred image.
4. Save the file locally.

### Objective

Recover image files from the network capture for further investigation.

---

# Filtering Traffic From the Client

The final question asks how to display only TCP traffic originating from the client.

### Display Filter

```text
tcp && ip.src == 172.16.10.2
```

### Explanation

| Expression | Purpose |
|---|---|
| `tcp` | Matches TCP traffic |
| `&&` | Requires both conditions |
| `ip.src == 172.16.10.2` | Matches packets originating from the client |

This displays TCP packets sent by `172.16.10.2`.

---

## Lab Review Questions

After completing the investigation, consider:

1. Which filters were used?
2. Were the filters effective?
3. How did filtering change the displayed traffic?
4. What useful information was recovered?
5. How did following TCP streams help the investigation?
6. What files were extracted?
7. How can these techniques support a security investigation?

---

## Important Filters Reference

| Filter | Purpose |
|---|---|
| `http` | Display HTTP traffic |
| `http && image-jfif` | Locate HTTP traffic containing JPEG/JFIF content |
| `tcp.stream eq 0` | Display a specific TCP stream |
| `ftp` | Display FTP control traffic |
| `ftp.request.command` | Display FTP request commands |
| `ftp-data` | Display FTP data transfers |
| `tcp && ip.src == 172.16.10.2` | Display TCP packets originating from the client |

---

## Key Takeaways

- Wireshark can analyze previously captured PCAP files.
- Display filters help isolate relevant traffic.
- HTTP GET requests and successful responses can identify transferred resources.
- Follow TCP Stream reconstructs captured TCP conversations.
- JPEG/JFIF data can be identified using Wireshark filters.
- Export Objects can recover files transferred over HTTP.
- Live captures allow analysts to investigate active network communications.
- FTP analysis requires examining control and data channels.
- FTP control commands help identify authentication and file transfer activity.
- FTP data streams can be reconstructed and saved as raw files.
- HTTP analysis can reveal web server information and transferred objects.
- Filtering by source IP allows analysts to focus on traffic generated by a specific client.
