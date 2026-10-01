
# Working with Files and Directories — PowerShell

---

## Overview

PowerShell provides cmdlets for creating, modifying, copying, renaming, and deleting files and directories. It also allows us to automate operations on multiple files using pipelines.

In this section, we will learn how to:

- Create files and directories.
- Read and modify file contents.
- Rename individual files and multiple files simultaneously.
- Understand basic Windows file and directory permissions.
- Recognize how filesystem permissions affect security.

---

## 1. File and Directory Management

The following cmdlets allow us to interact with files, directories, and other PowerShell objects.

| Cmdlet | Alias | Description |
|---|---|---|
| `Get-Item` | `gi` | Retrieves a specific item, such as a file or directory. |
| `Get-ChildItem` | `ls`, `dir`, `gci` | Lists the contents of a directory. |
| `New-Item` | `ni`, `mkdir` | Creates files, directories, and other objects. |
| `Set-Item` | `si` | Modifies an item's value where supported. |
| `Copy-Item` | `copy`, `cp` | Copies an item. |
| `Rename-Item` | `ren`, `rni` | Renames an item. |
| `Remove-Item` | `rm`, `del`, `rmdir` | Deletes an item. |
| `Get-Content` | `cat`, `type` | Reads file contents. |
| `Add-Content` | `ac` | Appends content to a file. |
| `Set-Content` | `sc` | Replaces a file's existing contents. |
| `Clear-Content` | `clc` | Removes a file's contents without deleting the file. |
| `Compare-Object` | `diff`, `compare` | Compares objects or file contents. |

**Important:** In PowerShell, `sc` is an alias for `Set-Content`. To use the Windows Service Controller discussed in the CMD section, we can explicitly execute `sc.exe`.

---

## 2. Creating Directories

The HTB exercise uses a scenario in which we need to create a directory structure for security documentation.

The required structure is:

    SOPs/
    ├── Physical Sec/
    ├── Cyber Sec/
    └── Training/

### Checking Our Current Directory

Before creating anything, we can verify our current location:

```powershell
Get-Location
```

We can then navigate to the Documents directory:

```powershell
Set-Location C:\Users\MTanaka\Documents
```

Alternatively, we can use the familiar `cd` alias.

### Creating the Main Directory

We can create a directory using `New-Item`:

```powershell
New-Item -Name "SOPs" -ItemType Directory
```

The `-Name` parameter specifies the directory name, while `-ItemType Directory` tells PowerShell to create a directory rather than a file.

### Creating Subdirectories

After entering the `SOPs` directory, we can create the required subdirectories:

```powershell
cd SOPs

mkdir "Physical Sec"
mkdir "Cyber Sec"
mkdir "Training"
```

Quotation marks are necessary around names containing spaces.

We can verify that the directories were created using:

```powershell
Get-ChildItem
```

---

## 3. Creating Files

The next step in the HTB exercise is to create the following Markdown files:

    SOPs/
    ├── Readme.md
    ├── Physical Sec/
    │   └── Physical-Sec-draft.md
    ├── Cyber Sec/
    │   └── Cyber-Sec-draft.md
    └── Training/
        └── Employee-Training-draft.md

### New-Item

We can create an empty file using:

```powershell
New-Item -Name "Readme.md" -ItemType File
```

The `-ItemType File` parameter specifies that we want to create a file.

We can also specify a relative or absolute path:

```powershell
New-Item -Path ".\Physical Sec\Physical-Sec-draft.md" -ItemType File
```

Following the same approach, we can create the remaining files:

```powershell
New-Item -Path ".\Cyber Sec\Cyber-Sec-draft.md" -ItemType File

New-Item -Path ".\Training\Employee-Training-draft.md" -ItemType File
```

### Verifying the Directory Structure

We can use the Windows `tree` executable directly from PowerShell:

```powershell
tree /F
```

The `/F` parameter includes files in the directory tree.

---

## 4. Working with File Contents

PowerShell provides several cmdlets for reading, adding, replacing, and clearing file contents.

| Cmdlet | Operation |
|---|---|
| `Get-Content` | Reads existing content. |
| `Add-Content` | Appends new content. |
| `Set-Content` | Replaces existing content. |
| `Clear-Content` | Removes content while preserving the file. |

### Add-Content

The HTB scenario requires us to insert the following information into each Markdown file:

    Title: Insert Document Title Here
    Date: x/x/202x
    Author: MTanaka
    Version: 0.1 (Draft)

We can use `Add-Content` to accomplish this:

```powershell
Add-Content .\Readme.md "Title: Insert Document Title Here
Date: x/x/202x
Author: MTanaka
Version: 0.1 (Draft)"
```

If the file already contains text, `Add-Content` appends the new content rather than replacing what is already there.

### Get-Content

We can verify the file's contents using:

```powershell
Get-Content .\Readme.md
```

Alternatively, we can use the `cat` alias:

```powershell
cat .\Readme.md
```

### Set-Content vs. Add-Content

The difference between these commands is important.

Suppose our file currently contains:

    Author: MTanaka

If we execute:

```powershell
Add-Content .\Readme.md "Version: 0.1"
```

The file will contain:

    Author: MTanaka
    Version: 0.1

However, if we execute:

```powershell
Set-Content .\Readme.md "Version: 0.1"
```

The original content will be replaced, leaving only:

    Version: 0.1

### Clear-Content

We can remove all content without deleting the file:

```powershell
Clear-Content .\Readme.md
```

The file remains in the directory, but its contents are cleared.

---

## 5. Renaming Files

The `Rename-Item` cmdlet allows us to change the name of an existing file or directory.

### Renaming a Single File

In the HTB scenario, we need to rename:

`Cyber-Sec-draft.md`

to:

`Infosec-SOP-draft.md`

We can execute:

```powershell
Rename-Item .\Cyber-Sec-draft.md -NewName Infosec-SOP-draft.md
```

The `-NewName` parameter specifies the new name.

We can verify the change using:

```powershell
Get-ChildItem
```

### Renaming Multiple Files

PowerShell's pipeline allows us to perform the same operation on multiple files.

Suppose we have five files:

    file-1.txt
    file-2.txt
    file-3.txt
    file-4.txt
    file-5.txt

We want to change their extensions from `.txt` to `.md`.

The HTB module demonstrates this using `Get-ChildItem`, a pipeline, and `Rename-Item`.

A version with a more precise replacement pattern is:

```powershell
Get-ChildItem -Path *.txt |
    Rename-Item -NewName {
        $_.Name -replace '\.txt$', '.md'
    }
```

Let's examine the individual components.

**Get-ChildItem -Path *.txt**

Retrieves files matching the `.txt` extension from the current directory.

**Pipeline (`|`)**

Passes each retrieved object to `Rename-Item`.

**`$_`**

Represents the current object being processed in the script block.

For example, when processing `file-1.txt`, `$_.Name` contains `file-1.txt`.

**`-replace`**

Performs a regular expression replacement.

The expression `'\.txt$'` matches the literal `.txt` extension at the end of the filename. The replacement changes it to `.md`.

After execution, the files will be named:

    file-1.md
    file-2.md
    file-3.md
    file-4.md
    file-5.md

This example demonstrates how PowerShell can automate repetitive filesystem operations.

---

## 6. File and Directory Permissions

Windows uses filesystem permissions to determine which users and groups can access particular files and directories, and which operations they are authorized to perform.

These permissions help organizations restrict sensitive information and prevent unauthorized modification of important files.

### Common NTFS Permissions

| Permission | Description |
|---|---|
| `Full Control` | Grants complete access, including changing permissions and taking ownership. |
| `Modify` | Allows reading, writing, modifying, and deleting files and directories. |
| `List Folder Contents` | Allows listing files and subdirectories within a folder. |
| `Read and Execute` | Allows reading files and executing permitted programs. |
| `Write` | Allows creating files and directories and writing content. |
| `Read` | Allows reading file contents and viewing directory information. |
| `Traverse Folder` | Allows traversing a directory to access permitted objects deeper in its hierarchy. |

### Full Control vs. Modify

Both permissions allow users to perform many filesystem operations.

However, `Full Control` additionally grants the ability to change permissions and take ownership of an object.

This distinction is important during privilege escalation assessments because permission changes can affect which accounts are authorized to access sensitive resources.

### Read vs. Write

`Read` allows us to access existing content without modifying it.

`Write` allows us to create files or write content, according to the permissions granted.

For example, an employee might receive read-only access to a directory containing company procedures while administrators retain permission to modify the documents.

---

## 7. Permission Inheritance

NTFS supports **permission inheritance**, allowing files and subdirectories to inherit permissions from their parent directories.

Consider the following structure:

    SOPs/
    ├── Readme.md
    ├── Physical Sec/
    ├── Cyber Sec/
    └── Training/

If we configure inheritable permissions on the `SOPs` directory, its files and subdirectories can inherit those permissions.

This avoids having to configure the same access rules individually for every object.

Inheritance can also be disabled when a particular file or directory requires different permissions.

### Pentesting Relevance

Understanding filesystem permissions helps us identify security misconfigurations, including:

- Sensitive files accessible to unauthorized users.
- Directories that allow unnecessary modification.
- Executable files writable by accounts that should not be able to change them.
- Unexpected permissions inherited from parent directories.

Permissions become especially important when investigating potential privilege escalation opportunities on Windows hosts.

---

## Command Summary

| Command | Purpose |
|---|---|
| `Get-Item` | Retrieves a specific item. |
| `Get-ChildItem` | Lists files and directories. |
| `New-Item` | Creates files or directories. |
| `Copy-Item` | Copies an item. |
| `Rename-Item` | Renames an item. |
| `Remove-Item` | Deletes an item. |
| `Get-Content` | Reads a file. |
| `Add-Content` | Appends content to a file. |
| `Set-Content` | Replaces file contents. |
| `Clear-Content` | Clears a file without deleting it. |
| `Compare-Object` | Compares objects or their contents. |
| `tree /F` | Displays a directory tree, including files. |

---

## Key Takeaways

- PowerShell provides dedicated cmdlets for managing filesystem objects.
- `New-Item` allows us to create both files and directories.
- `Add-Content`, `Set-Content`, and `Clear-Content` perform different operations on file contents.
- `Rename-Item` can rename individual files or multiple files through pipelines.
- `$_` represents the current object being processed inside a PowerShell script block.
- Combining cmdlets through pipelines enables efficient bulk operations.
- NTFS permissions determine which filesystem operations users and groups can perform.
- Permission inheritance simplifies access control but can introduce security risks when permissions are incorrectly configured.
