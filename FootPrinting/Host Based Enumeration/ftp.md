# FTP and TFTP

---

## FTP (File Transfer Protocol)

FTP is an application-layer protocol used to transfer files between a client and a server.

FTP uses two TCP connections:

- **Control Channel (TCP 21):** Used to exchange commands and server responses.
- **Data Channel:** Used to transfer files and directory listings.

### Active vs. Passive FTP

**Active FTP:**
- The client establishes the control connection.
- The server initiates the data connection to the client.
- Client-side firewalls may block this connection.

**Passive FTP:**
- The client establishes both connections.
- The server provides a port for the data connection.
- It is generally more compatible with client-side firewalls.

FTP traditionally transmits data and credentials without encryption.

---

## TFTP (Trivial File Transfer Protocol)

TFTP is a simplified file transfer protocol.

- Uses UDP (port 69).
- Does not provide user authentication.
- Does not support directory listing.
- Provides fewer features than FTP.
- Is commonly used in protected network environments.

---

## vsFTPd

vsFTPd (Very Secure FTP Daemon) is an FTP server implementation commonly used on Linux.

Its main configuration file is:

`/etc/vsftpd.conf`

### Important Settings

| Setting | Description |
|---|---|
| anonymous_enable=YES | Enables anonymous login |
| local_enable=YES | Allows local users to log in |
| write_enable=YES | Enables write commands |
| anon_upload_enable=YES | Allows anonymous uploads when other permissions permit |
| no_anon_password=YES | Disables the anonymous password prompt |
| hide_ids=YES | Hides real user/group IDs in listings |
| ls_recurse_enable=YES | Enables recursive directory listings |

The `/etc/ftpusers` file can contain users who are denied FTP access.

---

## Anonymous FTP

Some FTP servers allow users to authenticate using the `anonymous` account.

Depending on the server configuration, we may be able to:

- List directories.
- Download files.
- Upload files.
- Discover information about the server.

Anonymous access is not necessarily a vulnerability, but inappropriate permissions may expose sensitive information.

---

## Basic FTP Commands

| Command | Description |
|---|---|
| `ftp <IP>` | Connect to an FTP server |
| `ls` | List directory contents |
| `cd` | Change the remote directory |
| `get <file>` | Download a file |
| `put <file>` | Upload a file |
| `ls -R` | Recursively list directories, if supported |
| `status` | Show connection settings |
| `exit` | Close the connection |

---

## FTP Enumeration with Nmap

We can use Nmap to identify FTP services and gather information about their configuration.

```bash
sudo nmap -sV -sC -p21 <IP>
```

- `-sV`: Detects service versions.
- `-sC`: Executes default NSE scripts.
- `-p21`: Scans TCP port 21.

### Important NSE Scripts

- `ftp-anon`: Checks whether anonymous FTP access is allowed.
- `ftp-syst`: Retrieves FTP server information using the STAT command.

### FTP Response Codes

| Code | Meaning |
|---|---|
| 220 | Service ready |
| 230 | Login successful |
| 226 | Transfer completed |
| 530 | Not logged in |

---

## FTP Security

Potential FTP security issues include:

- Anonymous access to sensitive files.
- Excessive read or write permissions.
- Unencrypted credentials and data.
- Insecure server configurations.
- Sensitive information exposed through directory listings.

FTP over TLS (FTPS) can protect FTP communication using encryption.

---

## Key Takeaways

- FTP transfers files using separate control and data connections.
- Active and passive modes differ in who initiates the data connection.
- TFTP is simpler than FTP and uses UDP.
- vsFTPd is a common FTP server implementation.
- Anonymous FTP access can expose sensitive information if misconfigured.
- We can use Nmap and NSE scripts to enumerate FTP services.
- Understanding FTP permissions and configurations helps us identify potential security risks.