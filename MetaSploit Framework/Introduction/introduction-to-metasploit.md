
# Introduction to Metasploit

## Metasploit Framework

Metasploit Framework (MSF) is an open-source, Ruby-based, modular penetration testing platform used to identify, validate, and exploit vulnerabilities.

It provides reusable modules for exploitation, network enumeration, payload execution, and post-exploitation activities.

## Core Concepts

- **Exploit:** Code or technique used to take advantage of a vulnerability.
- **Payload:** Code or action executed as part of an exploitation process.
- **Session:** An established interaction channel with a target system, such as a shell or Meterpreter session.
- **Module:** A reusable component designed to perform a specific security-related task.

Not every exploit uses a separate payload or creates an interactive session.

## Metasploit Versions

- **Metasploit Framework:** Free, open-source, community-driven, and primarily command-line oriented.
- **Metasploit Pro:** Commercial version offering additional automation, reporting, GUI, and collaboration capabilities.

## MSFconsole

`msfconsole` is the main command-line interface for the Metasploit Framework.

```bash
msfconsole
```

Common commands:

- `help` — Display available commands.
- `search` — Search for modules.
- `info` — Display module information.
- `use` — Select a module.
- `show options` — Display module configuration options.
- `back` — Leave the selected module.
- `exit` — Exit the console.

## Framework Architecture

Typical installation directory:

`/usr/share/metasploit-framework/`

### Core Directories

- `data/` — Framework data and resources.
- `documentation/` — Technical documentation.
- `lib/` — Core Ruby libraries.
- `modules/` — Exploitation, enumeration, and other security modules.
- `plugins/` — Extensions and integrations.
- `scripts/` — Supporting scripts, including Meterpreter-related resources.
- `tools/` — Standalone command-line utilities.

### Module Categories

- `auxiliary` — Scanning, enumeration, and supporting tasks.
- `exploits` — Vulnerability exploitation.
- `payloads` — Actions executed during exploitation.
- `post` — Post-exploitation activities.
- `encoders` — Payload representation transformations.
- `nops` — No-operation instruction generation.
- `evasion` — Techniques intended to evade selected defenses.

## Key Takeaways

- Metasploit is a modular framework, not an automatic solution for every vulnerability.
- Exploits, payloads, and sessions serve different purposes.
- MSFconsole is the primary interface for interacting with the Framework.
- Understanding the architecture makes it easier to inspect, customize, and develop modules.
- Always verify module compatibility and potential effects before execution.
  