# Permission Management

Linux permissions control **who can access files and directories and what actions they are allowed to perform**.

Every file and directory is associated with:

```text
Owner
Group
Permissions
```

Users may belong to multiple groups, which can give them additional access to system resources.

When we create a file or directory, it normally becomes associated with our user and one of our groups.

---

# Permission Types

Linux uses three basic permissions:

| Permission | Symbol | Meaning             |
| ---------- | -----: | ------------------- |
| Read       |    `r` | Read contents       |
| Write      |    `w` | Modify contents     |
| Execute    |    `x` | Execute or traverse |

However, these permissions behave slightly differently depending on whether they are applied to a **file** or a **directory**.

---

# Permissions on Files

For a regular file:

```text
r → Read the file

w → Modify the file

x → Execute the file
```

For example, a script generally needs:

```text
x
```

permission before we can execute it directly.

---

# Permissions on Directories

Directory permissions have different meanings.

```text
r → View/list directory contents

w → Create, delete, or rename entries

x → Traverse/access the directory
```

The `x` permission is particularly important.

Without execute permission on a directory, we cannot properly traverse into it or access its contents.

For example:

```bash
cry0l1t3@htb[/htb]$ ls -l

drw-rw-r-- 3 cry0l1t3 cry0l1t3 4096 Jan 12 12:30 scripts
```

Notice that the directory does not have:

```text
x
```

permission.

Attempting to access its contents can therefore generate:

```bash
cry0l1t3@htb[/htb]$ ls -al mydirectory/

ls: cannot access 'mydirectory/script.sh': Permission denied
ls: cannot access 'mydirectory/..': Permission denied
ls: cannot access 'mydirectory/subdirectory': Permission denied
ls: cannot access 'mydirectory/.': Permission denied
```

The important distinction is:

```text
Directory x
→ Allows traversal

File x
→ Allows execution
```

Execute permission on a directory does **not** automatically give permission to execute files inside it.

---

# Reading `ls -l` Permissions

Consider:

```bash
cry0l1t3@htb[/htb]$ ls -l /etc/passwd
```

A permission string may look like:

```text
-rwxrw-r--
```

We can divide it into four sections:

```text
-   rwx   rw-   r--
│    │     │     │
│    │     │     └── Others
│    │     └──────── Group
│    └────────────── Owner
└─────────────────── File type
```

---

# File Type

The first character indicates the type of object.

Examples:

```text
- → Regular file

d → Directory

l → Symbolic link
```

So:

```text
-rwxr-xr--
```

starts with:

```text
-
```

meaning it is a regular file.

While:

```text
drwxr-xr-x
```

starts with:

```text
d
```

meaning it is a directory.

---

# Owner, Group, and Others

The remaining nine permission characters are divided into groups of three:

```text
rwx rw- r--
│    │    │
│    │    └── Others
│    └─────── Group
└──────────── Owner
```

These correspond to:

```text
Owner
Group
Others
```

For example:

```text
rwx
```

means:

```text
Read    ✔
Write   ✔
Execute ✔
```

While:

```text
r--
```

means:

```text
Read    ✔
Write   ✘
Execute ✘
```

---

# Example

Consider:

```text
-rwxr-xr--
```

Breaking it down:

```text
Owner:   rwx
Group:   r-x
Others:  r--
```

Therefore:

| Category | Read | Write | Execute |
| -------- | ---: | ----: | ------: |
| Owner    |  Yes |   Yes |     Yes |
| Group    |  Yes |    No |     Yes |
| Others   |  Yes |    No |      No |

---

# Changing Permissions with `chmod`

We can modify permissions using:

```bash
chmod
```

There are two common approaches:

```text
Symbolic notation
Octal notation
```

---

# Symbolic Permission References

Linux uses the following symbols to identify permission groups:

| Symbol | Meaning      |
| ------ | ------------ |
| `u`    | User / owner |
| `g`    | Group        |
| `o`    | Others       |
| `a`    | All          |

We combine them with:

```text
+ → Add permission

- → Remove permission
```

---

# Adding Permissions

Suppose we have:

```bash
cry0l1t3@htb[/htb]$ ls -l shell

-rwxr-x--x 1 cry0l1t3 htbteam 0 May 4 22:12 shell
```

We can add read permission for everyone:

```bash
cry0l1t3@htb[/htb]$ chmod a+r shell && ls -l shell

-rwxr-xr-x 1 cry0l1t3 htbteam 0 May 4 22:12 shell
```

Breaking down:

```text
chmod a+r shell
      ││
      │└── Add read permission
      │
      └── All users
```

So:

```bash
chmod a+r shell
```

means:

> Add read permission for everyone.

---

# Symbolic Examples

```bash
chmod u+x file
```

means:

```text
Add execute permission to the owner
```

```bash
chmod g+w file
```

means:

```text
Add write permission to the group
```

```bash
chmod o-r file
```

means:

```text
Remove read permission from others
```

```bash
chmod a+x file
```

means:

```text
Add execute permission for everyone
```

---

# Octal Permissions

Linux permissions can also be represented using numbers.

The basic values are:

```text
Read    = 4
Write   = 2
Execute = 1
```

Therefore:

```text
r = 4
w = 2
x = 1
```

We add the values together to create permission combinations.

---

# Common Octal Values

| Value | Permission |
| ----: | ---------- |
|   `0` | `---`      |
|   `1` | `--x`      |
|   `2` | `-w-`      |
|   `3` | `-wx`      |
|   `4` | `r--`      |
|   `5` | `r-x`      |
|   `6` | `rw-`      |
|   `7` | `rwx`      |

The most important combinations are:

```text
7 = 4 + 2 + 1 = rwx

6 = 4 + 2     = rw-

5 = 4 + 1     = r-x

4 = 4         = r--
```

---

# Understanding `chmod 754`

The HTB example uses:

```bash
cry0l1t3@htb[/htb]$ chmod 754 shell && ls -l shell

-rwxr-xr-- 1 cry0l1t3 htbteam 0 May 4 22:12 shell
```

The three digits represent:

```text
7   5   4
│   │   │
│   │   └── Others
│   └────── Group
└────────── Owner
```

Now convert each number.

### Owner — `7`

```text
4 + 2 + 1 = 7

r + w + x

rwx
```

### Group — `5`

```text
4 + 1 = 5

r + x

r-x
```

### Others — `4`

```text
4

r

r--
```

So:

```text
754
```

becomes:

```text
rwx r-x r--
```

Therefore:

```bash
chmod 754 shell
```

produces:

```text
-rwxr-xr--
```

---

# Binary and Octal Representation

The HTB material represents this as:

```text
Binary Notation:       4 2 1 | 4 2 1 | 4 2 1
------------------------------------------------
Binary Representation: 1 1 1 | 1 0 1 | 1 0 0
------------------------------------------------
Octal Value:             7   |   5   |   4
------------------------------------------------
Permissions:           r w x | r - x | r - -
```

A bit set to:

```text
1
```

means the corresponding permission exists.

A bit set to:

```text
0
```

means it does not.

---

# Changing Ownership with `chown`

Permissions are only part of the access model.

Files also have:

```text
Owner
Group
```

We can change them using:

```bash
chown
```

General syntax:

```bash
chown <user>:<group> <file>
```

For example:

```bash
cry0l1t3@htb[/htb]$ chown root:root shell && ls -l shell

-rwxr-xr-- 1 root root 0 May 4 22:12 shell
```

Breaking it down:

```text
chown root:root shell
      │    │     │
      │    │     └── File
      │    └──────── New group
      └───────────── New owner
```

So:

```bash
chown root:root shell
```

sets:

```text
Owner → root
Group → root
```

---

# `chmod` vs `chown`

These commands do different things.

```text
chmod
→ Change permissions

chown
→ Change owner/group
```

For example:

```bash
chmod 754 shell
```

changes:

```text
rwxr-xr--
```

while:

```bash
chown root:root shell
```

changes:

```text
owner → root
group → root
```

---

# SUID

Linux also supports special permission bits.

One of them is:

```text
SUID
```

which stands for:

```text
Set User ID
```

When an executable has SUID enabled, it can run with the privileges of the **file owner** rather than only the privileges of the user who launched it.

Conceptually:

```text
Normal executable:

Our user
   ↓
Program
   ↓
Runs with our privileges
```

With SUID:

```text
Our user
   ↓
SUID Program
   ↓
Runs with owner's privileges
```

If the owner is:

```text
root
```

the application may execute with root privileges.

---

# SGID

`SGID` means:

```text
Set Group ID
```

It is similar to SUID, but it relates to the **group** associated with the file.

Conceptually:

```text
SUID → Execute with owner's privileges

SGID → Execute with group's privileges
```

---

# Identifying SUID and SGID

These permissions are represented by:

```text
s
```

instead of the normal:

```text
x
```

in the relevant permission position.

For example, we may encounter something conceptually like:

```text
-rwsr-xr-x
```

The:

```text
s
```

in the owner's execute position indicates SUID.

---

# Security Relevance of SUID and SGID

SUID and SGID are particularly important in cybersecurity.

They can legitimately allow users to perform specific privileged operations.

However, a poorly configured SUID or SGID executable can potentially become a privilege escalation path.

The HTB material gives the example of an application such as:

```text
journalctl
```

If an administrator assigns SUID to a program that can indirectly launch a shell, a regular user may potentially obtain a shell with the file owner's privileges.

If that owner is:

```text
root
```

this can lead to complete system compromise.

For this reason, SUID and SGID binaries are important targets during Linux enumeration.

---

# Sticky Bit

The **sticky bit** is another special Linux permission.

It is primarily useful on shared directories.

Normally, if multiple users have write access to a directory, they may be able to manipulate entries inside it.

With the sticky bit enabled, deletion and renaming are restricted.

Only the following can normally delete or rename a file in such a directory:

```text
The file owner
The directory owner
root
```

This is useful for shared directories where multiple users need access but should not be able to remove each other's files.

---

# Sticky Bit Representation

The sticky bit appears in the **others execute position**.

For example:

```bash
cry0l1t3@htb[/htb]$ ls -l

drw-rw-r-t 3 cry0l1t3 cry0l1t3 4096 Jan 12 12:30 scripts
drw-rw-r-T 3 cry0l1t3 cry0l1t3 4096 Jan 12 12:32 reports
```

Notice:

```text
t
```

and:

```text
T
```

---

# Lowercase `t`

A lowercase:

```text
t
```

means:

```text
Sticky bit is set
+
Others have execute permission
```

Example:

```text
drw-rw-r-t
```

---

# Uppercase `T`

An uppercase:

```text
T
```

means:

```text
Sticky bit is set
+
Others do NOT have execute permission
```

Example:

```text
drw-rw-r-T
```

Therefore:

```text
t → Sticky bit + execute

T → Sticky bit without execute
```

---

# Permission Mental Model

When we see:

```text
-rwxr-xr--
```

we should mentally split it immediately:

```text
- | rwx | r-x | r--
    │      │      │
    │      │      └── Others
    │      └───────── Group
    └──────────────── Owner
```

Then translate:

```text
Owner:
read + write + execute

Group:
read + execute

Others:
read only
```

---

# Octal Mental Model

The easiest values to memorize are:

```text
r = 4
w = 2
x = 1
```

Then simply add them.

```text
rwx = 4 + 2 + 1 = 7

rw- = 4 + 2     = 6

r-x = 4 + 1     = 5

r-- = 4         = 4
```

So:

```text
755
```

can immediately become:

```text
7 → rwx
5 → r-x
5 → r-x
```

giving:

```text
rwxr-xr-x
```

---

# Quick Reference

| Concept    | Meaning                                          |
| ---------- | ------------------------------------------------ |
| `r`        | Read                                             |
| `w`        | Write                                            |
| `x`        | Execute / traverse                               |
| `u`        | Owner                                            |
| `g`        | Group                                            |
| `o`        | Others                                           |
| `a`        | All                                              |
| `chmod`    | Change permissions                               |
| `chown`    | Change owner/group                               |
| `4`        | Read                                             |
| `2`        | Write                                            |
| `1`        | Execute                                          |
| SUID       | Execute with file owner's privileges             |
| SGID       | Execute with file group's privileges             |
| Sticky bit | Restrict deletion/renaming in shared directories |

---

# Commands to Remember

```bash
ls -l
```

View permissions and ownership.

```bash
chmod a+r file
```

Add read permission for everyone.

```bash
chmod u+x file
```

Add execute permission for the owner.

```bash
chmod 754 file
```

Set permissions using octal notation.

```bash
chown user:group file
```

Change owner and group.

---

# Most Important Octal Values

```text
0 = ---
1 = --x
2 = -w-
3 = -wx
4 = r--
5 = r-x
6 = rw-
7 = rwx
```

The values we will see especially often are:

```text
7 = rwx

6 = rw-

5 = r-x

4 = r--
```

---

## Key Takeaway

**Linux permissions control access through `read (r)`, `write (w)`, and `execute (x)` permissions assigned separately to the owner, group, and others. We can modify these permissions with `chmod`, including octal notation based on `r=4`, `w=2`, and `x=1`, and modify ownership with `chown`. Special permissions such as SUID, SGID, and the sticky bit extend this model and are especially important from a security perspective because incorrect configurations can expose privileged access.**
