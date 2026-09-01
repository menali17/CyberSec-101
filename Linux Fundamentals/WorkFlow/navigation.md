# Navigation

Navigation in Linux is essential for moving through the filesystem and working with files and directories.

The main commands introduced in this section are:

* `pwd` — Show the current directory.
* `ls` — List directory contents.
* `cd` — Change directory.
* `clear` — Clear the terminal.

The shell also provides useful shortcuts such as:

* `Tab` — Auto-completion.
* `↑` / `↓` — Navigate command history.
* `Ctrl + L` — Clear the terminal.
* `Ctrl + R` — Search command history.

---

# `pwd` — Print Working Directory

Before navigating through the filesystem, we can determine our current location using:

```bash
cry0l1t3@htb[~]$ pwd

/home/cry0l1t3
```

`pwd` stands for:

**Print Working Directory**

It returns the full path of the directory we are currently working in.

---

# `ls` — List Directory Contents

The `ls` command lists files and directories.

```bash
cry0l1t3@htb[~]$ ls

Desktop  Documents  Downloads  Music  Pictures  Public  Templates  Videos
```

Without additional options, `ls` displays only the names of visible files and directories.

---

# `ls -l` — Long Listing Format

The `-l` option displays detailed information about files and directories.

```bash
cry0l1t3@htb[~]$ ls -l

total 32
drwxr-xr-x 2 cry0l1t3 htbacademy 4096 Nov 13 17:37 Desktop
drwxr-xr-x 2 cry0l1t3 htbacademy 4096 Nov 13 17:34 Documents
drwxr-xr-x 3 cry0l1t3 htbacademy 4096 Nov 15 03:26 Downloads
drwxr-xr-x 2 cry0l1t3 htbacademy 4096 Nov 13 17:34 Music
drwxr-xr-x 2 cry0l1t3 htbacademy 4096 Nov 13 17:34 Pictures
drwxr-xr-x 2 cry0l1t3 htbacademy 4096 Nov 13 17:34 Public
drwxr-xr-x 2 cry0l1t3 htbacademy 4096 Nov 13 17:34 Templates
drwxr-xr-x 2 cry0l1t3 htbacademy 4096 Nov 13 17:34 Videos
```

A line such as:

```bash
drwxr-xr-x 2 cry0l1t3 htbacademy 4096 Nov 13 17:37 Desktop
```

contains several pieces of information:

| Field          | Meaning                   |
| -------------- | ------------------------- |
| `drwxr-xr-x`   | File type and permissions |
| `2`            | Number of hard links      |
| `cry0l1t3`     | Owner                     |
| `htbacademy`   | Group owner               |
| `4096`         | Size                      |
| `Nov 13 17:37` | Date and time             |
| `Desktop`      | File/directory name       |

The first character can also indicate the object type.

For example:

```text
d → Directory
- → Regular file
```

---

# Hidden Files

Linux files and directories whose names begin with a dot (`.`) are considered **hidden**.

Examples:

```text
.bashrc
.bash_history
```

A normal:

```bash
ls
```

does not display them.

---

# `ls -la`

To display detailed information about **all files**, including hidden files, use:

```bash
cry0l1t3@htb[~]$ ls -la

total 403188
drwxr-xr-x 2 cry0l1t3 htbacademy 4096 Nov 13 17:37 .bash_history
drwxr-xr-x 2 cry0l1t3 htbacademy 4096 Nov 13 17:37 .bashrc
...SNIP...
drwxr-xr-x 2 cry0l1t3 htbacademy 4096 Nov 13 17:37 Desktop
drwxr-xr-x 2 cry0l1t3 htbacademy 4096 Nov 13 17:34 Documents
drwxr-xr-x 3 cry0l1t3 htbacademy 4096 Nov 15 03:26 Downloads
```

The options are:

```text
-l → Long listing format
-a → Show all entries, including hidden files
```

Therefore:

```bash
ls -la
```

means:

**List all files in long format.**

---

# Listing Another Directory

We do not need to navigate into a directory before listing its contents.

We can provide its path directly to `ls`.

Example:

```bash
cry0l1t3@htb[~]$ ls -l /var/

total 52
drwxr-xr-x  2 root root     4096 Mai 15 18:54 backups
drwxr-xr-x 18 root root     4096 Nov 15 16:55 cache
drwxrwsrwt  2 root whoopsie 4096 Jul 25  2018 crash
drwxr-xr-x 66 root root     4096 Mai 15 03:08 lib
drwxrwsr-x  2 root staff    4096 Nov 24  2018 local
<SNIP>
```

General syntax:

```bash
ls [options] <path>
```

For example:

```bash
ls -la /etc
```

lists the contents of `/etc` without changing the current working directory.

---

# `cd` — Change Directory

The `cd` command is used to move between directories.

`cd` stands for:

**Change Directory**

Example:

```bash
cry0l1t3@htb[~]$ cd /dev/shm

cry0l1t3@htb[/dev/shm]$
```

We have moved directly from the home directory to:

```text
/dev/shm
```

---

# Absolute Paths

An **absolute path** specifies the complete location beginning from the filesystem root `/`.

Example:

```bash
cd /dev/shm
```

The path:

```text
/dev/shm
```

starts at `/`, so it identifies exactly where the directory is located.

This allows us to jump directly to a directory without navigating through every intermediate directory.

Instead of:

```bash
cd /dev
cd shm
```

we can use:

```bash
cd /dev/shm
```

---

# `cd -` — Previous Directory

The command:

```bash
cd -
```

returns to the directory we were previously working in.

Example:

```bash
cry0l1t3@htb[/dev/shm]$ cd -

cry0l1t3@htb[~]$
```

This is useful for quickly switching between two directories.

Conceptually:

```text
/home/user → /dev/shm

cd -

/dev/shm → /home/user
```

---

# Tab Auto-Completion

The shell provides **auto-completion** using the `Tab` key.

Suppose we type:

```bash
cd /dev/s
```

and press `Tab` twice.

The shell may display:

```bash
cry0l1t3@htb[~]$ cd /dev/s [TAB 2x]

shm/  snd/
```

This shows all matching entries beginning with:

```text
s
```

If we continue typing:

```bash
cd /dev/sh
```

and only one matching directory exists, pressing `Tab` can automatically complete it:

```bash
cd /dev/shm/
```

Tab completion is extremely useful for:

* Faster navigation
* Completing filenames
* Completing directory names
* Reducing typing mistakes

---

# `.` — Current Directory

When listing all files:

```bash
cry0l1t3@htb[/dev/shm]$ ls -la /dev/shm

total 0
drwxrwxrwt  2 root root   40 Mai 15 18:31 .
drwxr-xr-x 17 root root 4000 Mai 14 20:45 ..
```

we see two special entries:

```text
.
..
```

A single dot:

```text
.
```

represents the **current directory**.

Therefore:

```bash
./script.sh
```

means a file called `script.sh` located in the current directory.

---

# `..` — Parent Directory

Two dots:

```text
..
```

represent the **parent directory**.

For example, if we are in:

```text
/dev/shm
```

then:

```text
.
→ /dev/shm
```

while:

```text
..
→ /dev
```

We can move to the parent directory using:

```bash
cry0l1t3@htb[/dev/shm]$ cd ..

cry0l1t3@htb[/dev]$
```

---

# `clear`

The `clear` command clears the contents currently displayed in the terminal.

```bash
cry0l1t3@htb[/dev]$ clear
```

This does not delete files or command history. It simply cleans the terminal display.

---

# Chaining Commands with `&&`

Commands can be combined using:

```text
&&
```

Example from the material:

```bash
cry0l1t3@htb[/dev]$ cd shm && clear
```

This performs:

```text
cd shm
   ↓
If successful
   ↓
clear
```

The second command runs **only if the first command succeeds**.

So:

```bash
cd shm && clear
```

means:

1. Change to `shm`.
2. If that succeeds, clear the terminal.

---

# `Ctrl + L`

Instead of typing:

```bash
clear
```

we can use:

```text
Ctrl + L
```

This provides a quick way to clear the terminal display.

---

# Command History

The shell keeps a history of previously executed commands.

The arrow keys can navigate through this history:

```text
↑ → Previous command

↓ → Next command
```

For example, repeatedly pressing `↑` allows us to return to commands we executed earlier without typing them again.

---

# `Ctrl + R` — Search Command History

Instead of repeatedly pressing the arrow keys, we can search command history using:

```text
Ctrl + R
```

After pressing it, type part of a previous command.

For example, searching for:

```text
ssh
```

can find a previously executed SSH command.

This is especially useful when trying to find long commands that were executed earlier.

---

# Navigation Cheat Sheet

| Command / Shortcut | Purpose                                    |
| ------------------ | ------------------------------------------ |
| `pwd`              | Show current directory                     |
| `ls`               | List visible contents                      |
| `ls -l`            | Detailed listing                           |
| `ls -a`            | Include hidden files                       |
| `ls -la`           | Detailed listing including hidden files    |
| `ls <path>`        | List another directory without entering it |
| `cd <directory>`   | Change directory                           |
| `cd -`             | Return to previous directory               |
| `cd ..`            | Move to parent directory                   |
| `.`                | Current directory                          |
| `..`               | Parent directory                           |
| `Tab`              | Auto-complete                              |
| `Tab` twice        | Display possible completions               |
| `clear`            | Clear terminal                             |
| `Ctrl + L`         | Clear terminal                             |
| `↑` / `↓`          | Navigate command history                   |
| `Ctrl + R`         | Search command history                     |

---

# Quick Reference

```bash
pwd
# Show current working directory

ls
# List visible files and directories

ls -l
# Long/detailed listing

ls -la
# Detailed listing including hidden files

ls -l /var
# List /var without navigating into it

cd /dev/shm
# Move directly to /dev/shm

cd ..
# Move to parent directory

cd -
# Return to previous directory

clear
# Clear terminal
```

Useful shortcuts:

```text
Tab
→ Auto-complete

Tab Tab
→ Show possible completions

Ctrl + L
→ Clear terminal

↑ / ↓
→ Browse command history

Ctrl + R
→ Search command history
```

---

## Key Takeaway

**Linux navigation revolves around knowing where you are (`pwd`), inspecting directory contents (`ls`), and moving between directories (`cd`). Options such as `ls -l` and `ls -la` provide additional information, while `.`, `..`, `cd -`, Tab completion, and command-history shortcuts make shell navigation significantly faster.**
