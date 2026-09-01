# Working with Files and Directories

Linux provides several commands for creating, moving, renaming, copying, and organizing files and directories directly from the terminal.

The main commands introduced in this section are:

| Command    | Purpose                                           |
| ---------- | ------------------------------------------------- |
| `touch`    | Create an empty file                              |
| `mkdir`    | Create a directory                                |
| `mkdir -p` | Create directories and missing parent directories |
| `tree`     | Display directories in a tree structure           |
| `mv`       | Move or rename files/directories                  |
| `cp`       | Copy files                                        |

---

# `touch` — Create an Empty File

The `touch` command can be used to create an empty file.

## Syntax

```bash
menali@htb[/htb]$ touch <name>
```

For example, to create:

```text
info.txt
```

use:

```bash
menali@htb[/htb]$ touch info.txt
```

If the file does not already exist, an empty file is created.

---

# `mkdir` — Create a Directory

The `mkdir` command creates a new directory.

`mkdir` stands for:

**Make Directory**

## Syntax

```bash
menali@htb[/htb]$ mkdir <name>
```

For example:

```bash
menali@htb[/htb]$ mkdir Storage
```

This creates:

```text
Storage/
```

in the current working directory.

---

# `mkdir -p` — Create Parent Directories

Sometimes we need to create multiple nested directories.

Instead of creating each directory individually:

```bash
mkdir Storage
mkdir Storage/local
mkdir Storage/local/user
mkdir Storage/local/user/documents
```

we can use:

```bash
menali@htb[/htb]$ mkdir -p Storage/local/user/documents
```

The `-p` option means:

**parents**

It automatically creates any missing parent directories.

The resulting structure is:

```text
Storage/
└── local/
    └── user/
        └── documents/
```

---

# `tree` — Display Directory Structure

The `tree` command displays files and directories hierarchically.

After creating `info.txt` and the directory structure:

```bash
menali@htb[/htb]$ tree .

.
├── info.txt
└── Storage
    └── local
        └── user
            └── documents

4 directories, 1 file
```

The:

```text
.
```

represents the **current directory**.

Therefore:

```bash
tree .
```

means:

> Display the directory tree starting from the current directory.

---

# Creating Files Using Paths

We do not need to navigate into a directory before creating a file inside it.

We can specify the path directly.

For example:

```bash
menali@htb[/htb]$ touch ./Storage/local/user/userinfo.txt
```

Here:

```text
.
```

means the current directory.

The path:

```text
./Storage/local/user/userinfo.txt
```

means:

```text
Current Directory
       ↓
    Storage
       ↓
     local
       ↓
      user
       ↓
  userinfo.txt
```

After creating the file:

```bash
menali@htb[/htb]$ tree .

.
├── info.txt
└── Storage
    └── local
        └── user
            ├── documents
            └── userinfo.txt

4 directories, 2 files
```

---

# `mv` — Move and Rename

The `mv` command can perform two important operations:

* Move files/directories.
* Rename files/directories.

`mv` stands for:

**Move**

---

# Renaming a File

The syntax shown in the material is:

```bash
menali@htb[/htb]$ mv <file/directory> <renamed file/directory>
```

For example, we can rename:

```text
info.txt
```

to:

```text
information.txt
```

using:

```bash
menali@htb[/htb]$ mv info.txt information.txt
```

Conceptually:

```text
info.txt
   ↓
information.txt
```

---

# Moving Files

First, create another file:

```bash
menali@htb[/htb]$ touch readme.txt
```

Now suppose we have:

```text
information.txt
readme.txt
Storage/
```

We can move both files into `Storage/`:

```bash
menali@htb[/htb]$ mv information.txt readme.txt Storage/
```

Afterward:

```bash
menali@htb[/htb]$ tree .

.
└── Storage
    ├── information.txt
    ├── local
    │   └── user
    │       ├── documents
    │       └── userinfo.txt
    └── readme.txt

4 directories, 3 files
```

The general idea is:

```text
mv SOURCE DESTINATION
```

For example:

```bash
mv file.txt directory/
```

moves `file.txt` into `directory/`.

---

# Moving Multiple Files

Multiple files can be moved simultaneously.

Example:

```bash
mv file1.txt file2.txt file3.txt Storage/
```

Here:

```text
file1.txt ─┐
file2.txt ─┼──→ Storage/
file3.txt ─┘
```

The final argument represents the destination directory.

---

# `cp` — Copy Files

The `cp` command copies files.

`cp` stands for:

**Copy**

Unlike `mv`, the original file remains in its original location.

General concept:

```text
mv → Move the original

cp → Create a copy
```

---

# Copying a File

Suppose we have:

```text
Storage/
├── readme.txt
└── local/
```

We can copy `readme.txt` into `local/` using:

```bash
menali@htb[/htb]$ cp Storage/readme.txt Storage/local/
```

Afterward:

```bash
menali@htb[/htb]$ tree .

.
└── Storage
    ├── information.txt
    ├── local
    │   ├── readme.txt
    │   └── user
    │       ├── documents
    │       └── userinfo.txt
    └── readme.txt

4 directories, 4 files
```

Notice that `readme.txt` now exists in **both locations**:

```text
Storage/readme.txt

Storage/local/readme.txt
```

The original file was not removed.

---

# `mv` vs `cp`

This distinction is important:

### `mv`

```bash
mv file.txt Storage/
```

Result:

```text
Before:

file.txt
Storage/

After:

Storage/
└── file.txt
```

The original file was **moved**.

### `cp`

```bash
cp file.txt Storage/
```

Result:

```text
Before:

file.txt
Storage/

After:

file.txt
Storage/
└── file.txt
```

The original remains and a **copy** is created.

---

# Working with Paths

Commands such as `touch`, `mv`, and `cp` can work directly with paths.

For example:

```bash
touch ./Storage/file.txt
```

creates a file inside `Storage`.

```bash
cp Storage/file.txt Storage/local/
```

copies a file between directories.

```bash
mv Storage/file.txt Storage/local/
```

moves a file between directories.

This means we do not always need to use `cd` before manipulating files.

---

# Directory Structure Example

After performing the operations from this section, the final structure is:

```text
.
└── Storage
    ├── information.txt
    ├── local
    │   ├── readme.txt
    │   └── user
    │       ├── documents
    │       └── userinfo.txt
    └── readme.txt
```

This structure was created primarily using:

```bash
touch
mkdir
mkdir -p
mv
cp
tree
```

---


# Quick Reference

```bash
touch info.txt
# Create an empty file

mkdir Storage
# Create a directory

mkdir -p Storage/local/user/documents
# Create nested directories and missing parents

tree .
# Display the current directory structure

touch ./Storage/local/user/userinfo.txt
# Create a file at a specific path

mv info.txt information.txt
# Rename a file

mv information.txt Storage/
# Move a file

mv file1.txt file2.txt Storage/
# Move multiple files

cp Storage/readme.txt Storage/local/
# Copy a file
```

---


## Key Takeaway

**Linux files and directories can be efficiently managed from the command line. `touch` creates empty files, `mkdir` creates directories, `mkdir -p` creates nested directory structures, `mv` moves or renames files, `cp` copies files, and `tree` provides a visual representation of the resulting directory hierarchy.**
