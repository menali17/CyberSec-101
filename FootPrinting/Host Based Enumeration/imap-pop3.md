# IMAP / POP3

---

## Overview

IMAP and POP3 are application-layer protocols used to access emails stored on mail servers.

Unlike SMTP, which is responsible for sending emails, IMAP and POP3 are primarily used for retrieving messages.

### IMAP (Internet Message Access Protocol)

IMAP allows us to access and manage emails directly on the server.

Features include:

- Synchronization across multiple devices.
- Server-side mailbox and folder management.
- Reading and organizing messages.
- Support for offline synchronization through compatible clients.

### POP3 (Post Office Protocol v3)

POP3 is a simpler protocol focused on retrieving emails from a server.

Its main operations include:

- Listing messages.
- Downloading emails.
- Deleting messages.

Unlike IMAP, POP3 does not provide advanced server-side mailbox synchronization.

---

## IMAP vs. POP3

| Feature | IMAP | POP3 |
|---|---|---|
| Email retrieval | Yes | Yes |
| Multi-device synchronization | Yes | Limited |
| Server-side folders | Yes | No |
| Message management | Advanced | Basic |
| Default ports | 143 / 993 | 110 / 995 |

---

## Important Ports

| Port | Service |
|---|---|
| 110/TCP | POP3 |
| 143/TCP | IMAP |
| 993/TCP | IMAPS (TLS) |
| 995/TCP | POP3S (TLS) |

IMAP and POP3 can use TLS to protect communication and authentication credentials.

---

## IMAP Commands

IMAP commands use unique tags to associate server responses with client requests.

| Command | Description |
|---|---|
| `LOGIN` | Authenticates a user |
| `LIST` | Lists available mailboxes |
| `SELECT` | Selects a mailbox |
| `FETCH` | Retrieves message data |
| `CREATE` | Creates a mailbox |
| `DELETE` | Deletes a mailbox |
| `RENAME` | Renames a mailbox |
| `LOGOUT` | Terminates the session |

### Example

```text
A001 LOGIN robin robin
A002 LIST "" "*"
A003 SELECT INBOX
A004 FETCH 1 BODY[]
A005 LOGOUT
```

---

## POP3 Commands

| Command | Description |
|---|---|
| `USER` | Specifies the username |
| `PASS` | Specifies the password |
| `STAT` | Retrieves mailbox statistics |
| `LIST` | Lists messages and their sizes |
| `RETR` | Retrieves an email |
| `DELE` | Marks a message for deletion |
| `CAPA` | Displays server capabilities |
| `RSET` | Resets deletion marks |
| `QUIT` | Closes the session |

### Example

```text
USER robin
PASS robin
STAT
LIST
RETR 1
QUIT
```

---

## Service Enumeration

### Nmap

We can enumerate the common IMAP and POP3 ports:

```bash
nmap -sV -sC -p110,143,993,995 <IP>
```

The scan may reveal:

- Service names and versions.
- Supported capabilities.
- SSL/TLS certificate information.
- Hostnames and organization details.

### OpenSSL — IMAPS

```bash
openssl s_client -connect <IP>:993 -quiet
```

### OpenSSL — POP3S

```bash
openssl s_client -connect <IP>:995 -quiet
```

We can use OpenSSL to establish encrypted connections and interact directly with the services.

### cURL — IMAPS

```bash
curl -k 'imaps://<IP>' --user robin:robin
```

- `imaps://`: Uses IMAP over TLS.
- `--user`: Supplies authentication credentials.
- `-k`: Disables certificate validation, which may be necessary for self-signed certificates in a laboratory environment.

---

## Dovecot

Dovecot is a mail server implementation that supports IMAP and POP3.

### Security-Related Settings

| Setting | Description |
|---|---|
| `auth_debug` | Enables authentication debugging |
| `auth_debug_passwords` | Enables detailed authentication logging that may include passwords |
| `auth_verbose` | Logs authentication failures |
| `auth_verbose_passwords` | Controls logging of passwords submitted during authentication |
| `auth_anonymous_username` | Configures the username used for anonymous SASL authentication |

Improper logging and authentication configurations may expose sensitive information.

---

## Security Considerations

Common IMAP/POP3 security risks include:

- Unencrypted authentication.
- Weak or reused credentials.
- Exposed email contents.
- Insecure logging configurations.
- Misconfigured authentication mechanisms.
- Information disclosure through TLS certificates.

---

## Key Takeaways

- SMTP sends emails, while IMAP and POP3 retrieve them.
- IMAP supports advanced mailbox management and synchronization.
- POP3 provides basic message retrieval functionality.
- Ports 993 and 995 provide implicit TLS encryption.
- Nmap helps identify mail services and their capabilities.
- OpenSSL allows us to interact directly with encrypted mail services.
- Misconfigurations may expose sensitive emails or authentication data.

**Our objective is to identify accessible mail services, understand their capabilities, and recognize potential security misconfigurations.**