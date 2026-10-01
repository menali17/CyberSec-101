
# System Navigation

---

## Listing a Directory

The `dir` command allows us to list the files and subdirectories within our current working directory.

```cmd
C:\Users\htb\Desktop> dir
```

Example output:

```text
Directory of C:\Users\htb\Desktop

06/11/2021  11:59 PM    <DIR>          .
06/11/2021  11:59 PM    <DIR>          ..
06/11/2021  11:57 PM                 0 file1.txt
06/11/2021  11:57 PM                 0 file2.txt
06/11/2021  11:57 PM                 0 file3.txt
06/11/2021  11:57 PM                 0 super-secret-sauce.txt
06/11/2021  11:59 PM                 0 write-secrets.ps1
```

The output displays file names, sizes, modification timestamps, and subdirectories.

We can also use `dir /?` to display the available parameters and explore its advanced functionality.

---

## Finding Our Current Location

Before navigating a Windows host, we need to identify our **current working directory**.

We can use either `cd` or `chdir` without additional arguments:

```cmd
C:\htb> cd

C:\htb
```

The current working directory determines the starting point for commands that reference files or directories without specifying an absolute path.

---

## Moving Around Using CD/CHDIR

The `cd` and `chdir` commands allow us to navigate between directories.

We can specify either an **absolute path** or a **relative path**.

### Absolute Paths

An absolute path specifies the complete location of a directory, starting from the root of its drive.

For example:

```cmd
C:\htb> cd C:\Users\htb\Pictures

C:\Users\htb\Pictures>
```

In this example, `C:\` represents the root directory of the C: drive.

Because we provided the complete path, our destination does not depend on our original working directory.

### Relative Paths

A relative path specifies a location in relation to our current working directory.

For example:

```cmd
C:\Users\htb> cd .\Pictures

C:\Users\htb\Pictures>
```

The `.` character represents our current directory, while `..` represents its parent directory.

We can use these references to navigate through the filesystem without specifying complete paths.

| Command | Description |
|---|---|
| `cd` | Displays our current working directory. |
| `cd .` | References our current directory. |
| `cd ..` | Moves to the parent directory. |
| `cd .\Pictures` | Moves into the `Pictures` subdirectory. |
| `cd C:\` | Moves to the root of the C: drive, when already on that drive. |
| `cd ..\..\..\` | Moves up three directory levels. |

For example, we can move directly from a user's Pictures directory to the root:

```cmd
C:\Users\htb\Pictures> cd ..\..\..\

C:\>
```

Understanding relative and absolute paths allows us to navigate efficiently without repeatedly typing complete directory paths.

---

## Exploring the File System

During system enumeration, repeatedly navigating between directories can become inefficient.

The `tree` command allows us to display the directory structure of a specified path and its subdirectories.

### Listing the Directory Structure

```cmd
C:\Users\htb> tree
```

Example output:

```text
C:.
├── Desktop
├── Documents
├── Downloads
├── Music
├── Pictures
│   ├── Camera Roll
│   └── Saved Pictures
└── Videos
    └── Captures
```

This command provides a hierarchical representation of the directory structure.

### Listing Directories and Files

By default, `tree` displays directories without listing their individual files.

We can use the `/F` parameter to include files:

```cmd
C:\Users\htb> tree /F
```

Example output:

```text
C:.
├── Desktop
│       passwords.txt.txt
│       Project plans.txt
│       secrets.txt
│
├── Documents
├── Downloads
├── Music
└── Pictures
    ├── Camera Roll
    └── Saved Pictures
```

From a penetration testing perspective, this command is useful for identifying potentially interesting files and directories, such as configuration files, project documents, and files that may contain exposed credentials.

**Important:** Running `tree /F` against a large directory structure can generate substantial output. We can interrupt its execution using `Ctrl + C` when necessary.

---

## Interesting Directories

Certain Windows directories are particularly relevant during system enumeration and penetration testing.

They can help us identify installed applications, locate temporary files, and understand which locations are accessible to our current user.

| Environment Variable | Default Location | Description |
|---|---|---|
| `%SYSTEMROOT%\Temp` | `C:\Windows\Temp` | Shared Windows directory for temporary system files. |
| `%TEMP%` | `C:\Users\<user>\AppData\Local\Temp` | Temporary directory associated with the current user. |
| `%PUBLIC%` | `C:\Users\Public` | Shared directory for files intended to be accessible by multiple users. |
| `%ProgramFiles%` | `C:\Program Files` | Default installation directory for 64-bit applications on 64-bit Windows. |
| `%ProgramFiles(x86)%` | `C:\Program Files (x86)` | Default installation directory for 32-bit applications on 64-bit Windows. |

### Accessing These Directories

We can navigate to these locations using their environment variables instead of manually typing their complete paths.

For example:

```cmd
cd %TEMP%
```

This takes us to our current user's temporary directory.

We can also inspect the shared Public directory:

```cmd
dir %PUBLIC%
```

To enumerate installed applications, we can inspect:

```cmd
dir "%ProgramFiles%"
```

```cmd
dir "%ProgramFiles(x86)%"
```

Quotation marks are necessary when paths contain spaces.

The actual locations of these directories and their access permissions may vary depending on the Windows configuration and filesystem permissions.

### Relevance to Penetration Testing

During an authorized security assessment, these directories can provide valuable information about a Windows host.

- **Temporary directories:** Identify temporary files, application artifacts, and potentially exposed information.
- **Public directories:** Examine files shared between different users.
- **Program Files:** Identify installed applications that may expand the system's attack surface.
- **User directories:** Discover application configurations, documents, and other information relevant to our assessment.

---

## Key Takeaways

- `dir` lists the contents of our current directory.
- `cd` and `chdir` allow us to identify and change our current working directory.
- Absolute paths specify a complete location, while relative paths depend on our current directory.
- `.` represents the current directory, and `..` represents the parent directory.
- `tree` displays the directory hierarchy, while `tree /F` also includes files.
- Windows environment variables provide convenient references to important system directories.
- Filesystem enumeration helps us understand the structure of a Windows host and identify potentially sensitive files and installed applications.
