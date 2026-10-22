
# Metasploit — Module Configuration and Exploitation

## Selecting a Module

Modules can be selected using their full path or their index from search results.

```text
use exploit/windows/smb/ms17_010_psexec
```

### Inspecting Module Information

```text
info
```

The `info` command displays:

- Module name and description
- Supported platforms and architectures
- Module reliability rank
- Available targets
- Required configuration options
- Vulnerability references
- Check support

## Configuring Module Options

```text
show options
set RHOSTS <target_ip>
```

### Local vs. Global Settings

- `set`: Configures an option in the selected module.
- `setg`: Defines a global option for reuse across modules.
- `unset`: Removes a local option.
- `unsetg`: Removes a global option.

Global settings normally remain active during the current Metasploit session.

## Important Parameters

| Parameter | Description |
|---|---|
| RHOSTS | Remote target hosts |
| RPORT | Remote service port |
| LHOST | Local address for applicable reverse connections |
| LPORT | Local listening port |
| SMBUser | SMB authentication username |
| SMBPass | SMB authentication password |
| SHARE | SMB share used by the module |

## Exploit vs. Payload

An **exploit** takes advantage of a vulnerability.

A **payload** defines the code or action executed during exploitation.

Example payload:

`windows/meterpreter/reverse_tcp`

- `windows`: Target platform
- `meterpreter`: Metasploit interaction payload
- `reverse_tcp`: TCP connection initiated from the target to the listener

## Reverse Connection

In a typical reverse TCP connection:

1. The attacker starts a listener.
2. The exploit is executed against the target.
3. A successfully executed payload connects back to the listener.
4. Metasploit establishes a session.

## Running Modules

```text
run
```

Typical output indicators:

- `[*]`: Informational message
- `[+]`: Positive result
- `[-]`: Failure or negative result
- `[!]`: Warning

## Sessions

A successful exploit may establish a shell or Meterpreter session.

Inside Meterpreter:

```text
shell
```

Inside a Windows shell:

```cmd
whoami
```

The command displays the current security identity.

`NT AUTHORITY\SYSTEM` represents a highly privileged Windows security context.

## Key Takeaways

- Always review module information before execution.
- Configure the required parameters.
- Understand the distinction between remote and local addresses.
- A vulnerability check is not the same as successful exploitation.
- Exploits and payloads have different responsibilities.
- Successful exploitation does not guarantee administrative privileges.
- Read execution logs carefully to understand what actually happened.
