# Nmap Scripting Engine

---

The **Nmap Scripting Engine (`NSE`)** allows us to extend Nmap with scripts written in **Lua**.

These scripts automate interactions with services. We can use them to retrieve banners, enumerate capabilities, gather application information, and check for specific vulnerabilities.

We do not need to write scripts to use NSE: Nmap includes many ready-to-use scripts.

Scripts are organized into categories. **A script can belong to more than one category.**

| Category | Purpose |
|---|---|
| `auth` | Investigates authentication mechanisms and related information. |
| `broadcast` | Discovers information through network broadcasts; some scripts support adding discovered targets when configured. |
| `brute` | Attempts to discover valid credentials through repeated authentication attempts. |
| `default` | Contains the default scripts selected with `-sC`. |
| `discovery` | Collects information about hosts, services, and resources. |
| `dos` | Checks denial-of-service conditions and may disrupt services. |
| `exploit` | Attempts to exploit specific vulnerabilities. |
| `external` | Uses external services to obtain or process information. |
| `fuzzer` | Sends unusual or varied input to investigate unexpected behavior. |
| `intrusive` | Contains scripts that may significantly affect the target. |
| `malware` | Checks for indicators of particular malware infections or backdoors. |
| `safe` | Contains scripts designed to avoid destructive or intrusive behavior. |
| `version` | Supports service and version detection. |
| `vuln` | Checks for specific vulnerabilities or gathers related evidence. |

The categories describe script behavior and purpose. **`Default` does not mean that every selected script belongs to `safe`.**

### Default Scripts

We can run the default script set with:

```bash
sudo nmap IP_DO_ALVO -sC
```

This is equivalent to selecting the `default` category:

```bash
sudo nmap IP_DO_ALVO --script default
```

Nmap runs applicable scripts according to their execution rules.

### Specific Script Category

We can select a category with `--script`:

```bash
sudo nmap IP_DO_ALVO --script discovery
```

Selecting a category does not mean every script will run against every port. Scripts check whether the target or service meets their execution conditions.

### Defined Scripts

We can select individual scripts by separating their names with commas:

```bash
sudo nmap IP_DO_ALVO --script banner,smtp-commands
```

This gives us more control over the interactions we want to perform.

### Nmap — Specifying Scripts

We can investigate the SMTP service on TCP port 25:

```bash
sudo nmap 10.129.2.28 -p 25 --script banner,smtp-commands
```

| Option | Purpose |
|---|---|
| `-p 25` | Scans TCP port 25. |
| `--script banner,smtp-commands` | Selects the two named scripts. |

Output from the supplied example:

```text
PORT   STATE SERVICE
25/tcp open  smtp
|_banner: 220 inlane ESMTP Postfix (Ubuntu)
|_smtp-commands: inlane, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8,
```

The scripts provide different information:

| Script | Information gathered |
|---|---|
| `banner` | The greeting sent by the service. |
| `smtp-commands` | Commands and extensions advertised by the SMTP server. |

The banner advertises **Postfix**, the name **`inlane`**, and an **Ubuntu** clue. As with manual banner grabbing, these are advertised details rather than guaranteed facts about the host.

Some advertised capabilities are particularly useful to investigate:

| Capability | Meaning |
|---|---|
| `SIZE 10240000` | Advertises a maximum message size of 10,240,000 bytes. |
| `STARTTLS` | Supports upgrading the connection to TLS. |
| `VRFY` | Advertises a command for verifying user or mailbox information. |
| `PIPELINING` | Supports sending multiple commands without waiting for each individual response. |

**Advertising `VRFY` does not guarantee that user enumeration will work.** The server may restrict it or return inconclusive responses.

### Nmap — Aggressive Scan

The `-A` option enables several features together:

| Included feature | Corresponding option |
|---|---|
| Service and version detection | `-sV` |
| Operating system detection | `-O` |
| Default NSE scripts | `-sC` |
| Traceroute | `--traceroute` |

Example:

```bash
sudo nmap 10.129.2.28 -p 80 -A
```

**`-A` does not automatically scan all ports or run every NSE script.** In this command, the selected port is still TCP port 80.

It also does not select a faster timing template.

The supplied example includes:

```text
80/tcp open http Apache httpd 2.4.29 ((Ubuntu))
|_http-generator: WordPress 5.3.4
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: blog.inlanefreight.com
```

We obtain:

- **Web server:** Apache httpd 2.4.29.
- **Advertised web application:** WordPress 5.3.4.
- **Page title:** `blog.inlanefreight.com`.

These findings come from different responses and page metadata. A page title that resembles a domain name is not, by itself, proof of a configured hostname.

The scan also attempts OS detection, but reports:

```text
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
No exact OS matches for host (test conditions non-ideal).
```

**OS detection works best when suitable open and closed TCP ports are available.** Here, the Linux results are guesses rather than an exact identification.

The percentages indicate fingerprint match confidence; we should not interpret them as a guaranteed statistical probability that the host runs a particular OS.

---

## Vulnerability Assessment

The **`vuln` category** helps us investigate known vulnerabilities and related security findings.

Example:

```bash
sudo nmap 10.129.2.28 -p 80 -sV --script vuln
```

| Option | Purpose |
|---|---|
| `-p 80` | Targets TCP port 80. |
| `-sV` | Identifies the service and version where possible. |
| `--script vuln` | Selects applicable scripts from the vulnerability category. |

The exact scripts and output depend on the installed script set, target responses, and execution conditions.

Selecting `vuln` is broader than selecting one specific check. Some scripts also belong to categories such as `intrusive` or `external`.

The supplied output contains several kinds of findings:

| Finding | What it tells us |
|---|---|
| `/wp-login.php` | A possible WordPress login page was found. |
| `/readme.html` | An accessible file may reveal application information. |
| `Username found: admin` | A script identified a likely WordPress username. |
| Apache version and CPE | The detected software can be correlated with vulnerability records. |
| CVE entries | Potentially relevant vulnerabilities require further investigation. |

**Different findings provide different levels of evidence.**

A discovered login page is an exposed resource, not automatically a vulnerability. An identified username is information disclosure, not proof that we can authenticate as that user.

The example also contains several WordPress version references associated with different files. These do not necessarily mean multiple WordPress versions are installed. They may reflect file signatures introduced in different releases.

A result such as:

```text
http-stored-xss: Couldn't find any stored XSS vulnerabilities.
```

Means that **this script did not identify stored XSS through its checks**. It does not prove that the application has no stored XSS vulnerabilities.

Similarly, a version-based CVE listing is a starting point for validation:

```text
CVE-2019-0211
CVE-2018-1312
CVE-2017-15715
```

Before concluding that a finding applies, we need to consider:

- The actual software and package version.
- Installed patches, including backported fixes.
- Required configuration or enabled modules.
- Authentication, access, or other exploitation prerequisites.

**NSE automates useful checks, but we still need to interpret and validate the results.**