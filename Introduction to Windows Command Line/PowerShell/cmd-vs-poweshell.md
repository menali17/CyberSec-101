
# CMD vs. PowerShell

---

## Overview

PowerShell is Microsoft's advanced command-line shell and scripting language, designed for system administration, automation, and configuration management.

Unlike CMD, which primarily processes text, **PowerShell works with structured objects**, allowing us to manipulate command output, access object properties, and build more sophisticated scripts.

PowerShell is built on .NET and is available on Windows, Linux, and macOS.

## CMD vs. PowerShell

| Feature | CMD | PowerShell |
|---|---|---|
| Language | Batch commands and scripts | Cmdlets, scripts, and native executables |
| Output | Plain text | Structured objects |
| Pipelines | Passes text between commands | Passes objects between cmdlets |
| Automation | Batch scripting | Advanced scripting and automation |
| Parallel execution | Limited | Supports parallel execution |
| Extensibility | Limited | Supports modules and .NET libraries |
| Platform | Windows | Windows, Linux, and macOS |

### Why PowerShell?

PowerShell provides extensive capabilities for managing Windows environments, including:

- Creating and managing Active Directory users and groups.
- Managing file permissions and network shares.
- Configuring Windows services and servers.
- Interacting with Azure and Microsoft 365.
- Gathering information about workstations and servers.
- Automating repetitive administrative tasks.

From a penetration testing perspective, PowerShell is particularly useful for enumeration, automation, and interacting with Windows APIs.

Its module system also allows us to extend its functionality by importing additional tools.

**Security consideration:** PowerShell supports extensive logging and command history. Its activity may be recorded and monitored by security solutions.

---

## Accessing PowerShell

We can access PowerShell through several methods:

1. **Windows Search:** Search for PowerShell and launch the application.
2. **Windows Terminal:** Open a PowerShell tab within Windows Terminal.
3. **PowerShell ISE:** Use the Integrated Scripting Environment to develop, test, and debug scripts.
4. **CMD:** Launch PowerShell directly from an existing Command Prompt session.

To launch PowerShell from CMD:

```cmd
powershell.exe
```

We can also execute an individual PowerShell command without switching to an interactive PowerShell session:

```cmd
powershell.exe -Command "Get-Process"
```

### Understanding the PowerShell Prompt

A typical PowerShell prompt looks like this:

```powershell
PS C:\Users\htb-student>
```

The `PS` prefix identifies the PowerShell environment, while the remaining path indicates our current working directory.

Most traditional Windows command-line executables, such as `ipconfig`, can also be executed directly from PowerShell.

---

## Getting Help

### Get-Help

PowerShell provides the `Get-Help` cmdlet, which allows us to retrieve documentation about commands and their available parameters.

For example:

```powershell
Get-Help Test-WSMan
```

Depending on the installed documentation, the output can include:

- Command description.
- Available parameters and syntax.
- Usage examples.
- Related commands.
- Links to online documentation.

We can request more specific information using:

| Command | Description |
|---|---|
| `Get-Help <cmdlet>` | Displays help for a cmdlet. |
| `Get-Help <cmdlet> -Examples` | Displays usage examples. |
| `Get-Help <cmdlet> -Detailed` | Displays detailed documentation. |
| `Get-Help <cmdlet> -Full` | Displays all available documentation. |
| `Get-Help <cmdlet> -Online` | Opens the official online documentation. |

For example:

```powershell
Get-Help Get-Process -Examples
```

### Update-Help

PowerShell does not always include complete documentation for every installed module.

We can download and install available help files using:

```powershell
Update-Help
```

Some modules may require administrative privileges to update their documentation.

Once the help files are installed, `Get-Help` can provide more comprehensive information even when we do not have Internet access.

---

## Getting Around in PowerShell

PowerShell provides cmdlets for navigating the filesystem, listing directories, and inspecting files.

### Get-Location

The `Get-Location` cmdlet displays our current working directory.

```powershell
Get-Location
```

Example output:

```text
Path
----
C:\Users\htb-student
```

It is equivalent to using `pwd` in Linux or `cd` without arguments in CMD.

### Get-ChildItem

The `Get-ChildItem` cmdlet lists the contents of our current directory or a specified path.

```powershell
Get-ChildItem
```

We can also provide an absolute path:

```powershell
Get-ChildItem C:\Users\htb-student\Documents
```

Unlike the traditional CMD `dir` command, `Get-ChildItem` returns objects containing properties such as file names, sizes, attributes, and modification timestamps.

### Set-Location

We can change our current working directory using `Set-Location`.

```powershell
Set-Location .\Documents
```

Alternatively, we can provide an absolute path:

```powershell
Set-Location C:\Users\htb-student\Documents
```

### Get-Content

The `Get-Content` cmdlet allows us to read the contents of a file.

```powershell
Get-Content Readme.md
```

We can also specify the complete path:

```powershell
Get-Content C:\Users\htb-student\Documents\Readme.md
```

---

## Finding Commands

### Get-Command

The `Get-Command` cmdlet allows us to discover available cmdlets, functions, aliases, and executables.

```powershell
Get-Command
```

PowerShell cmdlets generally follow the **Verb-Noun naming convention**.

For example:

```powershell
Get-Process
Set-Location
Get-Content
```

The verb describes the action, while the noun identifies the resource or object being manipulated.

We can search for commands using either component.

**Searching by verb:**

```powershell
Get-Command -Verb Get
```

This displays commands that use the `Get` verb.

**Searching by noun:**

```powershell
Get-Command -Noun Windows*
```

This displays commands whose nouns begin with `Windows`.

We can also use wildcards to search for command names:

```powershell
Get-Command Get-*
```

Combining `Get-Command` with `Get-Help` allows us to discover unfamiliar commands and learn how to use them.

---

## PowerShell Command History

PowerShell supports two mechanisms for tracking previously executed commands.

### Get-History

The `Get-History` cmdlet displays commands executed during our current PowerShell session.

```powershell
Get-History
```

Example output:

```text
Id CommandLine
-- -----------
1  Get-Command
2  Get-Location
3  Get-ChildItem
4  ipconfig /all
5  Get-Help Get-Process
```

Each command receives a history identifier.

We can execute a previous command again using `Invoke-History`:

```powershell
Invoke-History 4
```

This re-executes the fourth command in our session history.

By default, this history is lost when the PowerShell session terminates.

### PSReadLine History

The `PSReadLine` module provides additional command-line editing and history capabilities.

Unlike `Get-History`, PSReadLine can save commands across multiple PowerShell sessions.

On Windows, its history is commonly stored in:

```text
%APPDATA%\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
```

We can retrieve the current history file path using:

```powershell
(Get-PSReadLineOption).HistorySavePath
```

To inspect the saved history:

```powershell
Get-Content (Get-PSReadLineOption).HistorySavePath
```

PSReadLine also supports filtering certain commands containing potentially sensitive information, although this behavior depends on the installed version and configuration.

**Security consideration:** Command history may contain usernames, server addresses, administrative commands, and accidentally exposed credentials. It can therefore be a valuable source of information during authorized host enumeration.

---

## PowerShell Tips and Tricks

### Clearing the Screen

We can clear the current terminal display using:

```powershell
Clear-Host
```

The aliases `clear` and `cls` provide the same functionality.

Clearing the screen does not automatically remove our variables or command history.

### Useful Keyboard Shortcuts

| Shortcut | Description |
|---|---|
| `Ctrl + R` | Searches previously executed commands. |
| `Ctrl + L` | Clears the terminal display in supported PSReadLine configurations. |
| `Esc` | Clears the current command line. |
| `↑` | Navigates backward through command history. |
| `↓` | Navigates forward through command history. |
| `F7` | Displays interactive command history in supported environments. |
| `Tab` | Cycles forward through completion suggestions. |
| `Shift + Tab` | Cycles backward through completion suggestions. |

Available shortcuts may vary depending on the terminal and PSReadLine configuration.

### Tab Completion

PowerShell supports command, parameter, and path autocompletion.

For example, we can begin typing:

```powershell
Get-Ch
```

Pressing `Tab` cycles through matching commands.

This helps us discover commands and reduces typing errors.

---

## Aliases

Aliases are alternative names for existing PowerShell commands.

They allow us to execute frequently used cmdlets without typing their complete names.

### Get-Alias

We can list available aliases using:

```powershell
Get-Alias
```

To inspect a specific alias:

```powershell
Get-Alias ls
```

This reveals that `ls` is an alias for `Get-ChildItem`.

### Common Aliases

| Alias | Cmdlet | Purpose |
|---|---|---|
| `pwd` | `Get-Location` | Displays our current directory. |
| `ls`, `dir`, `gci` | `Get-ChildItem` | Lists directory contents. |
| `cd`, `sl` | `Set-Location` | Changes our current directory. |
| `cat`, `type`, `gc` | `Get-Content` | Displays file contents. |
| `clear`, `cls` | `Clear-Host` | Clears the terminal display. |
| `gal` | `Get-Alias` | Lists command aliases. |
| `fl` | `Format-List` | Formats output as a list. |
| `ft` | `Format-Table` | Formats output as a table. |
| `man` | `help` | Displays command documentation. |

Many PowerShell aliases resemble familiar Linux commands, making it easier to transition between Bash and PowerShell.

### Creating Custom Aliases

We can create our own aliases using `Set-Alias`.

For example:

```powershell
Set-Alias -Name gh -Value Get-Help
```

We can now execute:

```powershell
gh Get-Process
```

PowerShell interprets `gh` as `Get-Help`.

By default, custom aliases created with `Set-Alias` are available only within the current session unless we save them in our PowerShell profile.

---

## Key Takeaways

- PowerShell is both a command-line shell and an advanced scripting language built on .NET.
- Unlike CMD, PowerShell passes structured objects between cmdlets.
- `Get-Help` provides command documentation, while `Update-Help` downloads available help files.
- `Get-Location`, `Get-ChildItem`, `Set-Location`, and `Get-Content` provide basic filesystem navigation.
- `Get-Command` helps us discover commands using the Verb-Noun naming convention.
- `Get-History` displays commands from our current session, while PSReadLine can preserve history across sessions.
- Aliases allow us to execute cmdlets using shorter or more familiar names.
- PowerShell's scripting, automation, and object-processing capabilities make it particularly useful for Windows administration and security assessments.
