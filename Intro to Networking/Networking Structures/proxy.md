# Proxies

A **proxy** is a device or service that sits between two endpoints and acts as a **mediator** for their communication.

The key idea is that the proxy is not just forwarding packets blindly: it can inspect and process the traffic passing through it.

Because of this, proxies commonly operate at:

**OSI Layer 7 — Application Layer**

The main proxy types covered here are:

- **Forward Proxy**
- **Reverse Proxy**
- **Transparent / Non-Transparent Proxy**

---

# Forward Proxy

A **Forward Proxy** represents the client when accessing another resource.

Instead of connecting directly to the destination server, the client sends the request to the proxy.

```text
Client
  │
  ▼
Forward Proxy
  │
  ▼
Internet
  │
  ▼
Server
```

The destination server sees the proxy as the system making the connection.

---

## Example

In a corporate environment, internal machines may not be allowed to access the Internet directly.

Instead:

```text
Internal Host
     │
     ▼
Forward Proxy
     │
     ▼
Internet
```

The proxy can inspect and control outgoing traffic.

This allows organizations to:

- Restrict Internet access
- Filter websites
- Inspect HTTP traffic
- Apply security policies
- Monitor outbound connections

---

# Forward Proxy and Malware

A proxy can also provide an additional defensive layer.

If a system can only access the Internet through a proxy, malware must also be able to communicate through that proxy.

This means the malware may need to be:

**Proxy-aware**

Otherwise, its Command and Control communication may fail.

---

# Burp Suite as a Forward Proxy

**Burp Suite** is a common cybersecurity example of a forward proxy.

A browser can be configured to send HTTP traffic through Burp:

```text
Browser
   │
   ▼
Burp Suite
   │
   ▼
Web Server
```

Burp can then:

- Inspect requests
- Modify requests
- Inspect responses
- Modify responses

This is fundamental in web penetration testing.

---

# Reverse Proxy

A **Reverse Proxy** represents servers rather than clients.

Instead of controlling outgoing client requests, it receives incoming requests and forwards them toward backend servers.

```text
Internet
   │
   ▼
Reverse Proxy
   │
   ▼
Web Server
```

Clients communicate with the reverse proxy without necessarily knowing which backend system ultimately handles the request.

---

# Reverse Proxy Use Cases

Reverse proxies can provide:

- Traffic filtering
- Load distribution
- DDoS protection
- Web Application Firewall functionality
- Access to internal services
- Additional isolation between public and private infrastructure

---

# Cloudflare Example

Cloudflare can operate as a reverse proxy in front of a web application.

Instead of:

```text
User ─────► Web Server
```

the communication becomes:

```text
User
 │
 ▼
Cloudflare
 │
 ▼
Web Server
```

The origin server receives traffic after it passes through the reverse proxy infrastructure.

This allows Cloudflare to filter or absorb some malicious traffic before it reaches the application server.

---

# Web Application Firewall

A **Web Application Firewall (WAF)** can also operate as a reverse proxy.

For example:

```text
Internet
   │
   ▼
WAF
   │
   ├── Legitimate Request → Allow
   │
   └── Malicious Request → Block
   │
   ▼
Web Application
```

A WAF analyzes application-layer requests and attempts to identify malicious content.

An example mentioned in this section is:

**ModSecurity**

---

# Forward Proxy vs Reverse Proxy

The easiest way to distinguish them is by asking:

> Who is the proxy representing?

## Forward Proxy

Represents the **client**.

```text
Client → Proxy → Server
```

Used primarily to control outgoing communication.

## Reverse Proxy

Represents the **server**.

```text
Client → Proxy → Backend Server
```

Used primarily to control incoming communication.

---

## Comparison

| Type | Represents | Common Purpose |
|---|---|---|
| **Forward Proxy** | Client | Control/filter outgoing traffic |
| **Reverse Proxy** | Server | Control/filter incoming traffic |

A simple memory trick:

```text
Forward Proxy
CLIENT side

Reverse Proxy
SERVER side
```

---

# Transparent Proxy

A **Transparent Proxy** operates without requiring the client to explicitly know that the proxy exists.

The communication is intercepted automatically.

```text
Client
  │
  │ thinks it is communicating normally
  ▼
Transparent Proxy
  │
  ▼
Internet
```

The client does not need special proxy configuration.

---

# Non-Transparent Proxy

A **Non-Transparent Proxy** must be explicitly configured by the client.

For example, the operating system or browser may be configured with:

```text
Proxy Address: 192.168.1.20
Proxy Port:    8080
```

The traffic is then intentionally sent to the proxy.

```text
Client
  │
  │ Explicit Proxy Configuration
  ▼
Proxy
  │
  ▼
Internet
```

If the proxy is the only allowed path to the Internet and the client is not configured to use it, external communication may fail.

---

# Transparent vs Non-Transparent

| Type | Client Knows About Proxy? | Configuration Required? |
|---|---|---|
| **Transparent** | No | Usually no |
| **Non-Transparent** | Yes | Yes |

---

# Proxy vs VPN

A proxy and a VPN are not the same thing.

A proxy acts as an intermediary for specific communications and commonly operates at the application layer.

A VPN creates a network tunnel that can route network traffic into another network.

Simplified:

```text
Proxy
Application Traffic
      │
      ▼
    Proxy
      │
      ▼
Destination
```

versus:

```text
VPN
Device
  │
  ▼
Encrypted Tunnel
  │
  ▼
Remote Network
```

This distinction is especially important in cybersecurity because the tools and use cases are different.

---

# Cybersecurity Perspective

Proxies appear constantly in security work.

Examples include:

### Web Pentesting

```text
Browser
   │
   ▼
Burp Suite
   │
   ▼
Target Website
```

The proxy allows the tester to intercept and modify HTTP requests.

### Defensive Security

```text
Internal Network
      │
      ▼
Forward Proxy
      │
      ▼
Internet
```

Outbound traffic can be inspected and filtered.

### Web Application Protection

```text
Internet
   │
   ▼
Reverse Proxy / WAF
   │
   ▼
Web Server
```

Incoming requests can be inspected before reaching the application.

### Pivoting

Security professionals may also use proxy mechanisms to route traffic through compromised or intermediary hosts.

This becomes important later when studying:

- SOCKS proxies
- SSH tunneling
- Chisel
- sshuttle
- Pivoting

---

# Key Takeaways

- A **proxy** sits between endpoints and acts as a mediator.
- Proxies commonly operate at **Layer 7**.
- A **Forward Proxy** represents the client.
- A **Reverse Proxy** represents the server.
- Burp Suite is a common example of a forward proxy.
- Reverse proxies can protect backend systems and filter incoming traffic.
- WAFs can operate as reverse proxies.
- A **Transparent Proxy** does not require explicit client awareness.
- A **Non-Transparent Proxy** requires explicit proxy configuration.
- A proxy is not the same as a VPN.
- Proxies are highly relevant to web security, traffic filtering, pivoting, and network defense.