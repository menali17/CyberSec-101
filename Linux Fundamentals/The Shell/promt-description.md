# Prompt Description

The **Bash prompt** is the line displayed in the terminal when the shell is ready to receive a command.

By default, it can show information such as:

* Current username
* Hostname
* Current working directory
* User privilege level

A common prompt format is:

```text
<username>@<hostname><current working directory>$
```

Example:

```text
user@host[~]$
```

The cursor appears after the prompt, waiting for the user to enter a command.

---

# Home Directory

The tilde:

```text
~
```

represents the current user's **home directory**.

For example:

```text
user@host[~]$
```

indicates that the current working directory is the user's home directory.

---

# `$` vs `#`

The final character of the prompt can indicate the current privilege level.

## Regular User

A normal or unprivileged user usually sees:

```text
$
```

Example:

```text
user@host[~]$
```

---

## Root User

The **root user** usually sees:

```text
#
```

Example:

```text
root@htb[/htb]#
```

Quick reference:

```text
$ → Regular user

# → Root / privileged user
```

This is especially important during penetration testing because it provides a quick indication of the privileges available in the current shell.

---

# Minimal Shell Prompts

When obtaining a shell on a target system, the prompt may not display information such as:

* Username
* Hostname
* Current working directory

An unprivileged shell may simply appear as:

```text
$
```

A privileged root shell may appear as:

```text
#
```

This can occur when the shell environment is not fully configured or when the **PS1 variable** is not properly set.

---

# PS1 Variable

The **PS1 environment variable** controls how the primary Bash prompt is displayed.

It acts as a template that defines what information appears before each command.

PS1 can be customized to include:

* Username
* Hostname
* Current directory
* Date
* Time
* IP address
* Command status
* Colors
* Special characters

Example concept:

```text
Username + Hostname + Directory + $
```

The configuration can be modified through Bash configuration files such as:

```text
~/.bashrc
```

---

# Useful PS1 Special Characters

Bash provides special escape sequences that can be used when customizing the prompt.

| Character      | Information                                |
| -------------- | ------------------------------------------ |
| `\d`           | Date in format such as `Mon Feb 6`         |
| `\D{%Y-%m-%d}` | Date as `YYYY-MM-DD`                       |
| `\H`           | Full hostname                              |
| `\j`           | Number of jobs managed by the shell        |
| `\n`           | Newline                                    |
| `\r`           | Carriage return                            |
| `\s`           | Shell name                                 |
| `\t`           | Current time in 24-hour format             |
| `\T`           | Current time in 12-hour format             |
| `\@`           | Current time                               |
| `\u`           | Current username                           |
| `\w`           | Full path of the current working directory |

Some of the most useful ones to remember are:

```text
\u → Username

\H → Hostname

\w → Current working directory

\t → Current time
```

---

# Prompt Customization

The Bash prompt can be customized to display useful system information.

For example, during a penetration test, the prompt could contain:

```text
Username
Hostname
Current directory
Target IP
Date and time
```

This can help keep track of actions performed across different systems or targets.

A customized prompt can also help reduce mistakes when working with multiple machines simultaneously.

---

# Command Tracking

Linux provides several ways to keep track of commands executed during a session.

One important file is:

```text
~/.bash_history
```

This file stores commands previously executed by the user.

It can be useful for:

* Reviewing previous commands
* Documentation
* Troubleshooting
* Analysis

The `script` utility can also be used to record terminal sessions.

---

# `.bashrc`

The:

```text
~/.bashrc
```

file contains configuration settings for Bash.

It can be used to configure:

* Prompt appearance
* Environment variables
* Aliases
* Shell behavior
* Other Bash settings

The PS1 variable can therefore be customized inside `.bashrc`.

---

# Quick Reference

**Bash Prompt**
→ Indicates that the shell is ready for a command.

**`~`**
→ Current user's home directory.

**`$`**
→ Regular / unprivileged user.

**`#`**
→ Root / privileged user.

**PS1**
→ Environment variable controlling the Bash prompt.

**`\u`**
→ Username.

**`\H`**
→ Full hostname.

**`\w`**
→ Current working directory.

**`\t`**
→ Current time.

**`.bashrc`**
→ Bash configuration file.

**`.bash_history`**
→ Stores previously executed Bash commands.

**`script`**
→ Can record terminal sessions.

---

## Key Takeaway

**The Bash prompt provides information about the current shell environment, such as the user, hostname, directory, and privilege level. The `$` symbol usually represents a regular user, while `#` represents root. The appearance of the prompt is controlled by the PS1 variable and can be customized through files such as `.bashrc`.**
