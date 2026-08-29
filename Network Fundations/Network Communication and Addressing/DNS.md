# Domain Name System (DNS)

The **Domain Name System (DNS)** translates human-readable domain names into IP addresses.

Instead of remembering numerical addresses such as:

```text
93.184.216.34
```

users can access services through names such as:

```text
www.example.com
```

DNS acts as the translation layer between those two forms of addressing.

---

## Domain Names vs. IP Addresses

| Address Type    | Description                                   |
| --------------- | --------------------------------------------- |
| **Domain Name** | Human-readable name such as `www.example.com` |
| **IP Address**  | Numerical address such as `93.184.216.34`     |

The basic relationship is:

```text
www.example.com
       │
       ▼
      DNS
       │
       ▼
93.184.216.34
```

Applications can therefore use memorable names while the network ultimately communicates using IP addresses.

---

# DNS Hierarchy

DNS is structured hierarchically, similar to a tree.

A domain name can contain several levels.

For example:

```text
www.example.com
```

can be broken down into:

```text
www      → Subdomain / Hostname
example  → Second-Level Domain
com      → Top-Level Domain
.        → Root
```

Conceptually:

```text
                 Root
                  .
                  │
                 com
                  │
               example
                  │
                 www
```

---

## Root

The **root** is at the top of the DNS hierarchy.

It is represented conceptually by:

```text
.
```

Root servers help direct DNS queries toward the correct Top-Level Domain servers.

---

## Top-Level Domain (TLD)

A **Top-Level Domain** is the portion immediately below the root.

Examples include:

```text
.com
.org
.net
.uk
.de
```

TLDs can represent generic categories or country codes.

For:

```text
example.com
```

the TLD is:

```text
.com
```

---

## Second-Level Domain

The **Second-Level Domain** is generally the recognizable name registered beneath the TLD.

For:

```text
example.com
```

the second-level domain is:

```text
example
```

Another example:

```text
google.com
```

contains:

```text
google → Second-Level Domain
com    → Top-Level Domain
```

---

## Subdomains and Hostnames

Additional labels can exist before the second-level domain.

For example:

```text
www.example.com
```

contains:

```text
www
```

while:

```text
accounts.google.com
```

contains:

```text
accounts
```

These labels can identify specific hosts or subdivisions of a domain.

---

# DNS Resolution

When a user enters a domain name into an application, the system must determine the corresponding IP address.

This process is called:

**DNS Resolution**

or

**Domain Translation**

A simplified example is:

```text
www.example.com
       │
       ▼
DNS Resolution
       │
       ▼
93.184.216.34
```

---

# DNS Resolution Process

Suppose a user enters:

```text
www.example.com
```

into a browser.

Several steps can occur before the browser knows where to send the request.

---

## 1. User Requests a Domain

The process begins when the user provides a domain name.

```text
Browser
   │
   ▼
www.example.com
```

The system now needs the associated IP address.

---

## 2. Local DNS Cache

The computer first checks its **local DNS cache**.

The cache stores DNS information learned from previous requests.

Conceptually:

```text
www.example.com
       │
       ▼
Local DNS Cache
       │
   ┌───┴───┐
 Found   Not Found
```

If the answer is already cached, the system may use it without performing the full external DNS resolution process.

If it is not available locally, another DNS server must be queried.

---

# Recursive DNS Server

The client sends the query to a **recursive DNS server**.

This server may be provided by:

* An ISP
* A third-party DNS provider

The recursive resolver performs the work of finding the requested DNS information on behalf of the client.

```text
Computer
   │
   │ Where is www.example.com?
   ▼
Recursive DNS Server
```

---

# Root Server

If the recursive resolver does not already know the answer, it can contact a **root server**.

The root server does not necessarily return the final IP address.

Instead, it points the resolver toward the appropriate **TLD name server**.

For example:

```text
www.example.com
       │
       ▼
Root Server
       │
       ▼
.com TLD Server
```

The root server effectively provides the next step in the hierarchy.

---

# TLD Name Server

The **Top-Level Domain name server** manages information related to a particular TLD.

For:

```text
www.example.com
```

the relevant TLD is:

```text
.com
```

The `.com` TLD server helps direct the resolver toward the authoritative server responsible for:

```text
example.com
```

Conceptually:

```text
Root
  │
  ▼
.com TLD
  │
  ▼
Authoritative Server
for example.com
```

---

# Authoritative Name Server

The **authoritative name server** contains the DNS information for the requested domain.

The recursive resolver asks it for the address associated with:

```text
www.example.com
```

The authoritative server can respond with the relevant IP address.

For example:

```text
www.example.com
       │
       ▼
93.184.216.34
```

---

# Complete DNS Query Process

The complete process can be represented as:

```text
Client
  │
  │ www.example.com?
  ▼
Recursive DNS Server
  │
  ▼
Root Server
  │
  ▼
.com TLD Server
  │
  ▼
Authoritative DNS Server
  │
  │ 93.184.216.34
  ▼
Recursive DNS Server
  │
  ▼
Client
```

A more detailed flow is:

```text
1. User enters www.example.com

2. Client checks local DNS cache

3. Client queries recursive DNS server

4. Recursive resolver queries root server

5. Root server points to .com TLD server

6. TLD server points to the authoritative
   server for example.com

7. Authoritative server provides the IP

8. Recursive resolver returns the IP
   to the client
```

---

# DNS and Web Communication

DNS resolution occurs before the client can establish a normal network connection to the destination server.

For example:

```text
User
 │
 ▼
www.example.com
 │
 ▼
DNS
 │
 ▼
93.184.216.34
 │
 ▼
Web Server
```

The browser does not send the normal web request to the text `www.example.com` itself.

DNS first provides the address that allows the destination host to be located.

The previously studied concepts therefore work together:

```text
Domain Name
    │
    ▼
   DNS
    │
    ▼
IP Address
    │
    ▼
Port
    │
    ▼
Application
```

For a web request, this could resemble:

```text
www.example.com
       │
       ▼
93.184.216.34
       │
       ▼
TCP 80 / 443
       │
       ▼
HTTP / HTTPS
```

---

# DNS Caching

DNS information can be temporarily stored in a cache.

Caching reduces the need to repeat the complete DNS resolution process every time the same domain is requested.

Without caching:

```text
Client
  │
  ▼
Recursive
  │
  ▼
Root
  │
  ▼
TLD
  │
  ▼
Authoritative
```

With a cached result:

```text
Client
  │
  ▼
Cached DNS Result
  │
  ▼
IP Address
```

This can make repeated domain resolution faster and reduce the number of DNS queries that need to travel through the hierarchy.

---

# DNS Components Overview

| Component                     | Role                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| **DNS Cache**                 | Stores previously resolved DNS information locally           |
| **Recursive DNS Server**      | Resolves DNS queries on behalf of clients                    |
| **Root Server**               | Directs queries toward the appropriate TLD                   |
| **TLD Server**                | Directs queries toward the authoritative server for a domain |
| **Authoritative Name Server** | Provides DNS information for the domain it manages           |

---

# Example

Suppose the user wants to visit:

```text
www.example.com
```

The resolution can be simplified as:

```text
www.example.com
       │
       ▼
Local Cache
       │
   Not Found
       │
       ▼
Recursive Resolver
       │
       ▼
Root
       │
       ▼
.com
       │
       ▼
example.com
Authoritative DNS
       │
       ▼
93.184.216.34
       │
       ▼
Recursive Resolver
       │
       ▼
Client
```

The client can then use the returned IP address to communicate with the destination server.

---

# Cybersecurity Perspective

DNS is fundamental to cybersecurity because many network activities depend on domain resolution.

Understanding the DNS process is useful during:

* Network enumeration
* Traffic analysis
* Incident investigation
* Domain investigation
* Detection of suspicious network activity
* Analysis of malware communication
* Troubleshooting connectivity problems

DNS traffic can reveal which domains systems are attempting to contact, making it an important source of information during network analysis.

It also introduces an important distinction:

```text
Human-readable identity → Domain Name

Network destination     → IP Address
```

DNS provides the relationship between them.

---

# Key Takeaways

* **DNS** translates domain names into IP addresses.
* Domain names are designed to be easier for humans to remember than numerical IP addresses.
* DNS uses a hierarchical structure.
* The hierarchy includes the **root**, **Top-Level Domains**, **Second-Level Domains**, and **subdomains/hostnames**.
* Clients may first check a **local DNS cache** before querying an external resolver.
* A **recursive DNS server** performs DNS resolution on behalf of the client.
* **Root servers** direct queries toward the appropriate TLD.
* **TLD servers** direct queries toward the appropriate authoritative name server.
* **Authoritative name servers** provide DNS information for the domains they manage.
* DNS resolution ultimately provides the IP address required for network communication.
* DNS caching can reduce repeated queries and speed up subsequent resolutions.
