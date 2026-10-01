
# Windows Command Line — Introduction

---

## Overview

Windows provides two built-in command-line environments: **Command Prompt (CMD)** and **PowerShell**. Both allow us to interact directly with the operating system, automate administrative tasks, and manage system resources without relying on a graphical interface.

From a penetration testing perspective, these tools are useful for reconnaissance, enumeration, exploitation, and post-exploitation activities within Windows environments.

---

## Command Prompt vs. PowerShell

Although CMD and PowerShell serve similar purposes, they differ significantly in their capabilities.

| Feature | Command Prompt (CMD) | PowerShell |
|---|---|---|
| Introduced | 1981 (command-line predecessor) | 2006 |
| Primary purpose | Command execution and batch scripting | Administration, automation, and scripting |
| Output | Plain text | Structured objects |
| Scripting | Batch files (`.bat`, `.cmd`) | PowerShell scripts (`.ps1`) |
| Aliases | No native alias system | Supports command aliases |
| Pipelines | Passes text between commands | Passes objects between cmdlets |
| Programming capabilities | Limited | Built on .NET |
| Platform | Windows | Windows, Linux, and macOS (PowerShell 7+) |

### Command Prompt (CMD)

CMD is the traditional Windows command-line interpreter. It provides a straightforward way to execute commands, interact with files, and perform basic system administration.

For example, we can gather basic information about a Windows host using:

```cmd
whoami
hostname
ipconfig /all
systeminfo
```

These commands allow us to identify the current user, hostname, network configuration, and operating system information.

CMD also supports batch scripts, allowing us to automate sequences of commands.

### PowerShell

PowerShell is both a command-line environment and a scripting language built on .NET.

Its main advantage over CMD is that **PowerShell works with structured objects instead of relying exclusively on plain-text output.**

For example:

```powershell
Get-Process
```

This cmdlet returns objects representing running processes, including properties such as process names, identifiers, and memory usage.

We can filter these objects using pipelines:

```powershell
Get-Process | Where-Object {$_.CPU -gt 100}
```

This command retrieves processes whose accumulated CPU time exceeds 100 seconds.

Unlike traditional text-based pipelines, PowerShell pipelines pass structured objects between cmdlets, allowing us to access their properties directly.

---

## Executing Commands Across Environments

PowerShell can execute most traditional CMD commands directly.

For example, the following command works in both environments:

```cmd
ipconfig
```

However, CMD cannot execute PowerShell cmdlets directly. We must explicitly invoke PowerShell:

```cmd
powershell -Command "Get-Process"
```

We can also execute scripts and more complex commands through PowerShell's command-line interface.

---

## Relevance to Penetration Testing

Both environments are useful during Windows security assessments.

- **Reconnaissance:** Gather information about the operating system, users, processes, and network configuration.
- **Enumeration:** Identify services, permissions, installed applications, and other resources.
- **Automation:** Execute scripts to simplify repetitive tasks and collect information.
- **Post-exploitation:** Interact with compromised Windows hosts using native operating-system tools.

PowerShell also provides access to .NET libraries, making it useful for developing more advanced scripts and interacting with Windows APIs.

**Key takeaway:** CMD primarily processes text, while PowerShell processes structured objects. This distinction makes PowerShell considerably more flexible for automation, filtering, scripting, and advanced Windows administration.
