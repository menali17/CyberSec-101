
# Finding & Filtering Content

---

## Overview

PowerShell allows us to search, filter, sort, and manipulate data through its object-based architecture.

Unlike CMD, which primarily processes text, PowerShell works with **objects containing properties and methods**. This makes it possible to retrieve specific information without manually parsing large amounts of text.

In this section, we will learn how to:

- Understand PowerShell objects, classes, properties, and methods.
- Inspect objects using `Get-Member`.
- Select, sort, group, and filter object properties.
- Chain commands using the PowerShell pipeline.
- Search recursively for files and their contents.
- Use comparison operators and regular expressions.

---

## 1. Understanding PowerShell Objects

An object is an individual instance of a class. It contains data, represented by **properties**, and may provide operations, represented by **methods**.

| Concept | Description |
|---|---|
| Class | Defines the structure and behavior of a particular type of object. |
| Object | An individual instance of a class. |
| Property | A value or characteristic associated with an object. |
| Method | An operation that an object can perform. |

For example, a Windows user account is an object with properties such as `Name`, `Enabled`, `SID`, and `PasswordLastSet`.

### Inspecting an Object with Get-Member

We can use `Get-Member` to discover an object's properties and methods.

```powershell
Get-LocalUser Administrator | Get-Member
```

The command retrieves the local Administrator account and passes its object to `Get-Member`.

Some properties we might discover include:

| Property | Description |
|---|---|
| `Name` | Account name. |
| `Enabled` | Indicates whether the account is enabled. |
| `SID` | Security Identifier. |
| `LastLogon` | Last recorded logon time, when available. |
| `PasswordLastSet` | Date and time of the last password change. |
| `PasswordRequired` | Indicates whether the account requires a password. |

We can also inspect available methods, such as `Clone()` and `ToString()`.

---

## 2. Selecting Object Properties

When working with objects, we may not need every property returned by a command.

The `Select-Object` cmdlet allows us to choose which properties appear in our results.

### Displaying All Properties

```powershell
Get-LocalUser Administrator |
    Select-Object -Property *
```

The wildcard `*` selects all available properties.

### Selecting Specific Properties

Suppose we want to inspect the password modification dates of local users.

```powershell
Get-LocalUser |
    Select-Object -Property Name,PasswordLastSet
```

Example output:

    Name                PasswordLastSet
    ----                ---------------
    Administrator
    DefaultAccount
    Guest
    MTanaka             1/27/2021 2:39:55 PM
    WDAGUtilityAccount  1/18/2021 7:40:22 AM

Instead of displaying every property, we retrieve only the information relevant to our investigation.

### Sorting and Grouping

We can organize our results using `Sort-Object` and `Group-Object`.

```powershell
Get-LocalUser |
    Sort-Object -Property Name |
    Group-Object -Property Enabled
```

This pipeline retrieves local users, sorts them alphabetically, and groups them according to whether their accounts are enabled.

Example:

    Count Name  Group
    ----- ----  -----
    4     False {Administrator, DefaultAccount, Guest, WDAGUtilityAccount}
    1     True  {MTanaka}

This allows us to quickly distinguish active and disabled accounts.

---

## 3. Filtering Objects with Where-Object

Some PowerShell commands return substantial amounts of information.

For example:

```powershell
Get-Service | Select-Object -Property *
```

This displays all available properties for every service returned by `Get-Service`.

Instead of manually reviewing everything, we can use `Where-Object` to retrieve only the objects that satisfy a particular condition.

### Searching for Specific Services

Suppose we want to identify services whose display names contain the word `Defender`.

```powershell
Get-Service |
    Where-Object DisplayName -like '*Defender*'
```

Example output:

    Status   Name       DisplayName
    ------   ----       -----------
    Running  mpssvc     Windows Defender Firewall
    Stopped  Sense      Windows Defender Advanced Threat Protection
    Running  WdNisSvc   Microsoft Defender Antivirus Network Inspection
    Running  WinDefend  Microsoft Defender Antivirus Service

The `-like` operator performs wildcard matching.

The expression `'*Defender*'` matches any value containing the word `Defender`, regardless of the text appearing before or after it.

### Combining Filtering and Property Selection

We can retrieve additional information about only the matching services:

```powershell
Get-Service |
    Where-Object DisplayName -like '*Defender*' |
    Select-Object -Property *
```

This first filters the services and then displays the properties of each matching object.

---

## 4. PowerShell Comparison Operators

Comparison operators allow us to evaluate values and filter objects according to specific conditions.

| Operator | Description | Example |
|---|---|---|
| `-like` | Wildcard matching. | `Name -like '*Defender*'` |
| `-eq` | Equality comparison. | `Status -eq 'Running'` |
| `-contains` | Checks whether a collection contains a specified value. | `Groups -contains 'Administrators'` |
| `-match` | Regular expression matching. | `Name -match '^Win'` |
| `-ne` | Not equal. | `Status -ne 'Stopped'` |
| `-gt` | Greater than. | `CPU -gt 100` |
| `-lt` | Less than. | `CPU -lt 10` |

### Wildcard Matching

We can retrieve services whose names begin with `Win`:

```powershell
Get-Service |
    Where-Object Name -like 'Win*'
```

The wildcard `*` represents zero or more characters.

### Exact Matching

We can filter services based on their current state:

```powershell
Get-Service |
    Where-Object Status -eq 'Running'
```

This retrieves services whose `Status` property equals `Running`.

**Technical note:** PowerShell's standard string comparison operators are case-insensitive by default. We can use case-sensitive variants such as `-ceq` and `-clike` when required.

### Regular Expressions

The `-match` operator supports regular expressions.

```powershell
Get-Service |
    Where-Object Name -match '^Win'
```

The expression `^Win` matches names that begin with `Win`.

---

## 5. Understanding the PowerShell Pipeline

The pipeline (`|`) allows us to pass the output of one command directly into another.

Because PowerShell works with objects, each cmdlet can receive structured information from the previous command.

A pipeline generally follows this structure:

    Command-1 | Command-2 | Command-3

For example:

```powershell
Get-Service |
    Where-Object Status -eq 'Running' |
    Sort-Object DisplayName |
    Select-Object Name,DisplayName,Status
```

The commands perform four operations:

1. `Get-Service` retrieves Windows services.
2. `Where-Object` selects only running services.
3. `Sort-Object` sorts them by display name.
4. `Select-Object` displays only the specified properties.

Each stage processes the output received from the previous stage.

### Counting Objects

The `Measure-Object` cmdlet allows us to count objects passed through a pipeline.

For example, to count the distinct process names currently running on our system:

```powershell
Get-Process |
    Sort-Object ProcessName -Unique |
    Measure-Object
```

Example output:

    Count : 113

The result depends on the processes running on our host.

---

## 6. Pipeline Chain Operators

PowerShell 7 introduced pipeline chain operators that allow us to execute commands conditionally.

**These operators are not supported in Windows PowerShell 5.1.**

| Operator | Behavior |
|---|---|
| `&&` | Executes the following command if the previous command succeeds. |
| `||` | Executes the following command if the previous command fails. |

### Executing a Command After Success

```powershell
Get-Content '.\test.txt' && ping 8.8.8.8
```

If PowerShell successfully reads `test.txt`, it proceeds to execute `ping`.

### Executing a Command After Failure

```powershell
Get-Content '.\test.txt' || ping 8.8.8.8
```

In this case, `ping` is executed only if the first command fails.

These operators are useful for writing command sequences that depend on the success or failure of previous operations.

---

## 7. Finding Files Recursively

PowerShell allows us to search for files throughout a directory and its subdirectories.

The `Get-ChildItem` cmdlet provides the `-Recurse` parameter for recursive enumeration.

### Enumerating All Files

```powershell
Get-ChildItem -Path C:\Users\MTanaka\ -File -Recurse
```

The parameters perform the following operations:

| Parameter | Description |
|---|---|
| `-Path` | Specifies the directory to search. |
| `-File` | Returns only files. |
| `-Recurse` | Searches the directory and its subdirectories. |

This may return a considerable number of results, so we can apply filters to narrow our search.

### Filtering by File Extension

We can retrieve only `.txt` files:

```powershell
Get-ChildItem -Path C:\Users\MTanaka\ -File -Recurse |
    Where-Object { $_.Name -like '*.txt' }
```

Inside the script block, `$_` represents the current object being processed.

The expression `$_.Name` accesses the current file's name.

### Searching for Multiple File Types

We can combine conditions using `-or`:

```powershell
Get-ChildItem -Path C:\Users\MTanaka\ -File -Recurse |
    Where-Object {
        $_.Name -like '*.txt' -or
        $_.Name -like '*.py' -or
        $_.Name -like '*.ps1' -or
        $_.Name -like '*.md' -or
        $_.Name -like '*.csv'
    }
```

This retrieves text, Python, PowerShell, Markdown, and CSV files.

### Handling Access Errors

We may encounter directories that our account cannot access.

The HTB material introduces:

```powershell
-ErrorAction SilentlyContinue
```

For example:

```powershell
Get-ChildItem -Path C:\Users\MTanaka\ `
    -File -Recurse -ErrorAction SilentlyContinue
```

This suppresses non-terminating error messages encountered during enumeration.

It does not provide additional permissions or bypass filesystem access controls.

---

## 8. Searching Within Files with Select-String

Finding a file is not the same as searching its contents.

PowerShell provides `Select-String`, which performs text searches using regular expressions.

Its default alias is `sls`.

The cmdlet is comparable to `grep` on Linux and `findstr` in CMD.

### Searching for Keywords

The HTB module demonstrates combining recursive file enumeration with keyword searches:

```powershell
Get-ChildItem -Path C:\Users\MTanaka\ -Filter '*.txt' -Recurse -File |
    Select-String 'Password','credential','key'
```

This searches text files for the specified patterns.

When a match is found, the result normally includes the filename, line number, and matching text.

Example:

    notes.txt:3:- Password: [example value]
    wmic.txt:67:wmic netlogin get name,badpasswordcount

Not every match necessarily represents sensitive information. Searching for a common word such as `key` may produce unrelated results.

### Case-Sensitive Searching

`Select-String` is case-insensitive by default.

We can enable case-sensitive matching with `-CaseSensitive`:

```powershell
Select-String -Path '.\notes.txt' `
    -Pattern 'Password' -CaseSensitive
```

### Combining File and Content Filters

We can build a pipeline that searches for particular file types and then examines their contents:

```powershell
Get-ChildItem -Path C:\Users\MTanaka\ `
    -File -Recurse -ErrorAction SilentlyContinue |
    Where-Object {
        $_.Name -like '*.txt' -or
        $_.Name -like '*.py' -or
        $_.Name -like '*.ps1' -or
        $_.Name -like '*.md' -or
        $_.Name -like '*.csv'
    } |
    Select-String 'Password','credential','key','UserName'
```

This combines three operations:

1. Recursively enumerate accessible files.
2. Filter the results by file extension.
3. Search the remaining files for specified keywords.

For authorized penetration testing, this technique helps us identify potentially interesting files without installing additional enumeration tools on the target system.

---

## 9. Interesting Locations for Enumeration

The HTB module identifies several Windows locations and sources that may contain useful information during an authorized security assessment.

| Location | Potentially useful information |
|---|---|
| `C:\Users\<user>\` | User files and configuration data. |
| `AppData` | Application settings and temporary files. |
| Hidden directories | Configuration files and application-specific data. |
| PSReadLine history | Previously entered PowerShell commands. |
| Scheduled tasks | Configured automated actions and executable paths. |
| Clipboard | Data currently copied by the user, when accessible. |

### Hidden Files

We can enumerate hidden files and directories using:

```powershell
Get-ChildItem -Hidden
```

### PowerShell History

We can discover the current PSReadLine history file:

```powershell
(Get-PSReadLineOption).HistorySavePath
```

And inspect its contents:

```powershell
Get-Content (Get-PSReadLineOption).HistorySavePath
```

### Clipboard

PowerShell also provides a cmdlet for reading the current clipboard:

```powershell
Get-Clipboard
```

Clipboard contents and command history may contain sensitive data. Their inspection should remain within the permissions and scope of our assessment.

---

## Command Summary

| Cmdlet | Purpose |
|---|---|
| `Get-Member` | Displays object properties and methods. |
| `Select-Object` | Selects specific object properties. |
| `Where-Object` | Filters objects according to conditions. |
| `Sort-Object` | Sorts objects by selected properties. |
| `Group-Object` | Groups objects by property values. |
| `Measure-Object` | Counts or measures objects. |
| `Get-ChildItem -Recurse` | Enumerates files and directories recursively. |
| `Select-String` | Searches text and file contents using patterns. |
| `Get-Clipboard` | Reads the current clipboard. |

---

## Key Takeaways

- PowerShell commands return structured objects containing properties and methods.
- `Get-Member` allows us to inspect an object's structure.
- `Select-Object` reduces output to the properties we need.
- `Where-Object` filters results using comparisons and conditions.
- `Sort-Object`, `Group-Object`, and `Measure-Object` organize and summarize data.
- The PowerShell pipeline passes objects between commands, allowing us to combine multiple operations.
- PowerShell 7 supports conditional pipeline execution through `&&` and `||`.
- `Get-ChildItem -Recurse` enables recursive filesystem searches.
- `Select-String` searches file contents and supports regular expressions.
- Combining filesystem enumeration, filtering, and content searches is a useful technique for Windows administration and authorized security assessments.
