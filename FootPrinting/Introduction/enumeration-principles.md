# Enumeration Principles

---

## Enumeration

Enumeration is the process of gathering information about a target's infrastructure, systems, services, and protocols.

We can gather information using two methods:

- **Active Enumeration:** We interact directly with the target through scanning and service queries.
- **Passive Enumeration:** We collect information from external sources without directly interacting with the target.

**OSINT (Open Source Intelligence)** is treated as a separate process because it relies exclusively on passive information gathering in the HTB methodology.

Enumeration is an **iterative process**. We continuously use previously discovered information to identify new targets, services, and potential attack paths.

Information can be gathered from:

- Domains and subdomains
- IP addresses
- Open ports
- Running services and protocols
- Network infrastructure

---

## Enumeration Mindset

Our primary objective during enumeration is not to immediately exploit a system, but to identify and understand the possible ways to access it.

> Our goal is not to get at the systems but to find all the ways to get there.

Before attempting exploitation, we should understand:

- How the target's infrastructure is structured.
- Which services and protocols are running.
- How different systems communicate.
- What security measures may be implemented.
- Which potential attack paths exist.

For example, if we discover SSH, RDP, or WinRM services, we should not immediately attempt brute-force attacks.

**Brute-force attacks are noisy** and may trigger security mechanisms, resulting in IP blacklisting or restricted access.

Instead, we should investigate the services, gather additional information, and determine the most appropriate next steps.

---

## The Three Enumeration Principles

### 1. There Is More Than Meets the Eye

We should consider different perspectives and investigate beyond what is immediately visible.

A discovered service may reveal additional information about the target's infrastructure.

### 2. Distinguish Between What We See and What We Do Not See

We must analyze both visible information and information that may be hidden.

For example, a service might not appear during a scan because of firewall rules or network filtering.

The absence of visible information does not necessarily mean that the service does not exist.

### 3. There Are Always Ways to Gain More Information

We should continuously expand our understanding of the target.

When we do not know how to proceed, the problem may be a lack of technical understanding rather than a lack of exploitation techniques.

Understanding how services and protocols work helps us discover additional information and potential attack paths.

---

## Key Takeaways

- Enumeration involves active and passive information gathering.
- OSINT is treated as a separate, passive-only process.
- Enumeration is iterative: each discovery can lead to further investigation.
- We should understand the infrastructure before attempting exploitation.
- Noisy techniques can trigger defensive mechanisms.
- Both visible and hidden information are important.
- Technical understanding is more valuable than blindly running tools.

**The goal of enumeration is to understand the target and discover potential attack paths before attempting exploitation.**