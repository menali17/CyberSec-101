# Find Files and Directories

Finding files and directories efficiently is essential when working with Linux systems.

After gaining access to a system, we may need to locate:

* Configuration files
* User-created scripts
* Administrator files
* Installed programs
* Specific directories

Instead of manually navigating through the entire filesystem, Linux provides tools that allow us to search efficiently.

The main commands introduced in this section are:

| Command    | Purpose                               |
| ---------- | ------------------------------------- |
| `which`    | Find the executable path of a command |
| `find`     | Search the filesystem using filters   |
| `locate`   | Quickly search using a local database |
| `updatedb` | Update the database used by `locate`  |

---

# `which` — Find Executables

The `which` command returns the path to the executable file or link associated with a command.

It is useful for determining whether specific programs are available on the system.

For example, we may want to check for:

```text
curl
netcat
wget
python
gcc
```

To locate Python:

```bash
menali@htb[/htb]$ which python

/usr/bin/python
```

This tells us that the executable associated with `python` is located at:

```text
/usr/bin/python
```

If the requested program cannot be found, `which` displays no result.

---

# Why `which` Is Useful

During system enumeration, we may want to know which tools are already installed.

For example:

```bash
which python
which gcc
which wget
which curl
```

Possible results:

```bash
/usr/bin/python
/usr/bin/gcc
/usr/bin/wget
/usr/bin/curl
```

This allows us to quickly determine whether a particular command is available.

---

# `find` — Search Files and Directories

The `find` command searches for files and directories in the filesystem.

Unlike simpler search tools, `find` supports many filters.

We can search based on characteristics such as:

* Name
* Object type
* Owner
* Size
* Modification date

## Syntax

```bash
find <location> <options>
```

The first argument determines **where the search begins**.

For example:

```bash
find / <options>
```

starts searching from:

```text
/
```

which is the root of the filesystem.

---

# `find` Example

The HTB material provides the following advanced example:

```bash
menali@htb[/htb]$ find / -type f -name *.conf -user root -size +20k -newermt 2020-03-03 -exec ls -al {} \; 2>/dev/null

-rw-r--r-- 1 root root 136392 Apr 25 20:29 /usr/src/linux-headers-5.5.0-1parrot1-amd64/include/config/auto.conf
-rw-r--r-- 1 root root 82290 Apr 25 20:29 /usr/src/linux-headers-5.5.0-1parrot1-amd64/include/config/tristate.conf
-rw-r--r-- 1 root root 95813 May  7 14:33 /usr/share/metasploit-framework/data/jtr/repeats32.conf
-rw-r--r-- 1 root root 60346 May  7 14:33 /usr/share/metasploit-framework/data/jtr/dynamic.conf
-rw-r--r-- 1 root root 96249 May  7 14:33 /usr/share/metasploit-framework/data/jtr/dumb32.conf
-rw-r--r-- 1 root root 54755 May  7 14:33 /usr/share/metasploit-framework/data/jtr/repeats16.conf
-rw-r--r-- 1 root root 22635 May  7 14:33 /usr/share/metasploit-framework/data/jtr/korelogic.conf
-rwxr-xr-x 1 root root 108534 May  7 14:33 /usr/share/metasploit-framework/data/jtr/john.conf
-rw-r--r-- 1 root root 55285 May  7 14:33 /usr/share/metasploit-framework/data/jtr/dumb16.conf
-rw-r--r-- 1 root root 21254 May  2 11:59 /usr/share/doc/sqlmap/examples/sqlmap.conf
-rw-r--r-- 1 root root 25086 Mar  4 22:04 /etc/dnsmasq.conf
-rw-r--r-- 1 root root 21254 May  2 11:59 /etc/sqlmap/sqlmap.conf
```

At first, this command looks complicated, but it is simply a sequence of filters:

```text
find /
  ↓
-type f
  ↓
-name *.conf
  ↓
-user root
  ↓
-size +20k
  ↓
-newermt 2020-03-03
  ↓
-exec ls -al {} \;
  ↓
2>/dev/null
```

Each part progressively restricts or processes the results.

---

# Search Location

The first part:

```bash
find /
```

tells `find` where to begin searching.

Here:

```text
/
```

means the root of the filesystem.

Therefore, the search can traverse the entire filesystem.

General structure:

```bash
find <starting_location>
```

---

# `-type f`

The option:

```bash
-type f
```

filters results by object type.

Here:

```text
f → file
```

Therefore:

```bash
find / -type f
```

searches for files starting from `/`.

---

# `-name`

The option:

```bash
-name *.conf
```

filters results by name.

Here:

```text
*.conf
```

means files whose names end with:

```text
.conf
```

The asterisk:

```text
*
```

acts as a wildcard.

Conceptually:

```text
*.conf
   ↓
Anything ending in .conf
```

Examples that match:

```text
dnsmasq.conf
sqlmap.conf
NetworkManager.conf
```

---

# `-user`

The option:

```bash
-user root
```

filters files based on their owner.

Therefore:

```bash
find / -user root
```

searches for objects owned by:

```text
root
```

In the complete example, only `.conf` files owned by root continue to match.

---

# `-size`

We can filter results based on file size.

The example uses:

```bash
-size +20k
```

Here:

```text
+20k
```

means files larger than approximately:

```text
20 KiB
```

Therefore:

```bash
find / -size +20k
```

filters out smaller files.

---

# `-newermt`

The option:

```bash
-newermt 2020-03-03
```

filters files according to their modification date.

Only files newer than:

```text
2020-03-03
```

are included.

Therefore, this filter helps us locate files modified after a specific date.

---

# `-exec`

One of the most powerful `find` options is:

```bash
-exec
```

It allows us to execute another command for each result found.

The example uses:

```bash
-exec ls -al {} \;
```

Here:

```text
-exec
```

starts the command that should be executed.

```text
ls -al
```

is the command being executed.

```text
{}
```

acts as a placeholder for each result returned by `find`.

```text
\;
```

marks the end of the `-exec` command.

Conceptually:

```text
find result
     ↓
     {}
     ↓
ls -al <result>
```

If `find` discovers:

```text
/etc/dnsmasq.conf
```

then `{}` represents that path when the command is executed.

---

# Why `\;` Is Used

The semicolon:

```text
;
```

normally has meaning to the shell.

The backslash:

```text
\
```

escapes it so that it reaches `find` instead of being interpreted by the shell.

Therefore:

```text
\;
```

indicates the end of the command passed to `-exec`.

---

# `2>/dev/null`

The final part of the example is:

```bash
2>/dev/null
```

This is **not an option of `find`**.

It is a shell redirection.

It redirects:

```text
STDERR
```

to:

```text
/dev/null
```

The material introduces this here but covers redirection in more detail later.

For now, the important idea is:

```text
2>/dev/null
      ↓
Hide error messages
```

This is particularly useful when searching through the entire filesystem because we may encounter directories that we do not have permission to access.

Instead of displaying those errors, they are sent to `/dev/null`.

---

# Understanding the Complete `find` Command

We can now read:

```bash
find / -type f -name *.conf -user root -size +20k -newermt 2020-03-03 -exec ls -al {} \; 2>/dev/null
```

as:

```text
Search from /
        ↓
Only files
        ↓
Names ending in .conf
        ↓
Owned by root
        ↓
Larger than 20 KiB
        ↓
Modified after 2020-03-03
        ↓
Run ls -al on every result
        ↓
Hide error messages
```

This demonstrates why `find` is powerful: several filters can be combined into a single search.

---

# `locate` — Fast File Search

Searching the entire filesystem with `find` can take time.

The `locate` command provides a faster alternative.

The major difference is that `locate` does **not search the filesystem directly every time**.

Instead, it searches through a local database containing information about files and directories.

Conceptually:

```text
find
 ↓
Search filesystem directly


locate
 ↓
Search local database
```

This makes `locate` significantly faster for many searches.

---

# Updating the `locate` Database

Because `locate` relies on a database, that database may need to be updated.

We can update it using:

```bash
menali@htb[/htb]$ sudo updatedb
```

After the database has been updated, `locate` can search its contents.

---

# Searching with `locate`

For example, we can search for files ending in:

```text
.conf
```

using:

```bash
menali@htb[/htb]$ locate *.conf

/etc/GeoIP.conf
/etc/NetworkManager/NetworkManager.conf
/etc/UPower/UPower.conf
/etc/adduser.conf
<SNIP>
```

Because `locate` searches a database instead of traversing the filesystem directly, the results are generally returned much faster.

---

# `find` vs `locate`

The choice between `find` and `locate` depends on what we need.

| Feature                     | `find`              | `locate`                 |
| --------------------------- | ------------------- | ------------------------ |
| Search method               | Searches filesystem | Searches database        |
| Speed                       | Generally slower    | Generally faster         |
| Filtering                   | Extensive           | More limited             |
| Search by owner             | Yes                 | Limited                  |
| Search by size              | Yes                 | Limited                  |
| Search by date              | Yes                 | Limited                  |
| Execute commands on results | Yes                 | No equivalent shown here |
| Requires database           | No                  | Yes                      |
| Database update             | Not required        | `updatedb`               |

The important trade-off is:

```text
find
→ More powerful filtering

locate
→ Faster searching
```

---

# Choosing the Right Tool

If we simply want to quickly locate something by name, `locate` can be useful.

For example:

```bash
locate *.conf
```

If we need specific filters, `find` is more appropriate.

For example:

```bash
find / -type f -name *.conf -user root
```

If we want to determine whether a particular executable is available, `which` is more appropriate.

For example:

```bash
which python
```

Therefore:

```text
Need executable path?
        ↓
      which

Need advanced filesystem search?
        ↓
      find

Need fast filename search?
        ↓
      locate
```

---

# Quick Reference

```bash
which python
# Find the executable path for Python

find <location> <options>
# Search files and directories

find / -type f
# Search for files starting from /

find / -type f -name *.conf
# Search for .conf files

find / -type f -user root
# Search for files owned by root

find / -type f -size +20k
# Search for files larger than 20 KiB

find / -type f -newermt 2020-03-03
# Search for files modified after the specified date

find / -type f -exec ls -al {} \;
# Run ls -al on every result

2>/dev/null
# Redirect error output to /dev/null

sudo updatedb
# Update the locate database

locate *.conf
# Quickly locate .conf files using the database
```

---

# Command Summary

| Command / Option  | Purpose                         |
| ----------------- | ------------------------------- |
| `which <command>` | Find executable path            |
| `find <location>` | Start a filesystem search       |
| `-type f`         | Search for files                |
| `-name`           | Filter by name                  |
| `*`               | Wildcard                        |
| `-user`           | Filter by owner                 |
| `-size`           | Filter by size                  |
| `-newermt`        | Filter by modification date     |
| `-exec`           | Execute a command on results    |
| `{}`              | Placeholder for a `find` result |
| `\;`              | End the `-exec` command         |
| `2>/dev/null`     | Hide error output               |
| `locate`          | Search the file database        |
| `updatedb`        | Update the `locate` database    |

---

## Key Takeaway

**Linux provides different tools depending on what we need to find. `which` locates the executable associated with a command, `find` searches the filesystem directly and supports powerful filters such as name, type, owner, size, and modification date, while `locate` provides faster searches by querying a local database. When we need precise and flexible searches, `find` is generally the more powerful option; when speed is more important and simple filename matching is sufficient, `locate` can be more convenient.**
