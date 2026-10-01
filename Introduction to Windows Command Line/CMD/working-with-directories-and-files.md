
# Working with Directories and Files - CMD

---

## Directories

Directories are folders within the Windows filesystem that organize files and other directories into a hierarchical structure.

We can navigate and explore this structure using the commands introduced in the previous sections.

| Command | Description |
|---|---|
| `cd` | Displays or changes our current working directory. |
| `dir` | Lists files and directories. |
| `tree` | Displays the directory hierarchy. |
| `tree /F` | Displays the directory hierarchy, including files. |

### Creating Directories

We can create directories using either `md` or `mkdir`. Both commands perform the same operation.

```cmd
md new-directory
mkdir another-directory
```

We can verify that our directories were created by executing:

```cmd
dir
```

### Deleting Directories

The `rd` and `rmdir` commands allow us to remove directories.

```cmd
rd new-directory
```

By default, these commands can only remove empty directories. Attempting to remove a directory containing files or subdirectories produces an error.

To recursively delete a directory and all its contents, we can use the `/S` parameter.

```cmd
rd /S new-directory
```

CMD will request confirmation before deleting the directory.

We can also use `/Q` to suppress confirmation:

```cmd
rd /S /Q new-directory
```

**Important:** These commands permanently delete the specified directory and its contents. We should always verify the target path before executing recursive deletion.

---

## Moving and Copying Directories

Windows provides several commands for moving and copying directories, including `move`, `xcopy`, and `robocopy`.

### Moving Directories

The `move` command allows us to relocate directories and their contents.

**Syntax:**

```cmd
move <source> <destination>
```

For example:

```cmd
move example C:\Users\htb\Documents\example
```

This moves the `example` directory from our current location into the user's Documents directory.

We can verify the operation using:

```cmd
dir C:\Users\htb\Documents
```

### Xcopy

`xcopy` is a Windows utility designed to copy files and directory structures. Although it remains available, Microsoft recommends `robocopy` for many modern file-copying tasks.

**Syntax:**

```cmd
xcopy <source> <destination> <options>
```

For example:

```cmd
xcopy C:\Users\htb\Documents\example C:\Users\htb\Desktop\example /E
```

This copies the `example` directory and its contents to the Desktop.

Common parameters:

| Parameter | Description |
|---|---|
| `/E` | Copies all subdirectories, including empty ones. |
| `/S` | Copies directories and subdirectories, excluding empty ones. |
| `/K` | Preserves file attributes, including the read-only attribute. |

By default, `xcopy` does not preserve the read-only attribute on copied files. We can use `/K` when we need to retain it.

Unlike `move`, copying files does not remove them from their original location.

### Robocopy

`robocopy` (Robust File Copy) is a more advanced Windows utility designed for copying and synchronizing files and directories.

It supports local drives, network shares, large directory structures, and the preservation of file attributes and metadata.

**Syntax:**

```cmd
robocopy <source> <destination> <options>
```

For example:

```cmd
robocopy C:\Users\htb\Desktop C:\Users\htb\Documents
```

This copies files from the Desktop into Documents. By default, `robocopy` processes files in the source directory without recursively copying its subdirectories.

Common parameters:

| Parameter | Description |
|---|---|
| `/E` | Copies all subdirectories, including empty ones. |
| `/S` | Copies subdirectories, excluding empty ones. |
| `/MIR` | Mirrors the source directory to the destination. |
| `/B` | Copies files using backup mode. |
| `/L` | Lists the operations that would be performed without executing them. |
| `/A-:SH` | Removes the System and Hidden attributes from copied files. |

#### Mirroring Directories

We can use `/MIR` to synchronize a destination directory with its source.

```cmd
robocopy C:\Users\htb\Desktop\notes C:\Users\htb\Documents\Backup /MIR
```

**Important:** `/MIR` can delete files and directories from the destination if they do not exist in the source. We should avoid using this parameter on directories containing information we want to preserve.

Before executing a potentially destructive operation, we can combine `/MIR` with `/L` to preview the changes.

```cmd
robocopy C:\Users\htb\Desktop\notes C:\Users\htb\Documents\Backup /MIR /L
```

#### Backup Mode

The `/B` parameter allows `robocopy` to operate in backup mode.

```cmd
robocopy C:\Source C:\Backup /E /B
```

This mode requires appropriate backup privileges. Without them, the operation may fail.

**Technical note:** `/MIR` does not bypass NTFS permissions or grant backup privileges. It controls directory synchronization, while `/B` controls backup-mode access.

---

## Files

Windows provides several built-in commands for viewing, creating, modifying, deleting, copying, and moving files.

### Viewing File Contents

We can use `more` and `type` to inspect text files directly from CMD.

#### More

The `more` command displays text one screen at a time, preventing large outputs from overwhelming our terminal.

```cmd
more secrets.txt
```

We can use the Space bar to advance through the output or Enter to advance line by line.

The `/S` parameter compresses consecutive blank lines into a single blank line.

```cmd
more /S secrets.txt
```

We can also redirect the output of another command into `more` using a pipe:

```cmd
ipconfig /all | more
```

This allows us to inspect lengthy command outputs one screen at a time.

#### Type

The `type` command displays the contents of one or more text files.

```cmd
type bio.txt
```

We can also display multiple files:

```cmd
type file1.txt file2.txt
```

Unlike `more`, `type` prints the contents directly to the terminal without pagination.

We can combine `type` with output redirection to copy the contents of one text file into another.

```cmd
type passwords.txt >> secrets.txt
```

This appends the contents of `passwords.txt` to the end of `secrets.txt`.

### Openfiles

The `openfiles` utility allows us to inspect files opened by users or processes, particularly through network shares.

It can also be used to disconnect open file sessions.

```cmd
openfiles /query
```

This functionality requires appropriate administrative privileges. Local file tracking is not enabled by default on Windows.

---

## Creating and Modifying Files

### Echo

We can combine `echo` with output redirection to create or modify text files.

To create a file:

```cmd
echo Check out this text > demo.txt
```

To append additional content:

```cmd
echo More text for our demo file >> demo.txt
```

We can then verify the contents:

```cmd
type demo.txt
```

**Important:** The `>` operator creates a file or overwrites its existing contents, while `>>` appends data without replacing the existing contents.

### Fsutil

The `fsutil` utility provides advanced filesystem management functionality, including the ability to create files with a specified size.

For example:

```cmd
fsutil file createnew example.txt 222
```

This creates a file named `example.txt` with a size of 222 bytes.

### Renaming Files

We can rename files using either `ren` or `rename`.

```cmd
ren demo.txt superdemo.txt
```

Both commands perform the same operation.

---

## Input and Output Redirection

CMD provides several operators for controlling command input, output, and execution.

| Operator | Description |
|---|---|
| `>` | Redirects output to a file, overwriting existing contents. |
| `>>` | Appends output to a file. |
| `<` | Uses a file as standard input for a command. |
| `\|` | Sends the output of one command into another command. |
| `&` | Executes two commands sequentially, regardless of success or failure. |
| `&&` | Executes the second command only if the first succeeds. |
| `\|\|` | Executes the second command only if the first fails. |

### Redirecting Output

We can save the results of a command to a file.

```cmd
ipconfig /all > details.txt
```

This creates `details.txt` containing the output of `ipconfig /all`.

### Redirecting Input

The `<` operator allows us to provide the contents of a file as input to another command.

```cmd
find /i "see" < test.txt
```

This searches the contents of `test.txt` for the string `see`.

The `/i` parameter instructs `find` to perform a case-insensitive search.

### Piping Commands

The pipe operator (`|`) sends the output of one command directly into another.

```cmd
ipconfig /all | find /i "IPv4"
```

This executes `ipconfig /all` and filters the output to display lines containing `IPv4`.

Pipelines are particularly useful when working with commands that generate large amounts of output.

### Conditional Command Execution

CMD also supports executing multiple commands based on their success or failure.

**Execute both commands:**

```cmd
ping 8.8.8.8 & type test.txt
```

The second command executes after the first finishes, regardless of whether the first succeeds.

**Execute the second command only if the first succeeds:**

```cmd
cd C:\Users\htb\Documents && echo Success > result.txt
```

**Execute the second command only if the first fails:**

```cmd
cd C:\Nonexistent || echo Directory not found
```

---

## Deleting Files

We can delete files using either `del` or `erase`.

```cmd
del file1.txt
```

Both commands provide the same functionality.

We can also delete multiple files:

```cmd
erase file1.txt file2.txt
```

### Common Parameters

| Parameter | Description |
|---|---|
| `/P` | Requests confirmation before deleting each file. |
| `/F` | Forces deletion of read-only files. |
| `/S` | Deletes matching files from subdirectories. |
| `/Q` | Suppresses confirmation prompts. |
| `/A` | Selects files based on their attributes. |

### File Attributes

Windows files can have attributes that influence their visibility and behavior.

| Attribute | Description |
|---|---|
| `R` | Read-only |
| `H` | Hidden |
| `S` | System |
| `A` | Archive |
| `I` | Not content indexed |
| `L` | Reparse point |
| `O` | Offline |

We can use `dir` to identify files with specific attributes.

For example, to display read-only files:

```cmd
dir /A:R
```

To display hidden files:

```cmd
dir /A:H
```

To delete a read-only file, we can use `/F`:

```cmd
del /F example.txt
```

We can combine `/A` with a specific attribute to select matching files.

For example, the following command targets hidden files in the current directory:

```cmd
del /A:H *
```

**Important:** Wildcards such as `*` can match multiple files. We should inspect the files matching our criteria before performing deletion.

---

## Copying and Moving Files

### Copy

The `copy` command allows us to duplicate files.

```cmd
copy secrets.txt C:\Users\htb\Downloads\not-secrets.txt
```

This creates a copy of `secrets.txt` in the Downloads directory under a different name, leaving the original file unchanged.

We can also use `/V` to verify that files were written correctly.

```cmd
copy secrets.txt C:\Backup\secrets.txt /V
```

### Move

The `move` command relocates files or directories from one location to another.

```cmd
move C:\Users\htb\Desktop\bio.txt C:\Users\htb\Downloads
```

Unlike `copy`, `move` removes the file from its original location after a successful operation.

---

## Key Takeaways

- `md` and `mkdir` create directories, while `rd` and `rmdir` remove them.
- `move`, `xcopy`, and `robocopy` allow us to relocate, duplicate, and synchronize directory structures.
- `more` and `type` allow us to inspect text files directly from CMD.
- `echo` and `fsutil` provide different ways to create files.
- `ren` changes file names, while `del` and `erase` remove files.
- Input and output redirection operators allow us to save command results, filter information, and execute commands conditionally.
- File attributes influence file visibility and behavior and can be inspected using `dir /A`.
- Understanding these built-in utilities allows us to navigate, enumerate, and manipulate Windows filesystems efficiently during system administration and authorized security assessments.
