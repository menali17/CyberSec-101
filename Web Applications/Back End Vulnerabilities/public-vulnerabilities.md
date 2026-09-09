# Public Vulnerabilities

## Overview

Some of the most critical back-end vulnerabilities are those that can be exploited remotely without requiring local access to the server.

These vulnerabilities may result from:

* Coding mistakes
* Vulnerable application versions
* Vulnerable plugins or components
* Misconfigurations
* Vulnerable web servers

During a penetration test, identifying known public vulnerabilities is an important part of enumeration.

---

# Public CVEs

A **CVE (Common Vulnerabilities and Exposures)** is a publicly identified security vulnerability.

When vulnerabilities are discovered in widely used applications, they may be:

```text
Discovered
   ↓
Reported
   ↓
Patched
   ↓
Assigned a CVE
   ↓
Given a severity score
```

Security researchers may also publish **Proof of Concept (PoC)** exploits demonstrating how the vulnerability works.

---

# First Step: Identify the Version

Before searching for public exploits, we should determine the application's version.

Example:

```text
WordPress
Version 6.x.x
```

Version information may be found in:

* Page source
* Application files
* Version pages
* HTTP responses
* Open-source repositories
* Application metadata

For open-source applications, we can inspect the official repository to identify where version information is stored and then check the same location on the target.

---

# Searching for Public Exploits

Once we know:

```text
Application + Version
```

we can search for known vulnerabilities and public exploits.

Common resources include:

* Exploit DB
* Rapid7 DB
* Vulnerability Lab
* CVE databases

Conceptually:

```text
Identify Technology
       ↓
Identify Version
       ↓
Search for CVEs
       ↓
Search for Public Exploits
       ↓
Determine Applicability
```

---

# External Components

We should not only search for vulnerabilities in the main application.

Web applications often use external components such as:

```text
Plugins
Libraries
Extensions
Modules
```

These components may contain vulnerabilities even if the main application itself is secure.

Example:

```text
WordPress
   ↓
Plugin
   ↓
Vulnerable Plugin Version
   ↓
Possible Exploit
```

---

# Interesting Public Vulnerabilities

The module highlights vulnerabilities with:

```text
High CVSS scores
```

especially those that may lead to:

```text
Remote Code Execution (RCE)
```

However, lower-severity vulnerabilities may still be useful if they can be chained with other weaknesses.

---

# CVSS

**CVSS (Common Vulnerability Scoring System)** is a scoring system used to describe the severity of security vulnerabilities.

Scores range from:

```text
0.0 → 10.0
```

Higher values generally represent more severe vulnerabilities.

CVSS helps organizations prioritize which vulnerabilities should be addressed first.

---

# CVSS Metrics

The module divides CVSS scoring into three groups:

```text
Base
Temporal
Environmental
```

## Base Metrics

Describe the inherent characteristics of the vulnerability.

They generate the main vulnerability score.

---

## Temporal Metrics

Consider factors that may change over time.

For example:

```text
Exploit availability
Patch availability
Current vulnerability status
```

---

## Environmental Metrics

Adjust the score according to the specific environment in which the vulnerability exists.

The same vulnerability may have different practical importance in different organizations.

Conceptually:

```text
Same CVE

Company A
→ Low impact

Company B
→ Critical system
→ Much greater impact
```

---

# CVSS v3 Severity

The module presents the following ranges:

| Severity |        Score |
| -------- | -----------: |
| None     |        `0.0` |
| Low      |  `0.1 - 3.9` |
| Medium   |  `4.0 - 6.9` |
| High     |  `7.0 - 8.9` |
| Critical | `9.0 - 10.0` |

A simple way to remember:

```text
0        → None
1 - 3.9  → Low
4 - 6.9  → Medium
7 - 8.9  → High
9 - 10   → Critical
```

---

# CVE vs CVSS

These two terms should not be confused.

```text
CVE
→ Identifies the vulnerability

CVSS
→ Measures its severity
```

For example:

```text
CVE-XXXX-XXXXX
        ↓
Specific vulnerability

CVSS: 9.8
        ↓
Severity score
```

---

# Proof of Concept (PoC)

A **Proof of Concept exploit** demonstrates that a vulnerability can be exploited.

Researchers may release PoCs for:

* Testing
* Research
* Education
* Vulnerability validation

During a penetration test, a public PoC can help us determine whether a known vulnerability applies to our target.

However:

```text
CVE exists
≠
Target is automatically vulnerable
```

We still need to verify:

```text
Correct application
Correct version
Correct configuration
Correct vulnerable component
```

---

# Back-End Server Vulnerabilities

We should also search for vulnerabilities affecting back-end components.

Examples include:

```text
Web Server
Operating System
Database
Libraries
Services
```

Web servers are especially important because they may be directly exposed to the Internet.

Examples:

```text
Apache
NGINX
IIS
```

---

# External vs Internal Vulnerabilities

Some vulnerabilities can be exploited directly from the Internet:

```text
External Attacker
      ↓
Public Service
      ↓
Remote Exploit
      ↓
Server Compromise
```

Other vulnerabilities may only become useful after we gain access to the internal system.

```text
Initial Foothold
      ↓
Internal Access
      ↓
Local Vulnerability
      ↓
Privilege Escalation
```

or:

```text
Initial Foothold
      ↓
Internal Network
      ↓
Attack Other Servers
```

---

# Vulnerability Research Workflow

A useful workflow is:

```text
Identify Application
        ↓
Identify Version
        ↓
Identify Components / Plugins
        ↓
Search CVEs
        ↓
Check CVSS
        ↓
Search for PoCs / Exploits
        ↓
Verify Target Is Vulnerable
        ↓
Test Exploit
```

---

# Pentesting Perspective

When we identify a technology, we should ask:

```text
What application is this?

Which version is running?

Which plugins or modules are installed?

Are there known CVEs for this version?

What is the CVSS severity?

Is there a public exploit?

Does the exploit lead to RCE?

Does the vulnerability actually apply to this target?
```

One of the most important habits is:

```text
Technology
    ↓
Version
    ↓
CVE
    ↓
PoC / Exploit
```

---

# Key Takeaways

* Public vulnerabilities may allow remote compromise of web applications.
* CVEs identify publicly disclosed vulnerabilities.
* We should identify the application's version before searching for exploits.
* External components and plugins should also be checked.
* Public exploits and PoCs may be available for known vulnerabilities.
* CVSS represents vulnerability severity.
* CVE and CVSS are different concepts.
* High and Critical vulnerabilities should receive particular attention.
* RCE vulnerabilities are especially important.
* Web servers and other back-end components may also contain public vulnerabilities.
* A CVE matching the software does not automatically prove that the target is exploitable.
* We must verify the version, configuration, and vulnerable component before attempting exploitation.

---

# Pentesting Mindset

A useful mental model is:

```text
"What is running?"
       ↓
"What version?"
       ↓
"Is it known to be vulnerable?"
       ↓
"Is there a CVE?"
       ↓
"Is there a public exploit?"
       ↓
"Does it actually work against this target?"
```

Public vulnerability research is often one of the first steps after identifying the technologies used by a target.
