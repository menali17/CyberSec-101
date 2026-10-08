# IPMI (Intelligent Platform Management Interface)

---

## Overview

IPMI is a standardized interface for monitoring and managing server hardware remotely.

It operates independently of the host operating system through a dedicated management controller.

IPMI allows administrators to:

- Monitor hardware health and status.
- Power servers on or off remotely.
- Restart unresponsive systems.
- Access remote management consoles.
- Perform hardware maintenance and diagnostics.

IPMI can remain accessible when the main operating system is unavailable, provided the management controller has power and network connectivity.

---

## BMC (Baseboard Management Controller)

The BMC is a dedicated microcontroller responsible for out-of-band server management.

### IPMI vs. BMC

- **IPMI:** Defines management interfaces and protocols.
- **BMC:** Implements the hardware management functionality.

Common implementations include:

- Dell iDRAC
- HPE iLO
- Supermicro IPMI

Compromising a BMC may provide extensive control over the physical server.

---

## Default Ports

| Port | Protocol | Description |
|---|---|---|
| UDP 623 | IPMI | Remote management communication |

IPMI commonly uses RMCP/RMCP+ for network communication.

---

## IPMI Footprinting

### 1. Nmap Version Detection

```bash
sudo nmap -sU -p623 --script ipmi-version <IP>
```

- `-sU`: Performs a UDP scan.
- `-p623`: Scans the IPMI port.
- `--script ipmi-version`: Identifies IPMI version and authentication capabilities.

Example:

```text
623/udp open asf-rmcp
| ipmi-version:
|   Version:
|     IPMI-2.0
|_  Level: 2.0
```

### 2. Metasploit Version Detection

```text
msfconsole
use auxiliary/scanner/ipmi/ipmi_version
set RHOSTS <IP>
run
```

This module probes IPMI services and identifies available protocol information.

---

## Default Credentials

Some BMC implementations historically use default credentials.

| Product | Username | Password |
|---|---|---|
| Dell iDRAC | root | calvin |
| Supermicro IPMI | ADMIN | ADMIN |
| HPE iLO | Administrator | Device-specific randomized password |

Default credentials vary by model and configuration.

Leaving default credentials unchanged may lead to unauthorized administrative access.

---

## IPMI 2.0 RAKP Weakness

RAKP stands for Remote Authenticated Key-Exchange Protocol.

IPMI 2.0 uses RAKP during remote authentication.

The protocol can expose password-derived authentication material for valid user accounts before authentication is completed.

This information may be used in offline password-guessing attacks.

### Important Distinction

- Capturing a hash does not reveal the plaintext password automatically.
- Weak passwords are more susceptible to offline cracking.
- Strong, unique passwords significantly reduce this risk.

---

## IPMI Hash Enumeration

### Metasploit Module

```text
msfconsole
use auxiliary/scanner/ipmi/ipmi_dumphashes
set RHOSTS <IP>
run
```

The module attempts to retrieve IPMI 2.0 RAKP authentication hashes.

It may also test common passwords, depending on its configuration.

### Hashcat

IPMI 2.0 RAKP HMAC-SHA1 hashes use Hashcat mode `7300`.

```bash
hashcat -m 7300 <hash_file> <wordlist>
```

- `-m 7300`: IPMI2 RAKP HMAC-SHA1 hash format.
- `<hash_file>`: Captured authentication hashes.
- `<wordlist>`: Candidate passwords.

Offline cracking success depends on password strength and attack resources.

---

## Security Risks

Common IPMI security concerns include:

- Default or weak BMC credentials.
- Exposed remote management interfaces.
- IPMI 2.0 RAKP hash disclosure.
- Password reuse across critical systems.
- Excessive management permissions.
- Insufficient network segmentation.

Because BMCs provide hardware-level control, unauthorized access can have a severe impact.

---

## Security Recommendations

- Restrict management interfaces to trusted networks.
- Avoid exposing IPMI directly to the Internet.
- Replace default credentials.
- Use long, unique passwords.
- Apply appropriate access controls.
- Keep BMC firmware updated.

---

## Enumeration Methodology

1. Discover the IPMI service on UDP 623.
2. Identify the IPMI protocol version.
3. Enumerate advertised authentication capabilities.
4. Assess default credential risks when authorized.
5. Check for IPMI 2.0 RAKP hash exposure in authorized assessments.
6. Evaluate the security impact of identified findings.

**Our objective is to identify exposed hardware management interfaces and assess their potential security risks.**