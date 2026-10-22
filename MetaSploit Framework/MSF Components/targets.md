
# Payloads — Metasploit Framework

## Overview

A payload is code executed on a target system, typically after successful exploitation.

- **Exploit:** Takes advantage of a vulnerability.
- **Target:** Defines an exploitation profile.
- **Payload:** Defines the action to execute.

## Payload Types

### Singles (Stageless Payloads)

Singles are self-contained payloads that do not require downloading a separate stage.

Examples:

- `windows/shell_reverse_tcp`
- `windows/x64/meterpreter_reverse_tcp`

### Stagers

A stager is a small initial payload responsible for establishing a communication channel and receiving a larger stage.

In a reverse TCP configuration, the stager runs on the target and connects back to the attacker's listener.

### Stages

A stage is the larger component delivered after the stager establishes communication.

Examples include:

- Meterpreter
- Command shells
- VNC-based payloads

Some staged payloads also use intermediate components called middle stagers.

## Staged vs. Stageless

Staged:

`windows/x64/meterpreter/reverse_tcp`

Stageless:

`windows/x64/meterpreter_reverse_tcp`

Staged payloads separate connection establishment and delivery of the main functionality.

Stageless payloads contain their functionality in a single component.

## Reverse TCP vs. Bind TCP

### Reverse TCP

The target initiates a connection to the attacker's listener.

### Bind TCP

The target opens a listening port, and the attacker initiates the connection.

## Meterpreter

Meterpreter is an advanced Metasploit payload that provides an interactive interface with commands for system inspection, session management, and file system interaction.

Useful commands:

- `help` — Display available commands.
- `sysinfo` — Display system information.
- `getuid` — Identify the current user.
- `pwd` — Display the current directory.
- `ls` — List files.
- `cd` — Change directory.
- `shell` — Open a native operating system shell.

Meterpreter is not identical to Windows CMD.

## Searching for Payloads

```text
show payloads
grep meterpreter show payloads
grep meterpreter grep reverse_tcp show payloads
```

## Selecting a Payload

```text
set PAYLOAD windows/x64/meterpreter/reverse_tcp
show options
```

The selected payload must be compatible with the exploit module and target environment.

## Key Takeaways

- Singles are self-contained.
- Stagers establish communication and receive stages.
- Stages provide the main payload functionality.
- Reverse and bind describe connection direction.
- Meterpreter provides an advanced interactive session.
- A successful exploit does not guarantee a successful payload session.
