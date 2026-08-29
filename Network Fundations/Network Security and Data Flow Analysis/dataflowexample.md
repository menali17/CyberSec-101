# Data Flow Example

This section combines the concepts from the previous networking topics and shows what happens when a user accesses a website from a laptop connected to a home wireless network.

The complete flow involves:

* Wireless connectivity
* DHCP
* DNS
* MAC addressing
* IP addressing
* Ports
* Encapsulation
* ARP
* NAT
* Routing
* Firewalls
* Client-server communication
* Decapsulation

---

# 1. Accessing the Wireless Network

Before accessing the Internet, the laptop must first connect to the local wireless network.

The process begins by identifying the desired:

**SSID — Service Set Identifier**

The SSID represents the wireless network name.

For example:

```text id="2k4dyj"
Home-WiFi
```

If the wireless network uses security such as:

* WPA2
* WPA3

the user must provide the correct password or credentials.

Conceptually:

```text id="dc93pg"
Laptop
   │
   ▼
Find SSID
   │
   ▼
Authenticate
WPA2 / WPA3
   │
   ▼
Connected to WLAN
```

Once the wireless connection is established, the laptop still needs valid network configuration.

This is where **DHCP** becomes important.

---

# 2. Obtaining Network Configuration with DHCP

The operating system needs configuration such as:

* IP address
* Subnet mask
* Default gateway
* DNS server

If the laptop does not already have valid network settings, it requests them from the DHCP server.

In a home environment, the DHCP service commonly runs on the router.

For example:

```text id="a8uofm"
Laptop
   │
   │ DHCP
   ▼
Router / DHCP Server
```

The DHCP server may assign:

```text id="6g2x0o"
IP Address:      192.168.1.10
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.1.1
DNS Server:      <DNS Server>
```

The laptop can now participate in the IP network.

---

# 3. User Requests a Website

Suppose the user opens a browser and enters:

```text id="v4vhek"
www.example.com
```

At this point, the computer does not necessarily know the destination server's IP address.

The domain name must first be resolved.

---

# 4. DNS Resolution

The laptop sends a DNS query asking for the IP address associated with:

```text id="y6y1ah"
www.example.com
```

The DNS system responds with an address such as:

```text id="q6dui6"
93.184.216.34
```

Conceptually:

```text id="bqzjbg"
Laptop
   │
   │ www.example.com?
   ▼
DNS Server
   │
   │ 93.184.216.34
   ▼
Laptop
```

Now the browser knows the logical IP destination for the web request.

---

# 5. Application Layer

The browser creates an HTTP or HTTPS request for the webpage.

For example:

```text id="xnwntz"
Browser
   │
   ▼
HTTP / HTTPS Request
```

At this stage, the information belongs to the **Application Layer**.

---

# 6. Transport Layer

The application data is passed to the Transport Layer.

For typical web communication, the example uses **TCP**.

The transport information includes:

* Source port
* Destination port

For example:

```text id="psvr5g"
Source Port:      Dynamic Client Port
Destination Port: 443
```

or, when using HTTP:

```text id="sek1ux"
Destination Port: 80
```

The relationship is:

```text id="lp7g3y"
HTTP  → TCP 80
HTTPS → TCP 443
```

The Transport Layer encapsulates the application data into a TCP segment.

---

# 7. Internet / Network Layer

The TCP segment is passed to the Internet Layer of the TCP/IP model.

An IP header is added.

For example:

```text id="njtoqn"
Source IP:
192.168.1.10

Destination IP:
93.184.216.34
```

The packet can now be routed toward the remote server.

At this point:

```text id="gj5lft"
Source       = Laptop
Destination  = Web Server
```

in terms of logical IP addressing.

---

# 8. Link Layer

The IP packet must now be transmitted across the local WLAN.

It is encapsulated inside a wireless frame.

The frame needs MAC addresses.

The source MAC is the laptop's wireless network interface.

The destination MAC is the local next-hop device: the router/default gateway.

Conceptually:

```text id="jn9qll"
Source MAC:
Laptop NIC

Destination MAC:
Router Interface
```

This is an important distinction:

```text id="jq4h8p"
Destination IP
      │
      └── Remote Web Server

Destination MAC
      │
      └── Local Router
```

The remote server is outside the local network, so the laptop sends the local frame to its default gateway.

---

# 9. ARP

Before the laptop can send the frame to the router, it needs to know the router's MAC address.

The laptop can first check its ARP table.

If the mapping is not already known, it can use **ARP** to discover the MAC address associated with the default gateway's IP.

For example:

```text id="xoyyuv"
Who has 192.168.1.1?
        │
        ▼
       ARP
        │
        ▼
192.168.1.1 is at
AA:BB:CC:DD:EE:FF
```

The laptop can then create the local frame.

---

# 10. Encapsulation Overview

At this point, the original browser request has been encapsulated through several layers.

Conceptually:

```text id="f68b6v"
Application Data
      │
      ▼
┌─────────────────────────────┐
│ HTTP / HTTPS Request        │
└─────────────────────────────┘

      ↓ Transport

┌─────────────────────────────┐
│ TCP Header                  │
│ Application Data            │
└─────────────────────────────┘

      ↓ Internet

┌─────────────────────────────┐
│ IP Header                   │
│ TCP Header                  │
│ Application Data            │
└─────────────────────────────┘

      ↓ Link

┌─────────────────────────────┐
│ Wi-Fi / Ethernet Header     │
│ IP Header                   │
│ TCP Header                  │
│ Application Data            │
└─────────────────────────────┘
```

Each layer adds information required for its respective responsibility.

---

# 11. Local Transmission

The laptop sends the frame over the wireless network.

At the Link Layer:

```text id="6q2tmz"
Laptop MAC
     │
     ▼
Router MAC
```

At the Network Layer:

```text id="2wg4mt"
192.168.1.10
     │
     ▼
93.184.216.34
```

The frame reaches the router, which removes or processes the local link-layer information and handles the IP packet.

---

# 12. Network Address Translation

The laptop uses a private IP address:

```text id="n2sw7t"
192.168.1.10
```

This address cannot be routed directly across the public Internet.

The home router therefore performs:

**Network Address Translation (NAT)**

Suppose the router has the public address:

```text id="gj9b1z"
203.0.113.45
```

Before NAT:

```text id="myvnr8"
Source IP:
192.168.1.10
```

After NAT:

```text id="ei9yst"
Source IP:
203.0.113.45
```

The router maintains the necessary translation information so that returning traffic can later be sent back to the correct internal client.

---

# 13. Sending the Packet to the ISP

After NAT, the router forwards the packet toward the ISP.

```text id="8dmhkz"
Laptop
192.168.1.10
     │
     ▼
Router / NAT
203.0.113.45
     │
     ▼
ISP
     │
     ▼
Internet
```

From there, the packet may traverse several routers.

---

# 14. Routing Across the Internet

Intermediate routers examine the destination IP address:

```text id="hlszin"
93.184.216.34
```

and determine where the packet should be sent next.

A simplified path might look like:

```text id="akj46g"
Home Router
     │
     ▼
ISP Router
     │
     ▼
Intermediate Router
     │
     ▼
Intermediate Router
     │
     ▼
Destination Network
```

Each router forwards the packet toward its final destination.

---

# 15. Destination Firewall

When the packet reaches the destination network, a firewall may inspect the incoming traffic.

For example, it may check:

```text id="jmksaz"
Destination IP
Destination Port
Protocol
Firewall Policy
```

If the website is using HTTPS:

```text id="f7vbcb"
TCP 443
```

must be permitted.

Conceptually:

```text id="ouvfva"
Incoming Packet
      │
      ▼
   Firewall
      │
   Allowed?
   /      \
 Yes       No
 │          │
 ▼          ▼
Server     Block
```

---

# 16. Web Server Processing

If the request passes the firewall, it reaches the web server.

Examples of web server software include:

* Apache
* Nginx
* IIS

The server receives the request and prepares the requested webpage content.

This may include:

* HTML
* CSS
* JavaScript
* Images
* Other resources

Conceptually:

```text id="ab7wb2"
Client
   │
   │ HTTP Request
   ▼
Web Server
   │
   │ Process Request
   ▼
Web Content
```

---

# 17. Server Response

The server then sends a response back toward the client.

The addressing direction is now reversed.

The server becomes the source:

```text id="gb0wnq"
Source IP:
93.184.216.34
```

The home router's public IP becomes the destination:

```text id="6cp4a8"
Destination IP:
203.0.113.45
```

Conceptually:

```text id="b6g7dl"
Web Server
93.184.216.34
      │
      ▼
Internet
      │
      ▼
Home Router
203.0.113.45
```

---

# 18. Reverse NAT

When the response reaches the router, NAT determines which internal device initiated the original connection.

The router translates the destination back to the laptop's private address.

```text id="9to1vm"
203.0.113.45
      │
      │ NAT Table
      ▼
192.168.1.10
```

The router then forwards the traffic into the local network.

---

# 19. Local Delivery to the Laptop

The response is transmitted over the local WLAN to the laptop.

```text id="4k95t4"
Router
   │
   │ Wi-Fi Frame
   ▼
Laptop
```

The laptop receives the frame and begins processing it.

---

# 20. Decapsulation

The receiving system reverses the encapsulation process.

```text id="ks4zyg"
Wi-Fi Frame
     │
     ▼
Remove Link Header
     │
     ▼
IP Packet
     │
     ▼
Remove IP Header
     │
     ▼
TCP Segment
     │
     ▼
Remove TCP Header
     │
     ▼
Application Data
```

This process is called:

**Decapsulation**

The browser eventually receives the actual application data returned by the server.

---

# 21. Browser Rendering

The browser processes resources such as:

* HTML
* CSS
* JavaScript
* Images

and renders the webpage for the user.

```text id="kl914k"
Network Response
      │
      ▼
Browser
      │
      ▼
HTML / CSS / JavaScript
      │
      ▼
Rendered Webpage
```

What appears to the user as simply entering a URL and loading a page therefore involves multiple protocols, devices, and network layers.

---

# Complete Data Flow

The entire process can be summarized as:

```text id="dzdpnv"
1. Laptop connects to WLAN
           │
           ▼
2. WPA2/WPA3 Authentication
           │
           ▼
3. DHCP Configuration
           │
           ▼
4. User enters domain
           │
           ▼
5. DNS Resolution
           │
           ▼
6. HTTP/HTTPS Request
           │
           ▼
7. TCP Encapsulation
           │
           ▼
8. IP Encapsulation
           │
           ▼
9. ARP resolves gateway MAC
           │
           ▼
10. Wi-Fi Frame
           │
           ▼
11. Router
           │
           ▼
12. NAT
           │
           ▼
13. ISP
           │
           ▼
14. Internet Routing
           │
           ▼
15. Destination Firewall
           │
           ▼
16. Web Server
           │
           ▼
17. Server Response
           │
           ▼
18. Reverse Internet Path
           │
           ▼
19. Reverse NAT
           │
           ▼
20. Laptop
           │
           ▼
21. Decapsulation
           │
           ▼
22. Browser Renders Page
```

---

# Layer-by-Layer View

The main networking concepts can also be mapped to their respective layers.

| Layer                  | Example in This Flow                       |
| ---------------------- | ------------------------------------------ |
| **Application**        | HTTP/HTTPS, DNS                            |
| **Transport**          | TCP and ports                              |
| **Internet / Network** | IP addressing and routing                  |
| **Link**               | MAC addressing, Wi-Fi/Ethernet frames, ARP |

This creates the relationship:

```text id="jw5efj"
Application
HTTP / HTTPS
      │
      ▼
Transport
TCP + Ports
      │
      ▼
Network
IP Addresses
      │
      ▼
Link
MAC Addresses
      │
      ▼
Wireless Signal
```

---

# Technologies Working Together

The example combines nearly every major concept introduced in Network Foundations.

| Technology      | Role                                                   |
| --------------- | ------------------------------------------------------ |
| **Wi-Fi**       | Connects the laptop to the local network               |
| **WPA2/WPA3**   | Protects access to the wireless network                |
| **DHCP**        | Provides network configuration                         |
| **DNS**         | Resolves the website domain into an IP address         |
| **ARP**         | Resolves the gateway's IPv4 address into a MAC address |
| **MAC Address** | Enables local frame delivery                           |
| **IP Address**  | Enables logical communication across networks          |
| **Ports**       | Identify applications and services                     |
| **TCP**         | Provides transport for the web request in this example |
| **NAT**         | Translates the private address to a public address     |
| **Router**      | Forwards packets between networks                      |
| **Firewall**    | Controls whether traffic is permitted                  |
| **HTTP/HTTPS**  | Carries the web request and response                   |

---

# Cybersecurity Perspective

This complete flow is extremely useful when analyzing security incidents because a problem or attack can occur at several different stages.

For example:

```text id="3k0od3"
Wireless
   │
   ├── Unauthorized Network Access
   │
DHCP
   │
   ├── Incorrect Network Configuration
   │
DNS
   │
   ├── Suspicious Domain Resolution
   │
Network
   │
   ├── Unexpected IP Communication
   │
Ports
   │
   ├── Exposed Services
   │
Firewall
   │
   ├── Allowed / Blocked Connections
   │
Application
   │
   └── Malicious Requests
```

A security analyst therefore benefits from understanding the entire communication path rather than viewing protocols independently.

When investigating traffic, it is useful to ask:

* Which host initiated the connection?
* Which DNS name was resolved?
* Which destination IP was contacted?
* Which port and protocol were used?
* Was the traffic allowed by a firewall?
* Was NAT involved?
* Which application generated the connection?
* What response came back?

---

# Key Takeaways

* A simple website request involves many protocols and network layers.
* The laptop must first connect to the WLAN and obtain valid network configuration.
* **DHCP** can provide the IP address, subnet mask, default gateway, and DNS server.
* **DNS** translates the requested domain name into an IP address.
* Application data is encapsulated as it moves down through the network stack.
* **TCP ports** identify the source and destination applications.
* **IP addresses** identify the logical endpoints of communication.
* **MAC addresses** are used for local link communication.
* **ARP** can determine the MAC address of the default gateway.
* The router performs **NAT** when private addresses communicate with the public Internet.
* Routers forward packets based on destination IP information.
* Firewalls can inspect and permit or deny traffic before it reaches a server.
* The server processes the request and returns a response.
* NAT translates the returning traffic back toward the correct internal client.
* The client performs **decapsulation** before passing the received data to the browser.
* The browser renders the returned webpage.
* Understanding the complete data flow connects individual networking concepts into a single practical model.
