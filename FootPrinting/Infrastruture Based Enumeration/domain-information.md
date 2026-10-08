# Domain Information

---

## Overview

Domain information gathering is part of the **Internet Presence** layer of our enumeration methodology.

Our objective is to understand an organization's external infrastructure, technologies, services, and third-party providers.

We begin by analyzing the company's main website to understand its business activities and the technologies required to support them.

Passive reconnaissance allows us to gather information through public sources and third-party services without directly scanning the target infrastructure.

---

## SSL/TLS Certificates

SSL/TLS certificates may reveal additional domains and subdomains through their Subject Alternative Name (SAN) fields.

We can use these records to discover potential targets.

### Certificate Transparency

Certificate Transparency (CT) provides publicly accessible logs of issued digital certificates.

We can search these logs using:

- https://crt.sh/

### Query Certificates

```bash
curl -s "https://crt.sh/?q=inlanefreight.com&output=json" | jq .
```

**Explanation:**
- `curl`: Performs an HTTP request.
- `-s`: Enables silent mode.
- `output=json`: Requests JSON output.
- `|`: Pipes output to another command.
- `jq .`: Formats JSON output.

### Extract Unique Subdomains

```bash
curl -s "https://crt.sh/?q=inlanefreight.com&output=json" \
| jq -r '.[].name_value' \
| sort -u
```

We extract the domain names from certificate records and remove duplicates.

**Note:** Historical certificate records do not guarantee that the discovered subdomains are still active.

---

## Resolving Subdomains

After identifying subdomains, we can resolve them to their associated IP addresses.

### Single Domain

```bash
host inlanefreight.com
```

### Multiple Domains

```bash
for i in $(cat subdomainlist); do
    host "$i"
done
```

We can use DNS resolution to associate discovered subdomains with IP addresses.

However, we must verify whether the systems belong to our authorized scope before performing active testing.

---

## Shodan

Shodan is a search engine for Internet-connected devices and services.

It collects information about exposed systems, including:

- Open TCP/IP ports
- Running services
- Service versions
- SSL/TLS information
- Network information

### Query an IP Address

```bash
shodan host <IP>
```

### Query Multiple IP Addresses

```bash
for i in $(cat ip-addresses.txt); do
    shodan host "$i"
done
```

**Shodan vs. Nmap:**

- **Nmap:** We actively scan a target.
- **Shodan:** We retrieve information collected by a third-party service.

Shodan data may be outdated and should not automatically be treated as the current state of a system.

---

## DNS Enumeration

DNS records can reveal information about a company's infrastructure and third-party services.

### Query DNS Records

```bash
dig any inlanefreight.com
```

The `ANY` query requests available record types, but it does not guarantee a complete response.

We can query individual record types:

```bash
dig A inlanefreight.com
dig MX inlanefreight.com
dig NS inlanefreight.com
dig TXT inlanefreight.com
dig SOA inlanefreight.com
```

### Important DNS Records

| Record | Description |
|---|---|
| A | Maps a domain to an IPv4 address. |
| MX | Identifies mail servers responsible for receiving email. |
| NS | Identifies authoritative name servers. |
| TXT | Stores text-based information, including verification and email security records. |
| SOA | Contains administrative information about a DNS zone. |

---

## TXT Records and Third-Party Services

TXT records may reveal integrations with external services.

Examples from the HTB material:

| Service | Potential Information |
|---|---|
| Atlassian | Software development and collaboration tools |
| Google | Email services and domain verification |
| LogMeIn | Remote access solutions |
| Mailgun | Email APIs and SMTP services |
| Microsoft | Domain verification and email infrastructure |

These records provide clues about an organization's technology stack, but they do not necessarily prove that every associated service is currently active.

### SPF Records

Sender Policy Framework (SPF) specifies which mail sources are authorized by a domain's email policy.

Example:

```text
v=spf1 include:mailgun.org include:_spf.google.com ~all
```

**Explanation:**
- `v=spf1`: Identifies an SPF record.
- `include:mailgun.org`: Includes Mailgun's SPF authorization rules.
- `include:_spf.google.com`: Includes Google's SPF authorization rules.
- `~all`: Marks other senders as a soft failure.

Related email authentication mechanisms include:

- **SPF:** Authorizes sending sources.
- **DKIM:** Uses cryptographic signatures to validate email messages.
- **DMARC:** Defines domain-level policies and reporting for SPF/DKIM alignment.

---

## Key Takeaways

- Domain enumeration involves more than discovering subdomains.
- We can use SSL/TLS certificates to identify additional potential targets.
- Certificate Transparency logs provide historical information about issued certificates.
- `crt.sh` allows us to search certificate records.
- `host` helps us resolve domain names to IP addresses.
- Shodan provides third-party information about Internet-exposed services.
- DNS records can reveal infrastructure details and external service providers.
- TXT records may expose information about organizational integrations.
- We should always verify target ownership and authorization before active testing.

**Our objective is to understand the organization's Internet presence and discover potential investigation paths before proceeding with active enumeration.**