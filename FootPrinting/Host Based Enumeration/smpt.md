# SMTP (Simple Mail Transfer Protocol)

---

## Overview

SMTP is an application-layer protocol used to send and relay emails across IP networks.

SMTP handles outgoing email communication, while IMAP and POP3 are used to retrieve messages.

### Email Components

- **MUA (Mail User Agent):** Application used to compose and send emails.
- **MSA (Mail Submission Agent):** Accepts messages submitted by clients.
- **MTA (Mail Transfer Agent):** Transfers and relays emails between servers.
- **MDA (Mail Delivery Agent):** Delivers messages to recipients' mailboxes.

### SMTP Ports

| Port | Description |
|---|---|
| TCP 25 | SMTP server-to-server communication |
| TCP 587 | Email submission, commonly using STARTTLS |
| TCP 465 | SMTP submission using implicit TLS |

SMTP is traditionally unencrypted.

We can use TLS to protect email communication.

---

## SMTP Commands

| Command | Description |
|---|---|
| `HELO` | Identifies the client and starts an SMTP session |
| `EHLO` | Initiates ESMTP and requests supported extensions |
| `AUTH` | Authenticates the client |
| `MAIL FROM` | Specifies the sender |
| `RCPT TO` | Specifies the recipient |
| `DATA` | Starts transferring message content |
| `VRFY` | Requests verification of a user or address |
| `EXPN` | Requests expansion of a mailing list |
| `RSET` | Resets the current mail transaction |
| `NOOP` | Requests a response without performing an operation |
| `QUIT` | Terminates the SMTP session |

### HELO vs. EHLO

- **HELO:** Standard SMTP greeting.
- **EHLO:** Extended SMTP greeting that advertises additional capabilities.

ESMTP supports extensions such as authentication and STARTTLS.

### STARTTLS

STARTTLS upgrades an existing plaintext SMTP connection to a TLS-encrypted connection.

---

## SMTP Configuration

Postfix is a commonly used mail server implementation on Linux.

Its main configuration file is:

`/etc/postfix/main.cf`

### Important Settings

| Setting | Description |
|---|---|
| `smtpd_banner` | Defines the server greeting banner |
| `myhostname` | Specifies the mail server hostname |
| `mydestination` | Defines domains for which the server accepts local delivery |
| `mynetworks` | Defines networks considered trusted |
| `inet_protocols` | Controls IPv4 and IPv6 support |

---

## SMTP Enumeration

We can interact with SMTP servers to identify supported capabilities and potentially exposed information.

### Connect with Telnet

```bash
telnet <IP> 25
```

### Identify Available Extensions

```text
EHLO inlanefreight.htb
```

The server may return supported commands and features.

### User Verification

```text
VRFY username
```

The `VRFY` command may provide information about user accounts.

However, responses are not always reliable.

For example, SMTP response code `252` does not guarantee that a user exists.

---

## Open Relay

An SMTP Open Relay occurs when a mail server allows unauthorized clients to relay messages to external recipients.

This misconfiguration may allow attackers to abuse the server for spam and email spoofing.

### Potentially Dangerous Configuration

```ini
mynetworks = 0.0.0.0/0
```

This configuration treats all IPv4 addresses as trusted networks in Postfix.

Depending on the remaining relay restrictions, it may result in an open relay.

---

## Footprinting SMTP with Nmap

### Service Enumeration

```bash
nmap -sV -sC -p25 <IP>
```

- `-sV`: Detects the SMTP service version.
- `-sC`: Executes default NSE scripts.
- `-p25`: Scans TCP port 25.

The `smtp-commands` NSE script can enumerate supported SMTP commands and extensions.

### Open Relay Detection

```bash
nmap -p25 --script smtp-open-relay <IP>
```

The script tests whether the server may permit unauthorized mail relay.

---

## SMTP Security

Common security concerns include:

- Open relay misconfigurations.
- Unencrypted SMTP communication.
- User enumeration through SMTP commands.
- Email spoofing.
- Weak authentication configurations.

### Email Security Mechanisms

- **SPF:** Specifies authorized sending sources.
- **DKIM:** Provides cryptographic signatures for email authentication.
- **DMARC:** Establishes domain-level policies using SPF/DKIM alignment.

---

## Key Takeaways

- SMTP is responsible for sending and relaying emails.
- IMAP and POP3 are used to retrieve emails.
- TCP ports 25, 587, and 465 are commonly associated with SMTP.
- `EHLO` helps us identify available SMTP capabilities.
- `VRFY` can provide user information, but responses may be unreliable.
- Open Relay is an important SMTP security misconfiguration.
- Nmap can help us enumerate SMTP services and test relay configurations.

**Our objective is to understand the SMTP service, identify its capabilities, and recognize potential security misconfigurations.**