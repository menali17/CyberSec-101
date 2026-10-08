# DNS (Domain Name System)

---

## Overview

The Domain Name System (DNS) is a distributed system responsible for resolving domain names into IP addresses.

DNS also provides information about mail servers, nameservers, and other services associated with a domain.

DNS commonly uses **UDP/TCP port 53**.

### Types of DNS Servers

- **Root Server:** Directs queries toward the appropriate TLD servers.
- **Authoritative Nameserver:** Holds authoritative information for a DNS zone.
- **Recursive Resolver:** Resolves domain names by querying other DNS servers.
- **Caching Server:** Temporarily stores DNS responses.
- **Forwarding Server:** Forwards DNS requests to another DNS server.

DNS communication is traditionally unencrypted, but technologies such as DoT and DoH can encrypt DNS queries.

---

## DNS Records

| Record | Description |
|---|---|
| A | Maps a domain to an IPv4 address |
| AAAA | Maps a domain to an IPv6 address |
| MX | Identifies mail servers |
| NS | Identifies authoritative nameservers |
| TXT | Stores text information, including SPF and verification records |
| CNAME | Creates an alias for another domain name |
| PTR | Maps an IP address to a domain name |
| SOA | Contains administrative information about a DNS zone |

### Forward and Reverse DNS

- **Forward DNS:** Resolves domain names into IP addresses.
- **Reverse DNS:** Resolves IP addresses into domain names using PTR records.

---

## DNS Configuration

BIND9 is a commonly used DNS server implementation on Linux.

Important configuration files include:

- `/etc/bind/named.conf.local`
- `/etc/bind/named.conf.options`
- DNS zone files
- Reverse DNS zone files

### Zone Files

A zone file contains DNS records for a specific DNS zone.

It typically includes:

- SOA records
- NS records
- A/AAAA records
- MX records
- CNAME records

### Security-Related Settings

| Setting | Description |
|---|---|
| `allow-query` | Controls which clients can query the server |
| `allow-recursion` | Controls which clients can request recursive resolution |
| `allow-transfer` | Controls which clients can perform zone transfers |
| `zone-statistics` | Enables collection of zone statistics |

Misconfigured DNS permissions may expose information about an organization's infrastructure.

---

## DNS Enumeration

We can use `dig` to query DNS records.

### Query Nameservers

```bash
dig NS inlanefreight.htb @<DNS_IP>
```

### Query SOA

```bash
dig SOA inlanefreight.htb @<DNS_IP>
```

### Query Available Records

```bash
dig ANY inlanefreight.htb @<DNS_IP>
```

An ANY query does not guarantee that all DNS records will be returned.

### Query DNS Server Version

```bash
dig @<DNS_IP> version.bind CH TXT
```

Some DNS servers expose version information through CHAOS-class TXT queries.

This functionality may be disabled or configured to return a custom value.

---

## DNS Zone Transfer (AXFR)

AXFR is a mechanism used to transfer DNS zone records between servers.

It is commonly used to synchronize authoritative DNS servers.

If zone transfers are improperly configured, unauthorized clients may retrieve information about the zone.

### Attempt a Zone Transfer

```bash
dig AXFR inlanefreight.htb @<DNS_IP>
```

A successful zone transfer may reveal:

- Subdomains
- Hostnames
- IP addresses
- Mail servers
- Internal infrastructure information

### Security Risk

The `allow-transfer` setting should restrict zone transfers to authorized systems.

---

## Subdomain Brute Forcing

Subdomain brute forcing involves querying DNS with a list of possible subdomain names.

For example:

```text
www.inlanefreight.htb
mail.inlanefreight.htb
dev.inlanefreight.htb
admin.inlanefreight.htb
```

We can use wordlists and tools such as:

- `dig`
- `dnsenum`
- SecLists

Unlike password brute forcing, DNS subdomain brute forcing attempts to discover valid domain names through repeated DNS queries.

---

## Key Takeaways

- DNS translates domain names into IP addresses.
- DNS records can reveal information about an organization's infrastructure.
- BIND9 uses configuration files and zone files to manage DNS services.
- `dig` allows us to query DNS records.
- AXFR is used to transfer DNS zone information.
- Misconfigured zone transfers may disclose sensitive infrastructure details.
- Subdomain brute forcing can help discover additional hosts when zone transfers are unavailable.

**Our objective is to identify DNS records, discover potential hosts, and understand the target's DNS infrastructure.**