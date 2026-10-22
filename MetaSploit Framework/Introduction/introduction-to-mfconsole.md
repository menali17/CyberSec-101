
# Introduction to MSFconsole

## Starting MSFconsole

`msfconsole` is the main command-line interface of the Metasploit Framework.

```bash
msfconsole
msfconsole -q
```

- `-q`: Starts MSFconsole without displaying the startup banner.
- `help`: Displays available commands.

### Updating Metasploit

On supported APT-based installations:

```bash
sudo apt update && sudo apt install metasploit-framework
```

`apt update` refreshes package metadata, while `apt install` installs or updates the selected package when a newer candidate is available.

## Enumeration Before Exploitation

Enumeration is the process of gathering information about a target before attempting exploitation.

Important information includes:

- Open ports
- Running services
- Service versions
- Operating systems
- Potential vulnerabilities

Example:

```bash
nmap -sV -sC <target_ip>
```

Service versions help identify potential vulnerabilities, but version information alone does not confirm that a target is vulnerable.

## MSF Engagement Structure

### 1. Enumeration

Identify services and research potential vulnerabilities.

- Passive Scanning: OSINT, Whois, public DNS records.
- Active Scanning: Nmap, vulnerability scanners, service identification.
- Vulnerability Research: SearchSploit, vulnerability databases, Metasploit modules.

```text
search <keyword>
```

### 2. Preparation

Prepare and validate the selected module.

- Code auditing
- Dependency checks
- Importing custom modules
- Reviewing potential side effects

### 3. Exploitation

Configure and execute a module against an authorized target.

Important commands:

- `use`: Select a module.
- `show options`: Display configuration options.
- `set`: Configure a parameter.
- `show payloads`: List compatible payloads.
- `show targets`: List supported target profiles.
- `run`: Execute a module.
- `exploit`: Execute an exploit module.

Common parameters:

- `RHOSTS`: Remote target hosts.
- `RPORT`: Remote target port.
- `LHOST`: Local host address for applicable payloads.
- `LPORT`: Local port for applicable payloads.

### 4. Privilege Escalation

Identify ways to gain higher privileges after initial access.

Possible activities include:

- Vulnerability research
- Credential analysis
- Token impersonation

### 5. Post-Exploitation

Evaluate the impact of successful exploitation within the authorized scope.

- Pivoting
- Credential exposure assessment
- Data exposure validation
- Session management
- Cleanup

## Basic MSFconsole Workflow

```text
search type:auxiliary ftp
use auxiliary/scanner/ftp/ftp_version
show options
set RHOSTS <target_ip>
run
```

This example performs FTP service enumeration without exploiting a vulnerability.

## Key Takeaways

- Enumeration must precede exploitation.
- Metasploit supports more than vulnerability exploitation.
- Modules require appropriate configuration.
- Payloads and targets must be compatible with the selected exploit.
- Privilege escalation is different from initial access.
- Post-exploitation activities must remain within the authorized scope.
- The engagement workflow is iterative rather than strictly linear.
