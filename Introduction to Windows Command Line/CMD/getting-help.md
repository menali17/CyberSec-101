
# Getting Help

---

## How to Get Help

Command Prompt provides a built-in `help` utility that allows us to discover available commands and understand their syntax without relying on external documentation.

### Listing Available Commands

Running `help` without additional parameters displays a list of supported Windows commands and a brief description of their functionality.

```cmd
help
```

Example output:

```text
ASSOC     Displays or modifies file extension associations.
ATTRIB    Displays or changes file attributes.
BCDEDIT   Sets properties in the boot database.
CD        Displays or changes the current directory.
CHKDSK    Checks a disk and displays a status report.
```

### Getting Help for a Specific Command

We can obtain more detailed information about a particular command using:

```cmd
help <command>
```

For example:

```cmd
help time
```

This displays the command's description, syntax, and available parameters.

```text
Displays or sets the system time.

TIME [/T | time]
```

Not every command is supported directly by the `help` utility. Some commands provide their own documentation through the `/?` parameter.

For example:

```cmd
help ipconfig
```

Returns:

```text
This command is not supported by the help utility.
Try "ipconfig /?".
```

We can then execute:

```cmd
ipconfig /?
```

This displays the available options and syntax for `ipconfig`.

---

## Why Do We Need the Help Utility?

The `help` utility acts as an **offline command reference**, similar to the `man` pages in Linux.

During penetration testing, we may encounter environments where Internet access is restricted, monitored, or completely unavailable.

For example, during an internal assessment, we might obtain a CMD session on a Windows host whose outbound network traffic is blocked by a firewall.

If we cannot remember the syntax of a particular command, we can consult its built-in documentation instead of relying on external resources.

This makes the `help` utility particularly useful during reconnaissance and system enumeration in restricted environments.

---

## Additional Resources

When Internet access is available, we can consult external documentation for more detailed information about Windows commands.

- [Microsoft Windows Commands Documentation](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/windows-commands): Official documentation containing command descriptions, syntax, and usage examples.
- [SS64 — Windows CMD](https://ss64.com/nt/): A quick reference for Windows CMD commands, including their parameters and examples.

---

## Basic Tips & Tricks

### Clearing the Screen

When our terminal becomes cluttered with command output, we can clear the screen using:

```cmd
cls
```

This removes the previous output from the terminal display without deleting our command history.

### Command History

CMD maintains a history of previously executed commands during the current active session.

We can retrieve this history using:

```cmd
doskey /history
```

Example output:

```text
systeminfo
ipconfig /all
cls
ipconfig /all
help
doskey /history
ping 8.8.8.8
```

This allows us to identify previously executed commands without having to remember or retype them.

#### Useful Keyboard Shortcuts

| Key / Command | Description |
|---|---|
| `doskey /history` | Displays the current session's command history. |
| `Page Up` | Retrieves the first command in our history. |
| `Page Down` | Retrieves the last command in our history. |
| `↑` | Navigates backward through previous commands. |
| `↓` | Navigates forward through previous commands. |
| `→` | Retrieves the previous command one character at a time. |
| `F3` | Repeats the previous command. |
| `F5` | Cycles through previously executed commands. |
| `F7` | Displays an interactive command history list. |
| `F9` | Retrieves a command by its position in the history. |

These shortcuts can vary depending on the terminal application hosting CMD.

**Important:** Unlike Bash, CMD does not maintain persistent command history by default. Once we close the session, its history is lost.

We can save our current session's history to a file using:

```cmd
doskey /history > commands.txt
```

This redirects the command history into `commands.txt`, allowing us to review it later.

### Interrupting a Running Process

We can interrupt a running command or process using:

```text
Ctrl + C
```

For example, if we execute:

```cmd
ping 8.8.8.8 -t
```

The `-t` parameter instructs Windows to continuously send ICMP Echo Requests until interrupted.

We can press `Ctrl + C` to stop the command and display the accumulated ping statistics.

**Important:** Interrupting a process may terminate its execution before it finishes its operations. We should exercise caution when stopping commands that modify files or perform administrative tasks.

---

## Key Takeaways

- `help` displays available Windows commands and their descriptions.
- `help <command>` provides detailed information about supported commands.
- `/?` provides command-specific documentation when the `help` utility is unavailable.
- The built-in help system is especially useful during penetration testing in environments without Internet access.
- `cls` clears the terminal display without deleting command history.
- `doskey /history` displays previously executed commands from our current session.
- `Ctrl + C` interrupts a running command or process.
