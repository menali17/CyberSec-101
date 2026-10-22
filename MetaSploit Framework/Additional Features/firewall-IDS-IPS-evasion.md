
# Firewall and IDS/IPS Evasion — Metasploit Framework

## 1. Introduction

To understand how attackers attempt to evade security controls, we first need to understand the mechanisms used to protect systems and networks.

The lesson introduces two major protection categories:

- **Endpoint Protection**
- **Perimeter Protection**

These controls operate at different locations and often complement one another.

---

## 2. Endpoint Protection

**Endpoint Protection** refers to security mechanisms designed to protect individual devices.

Endpoints may include:

- Personal computers
- Corporate workstations
- Servers
- Other network-connected devices

Common endpoint security capabilities include:

- Antivirus (AV)
- Antimalware
- Host-based firewalls
- Endpoint Detection and Response (EDR)
- Behavioral monitoring

### Antivirus vs. EDR

**Antivirus (AV)** primarily focuses on preventing, detecting, and removing malicious software.

**Endpoint Detection and Response (EDR)** provides broader visibility into endpoint activity, including process execution, system changes, and suspicious behavior.

Modern endpoint protection may combine both technologies.

Examples of endpoint security products include Microsoft Defender, Bitdefender, and Malwarebytes.

**Key Point:** Endpoint protection operates on individual hosts and can inspect files, processes, and system behavior.

---

## 3. Perimeter Protection

**Perimeter Protection** refers to security controls positioned between networks with different trust levels.

These controls typically inspect or restrict communication between internal and external networks.

Common examples include:

- Network firewalls
- Intrusion Detection Systems (IDS)
- Intrusion Prevention Systems (IPS)
- Network security gateways

### Network Security Zones

The lesson introduces three network zones:

| Zone | Description |
|---|---|
| Outside | External networks, such as the Internet |
| DMZ | Network segment hosting publicly accessible services |
| Inside | Internal organizational network |

### DMZ (Demilitarized Zone)

A **DMZ** is a network segment separating public-facing services from the internal network.

It commonly hosts:

- Web servers
- Mail gateways
- Public DNS servers
- Other externally accessible services

The purpose of a DMZ is to isolate public services and limit the impact of a compromise.

**Important:** A DMZ is not automatically trusted. Traffic between the DMZ and internal networks should be restricted through explicit security policies.

---

## 4. Security Policies

Security policies define which activities are permitted or denied within an environment.

They can control:

- Network traffic
- Application behavior
- User access
- File operations
- DDoS protection

Many security policies follow principles similar to **Access Control Lists (ACLs)**.

An ACL generally contains rules that permit or deny actions based on predefined conditions.

### Example

```text
ALLOW  Internal_Network -> Web_Server : TCP/443
DENY   Internet -> Internal_Network : ANY
```

The first rule allows internal clients to reach a web server using HTTPS.

The second denies direct incoming connections from the Internet to the internal network.

Actual policy evaluation depends on the firewall platform, rule ordering, and connection state.

---

## 5. Security Detection Methods

The lesson introduces four approaches to identifying malicious activity.

### 5.1 Signature-Based Detection

**Signature-based detection** compares files, traffic, or activity against known malicious patterns.

A signature may represent:

- A specific byte sequence
- A known malware pattern
- A recognizable network payload
- A particular attack characteristic

When a matching pattern is detected, the security solution may generate an alert or block the activity.

**Advantages:**
- Effective against known threats.
- Often fast and efficient.
- Can provide precise detections.

**Limitations:**
- Unknown threats may not match existing signatures.
- Signatures require maintenance and updates.
- Modified samples may behave differently during detection.

### 5.2 Heuristic / Statistical Anomaly Detection

**Anomaly detection** identifies activity that differs from an established baseline.

A baseline represents normal behavior in an environment.

Examples of unusual behavior include:

- Unexpected outbound connections
- Sudden increases in network traffic
- Unusual authentication patterns
- Abnormal process activity

Unlike signature-based detection, anomaly detection does not always require an exact match against previously identified malicious code.

**Limitation:** Legitimate unusual behavior can also generate false positives.

### 5.3 Stateful Protocol Analysis

**Stateful Protocol Analysis** examines network communication in the context of expected protocol behavior.

Rather than inspecting isolated packets only, it considers protocol states and communication sequences.

For example, an analyzer may detect traffic that violates the expected behavior of an established protocol.

This can reveal malformed or suspicious communications.

### 5.4 Live Monitoring and SOC Alerting

A **Security Operations Center (SOC)** monitors security activity and investigates potential threats.

Security analysts may use:

- SIEM platforms
- IDS/IPS alerts
- EDR telemetry
- Network monitoring tools
- Threat intelligence

The SOC investigates alerts, correlates events, and determines whether additional response actions are required.

---

## 6. IDS vs. IPS

Although closely related, IDS and IPS perform different roles.

| Feature | IDS | IPS |
|---|---|---|
| Full name | Intrusion Detection System | Intrusion Prevention System |
| Main purpose | Detect suspicious activity | Detect and prevent suspicious activity |
| Typical response | Generate alerts | Block or reject traffic |
| Deployment | Often passive or monitoring-oriented | Often inline |
| Traffic blocking | Not inherently | Supported |

### IDS

An IDS analyzes activity and reports potential threats.

It does not inherently block malicious traffic.

### IPS

An IPS can actively prevent suspicious communication by enforcing security rules.

**Key Point:** Detection identifies suspicious activity, while prevention attempts to stop it.

---

## 7. Evasion Techniques

**Evasion** refers to techniques intended to reduce the likelihood that security controls detect or prevent malicious activity.

The lesson discusses several categories:

- Payload encoding
- Encrypted communications
- Executable templates
- Archives
- Packers
- Exploit pattern modification

Understanding these techniques is useful for identifying suspicious files, network activity, and exploitation attempts.

### 7.1 Payload Encoding

Encoding transforms the representation of data.

Metasploit includes encoders that can transform payload bytes.

The lesson mentions:

```text
x86/shikata_ga_nai
```

This is a polymorphic XOR additive feedback encoder.

Its output can vary while preserving the intended decoded payload behavior.

**Important:** Encoding is not equivalent to encryption, and it does not guarantee antivirus evasion.

Security products can also inspect decoded content and runtime behavior.

### 7.2 Encrypted Communications

The lesson discusses Meterpreter's use of AES-encrypted communication.

Encryption protects the content of transmitted data from straightforward inspection.

However, encrypted traffic may still be analyzed using:

- Source and destination addresses
- Connection timing
- Traffic volume
- Protocol metadata
- Endpoint telemetry
- Traffic fingerprinting

**Key Point:** Encryption provides confidentiality but does not guarantee that communication will evade IDS/IPS detection.

---

## 8. MSFvenom and Executable Templates

**MSFvenom** is a Metasploit utility used to generate and configure payloads.

The lesson introduces executable templates, which allow generated output to be based on an existing executable structure.

It discusses the concept of a **backdoored executable**: an application containing additional unauthorized functionality.

### Relevant MSFvenom Options

| Option | Description |
|---|---|
| `-p` | Select a payload |
| `LHOST` | Configure the local host address |
| `LPORT` | Configure the local listening port |
| `-x` | Specify an executable template |
| `-k` | Request preservation of template execution behavior |
| `-e` | Select an encoder |
| `-i` | Specify encoder iterations |
| `-a` | Select an architecture |
| `--platform` | Specify a target platform |
| `-f` | Choose an output format |
| `-o` | Specify an output file |

### Defensive Considerations

Executable templates can be abused to disguise malicious functionality inside apparently legitimate applications.

From a defensive perspective, relevant indicators include:

- Unexpected executable modifications
- Invalid digital signatures
- Suspicious child processes
- Unusual network connections
- Unexpected executable sections

**Important:** An executable retaining some legitimate functionality does not establish that it is safe.

---

## 9. Archives

Archives package one or more files into a single container.

Common archive formats include:

- ZIP
- RAR
- 7z

Archives can also support password protection or encryption.

### Why Archives Matter to Security

The lesson explains that encrypted archives can limit the ability of some security products to inspect their contents before extraction.

This creates challenges for automated file scanning.

However:

- Encrypted archives may be treated as suspicious.
- Security gateways may block files they cannot inspect.
- Files may be scanned after extraction.
- Renaming an archive does not change its internal format.

### Defensive Analysis

When inspecting a suspicious archive, we should examine:

- The actual file format
- Whether encryption is enabled
- Nested archive structures
- Extracted filenames and file types
- The provenance of the archive

A file extension alone is not sufficient to establish its true format.

---

## 10. VirusTotal

**VirusTotal** is a service that analyzes submitted files, URLs, and other indicators using multiple detection engines.

The lesson uses it to compare detection results for two different file representations.

### Example Results from the Lesson

| Sample | Detection Result |
|---|---|
| Original test sample | 11 / 59 |
| Password-protected archive sample | 0 / 49 |

These are historical results presented in the lesson, using security engines from 2022.

### Understanding Detection Ratios

A detection ratio such as:

```text
11 / 59
```

means that 11 of the 59 reporting engines classified the sample as suspicious or malicious.

A result such as:

```text
0 / 49
```

does **not** necessarily mean the underlying content is safe.

For encrypted archives, scanners may be unable to examine the enclosed files.

The two ratios also involve different submitted file representations and different numbers of reporting engines.

### Important Considerations

- Detection results may change as signatures and engines are updated.
- A clean result is not proof that a file is harmless.
- Some engines may fail to inspect encrypted or unsupported content.
- Public submissions may expose samples and metadata to third parties.

**Key Point:** VirusTotal results are supporting evidence, not definitive proof of safety.

---

## 11. Packers

A **Packer** transforms an executable into a packed representation.

A typical packed executable contains:

1. Packed or compressed program data.
2. A routine that restores the program during execution.
3. Information required to run the unpacked program.

### How Packers Work

```text
Original Executable
        |
        v
Packing / Compression
        |
        v
Packed Executable
        |
        v
Runtime Unpacking
        |
        v
Program Execution
```

Packers can have legitimate uses, including reducing file size and software protection.

They may also be abused to conceal malicious executable content from basic static inspection.

### Examples Mentioned in the Lesson

- UPX
- Themida
- MPRESS
- Enigma Protector
- ExeStealth
- Morphine

### Defensive Perspective

Security analysts may look for:

- High-entropy executable sections
- Unusual executable headers
- Runtime memory unpacking
- Suspicious process behavior
- Unexpected memory permissions

**Important:** Packing, compression, encryption, and obfuscation are related but distinct concepts.

Not every packed executable is malicious, and not every packer encrypts its content.

---

## 12. Exploit Coding and Detection

The final part of the lesson discusses how exploitation patterns can be identified by security solutions.

One example is a **Buffer Overflow (BoF)**.

A buffer overflow occurs when data is written outside the intended bounds of a memory buffer.

Depending on the vulnerability and memory protections, this may result in:

- Application crashes
- Memory corruption
- Unexpected program behavior
- Potential code execution

### Recognizable Exploit Patterns

IDS/IPS products may detect characteristic patterns associated with known exploits.

These include recognizable payload structures and abnormal protocol data.

### Offset

An **offset** usually describes a position or displacement within data or memory.

In buffer overflow research, offsets can be used to determine where particular values affect program execution.

### NOP Sled

A **NOP sled** is a sequence of no-operation instructions.

Historically, NOP sleds were used in certain memory corruption exploits to increase the range of addresses that could lead execution toward a payload.

Recognizable NOP sequences can also provide useful detection indicators.

**Key Point:** Exploitation attempts can leave patterns in network traffic, application behavior, and memory that defenders can analyze.

---

## 13. Defensive Lessons

Although this HTB section focuses on evasion, it introduces several important defensive principles.

1. We should not rely exclusively on signature-based detection.
2. Encrypted traffic can still reveal suspicious metadata and behavior.
3. Endpoint monitoring complements network-level security.
4. Password-protected archives create inspection challenges.
5. Packed executables are not automatically malicious, but may require additional analysis.
6. File extensions cannot be trusted as the sole indicator of file type.
7. VirusTotal detection ratios should be interpreted cautiously.
8. Exploit payloads may have recognizable behavioral or structural characteristics.
9. Layered security controls reduce dependence on any single detection method.
10. SOC analysts should correlate alerts across multiple telemetry sources.

---

## 14. Key Takeaways

- **Endpoint Protection** protects individual devices.
- **Perimeter Protection** controls communication between networks.
- **DMZs** isolate public-facing services from internal systems.
- **Security Policies** define permitted and prohibited actions.
- **Signature Detection** identifies recognizable malicious patterns.
- **Anomaly Detection** identifies deviations from expected behavior.
- **Stateful Protocol Analysis** examines communication against expected protocol behavior.
- **IDS** detects and alerts; **IPS** can actively block.
- **Payload Encoding** changes data representation but does not guarantee evasion.
- **AES Encryption** protects communication content without making traffic invisible.
- **Executable Templates** can be abused to disguise additional functionality.
- **Archives** may complicate automated inspection.
- **Packers** transform executables and can complicate static analysis.
- **VirusTotal** provides detection evidence but cannot definitively establish file safety.
- **Buffer Overflow Patterns** and **NOP Sleds** can provide useful detection indicators.
