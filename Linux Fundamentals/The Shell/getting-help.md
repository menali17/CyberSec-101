# Getting Help

When working with Linux, you will often encounter commands or tools whose options you do not remember.

Instead of memorizing every parameter, it is important to know how to quickly find documentation and usage information.

The main ways to get help are:

* `man`
* `--help`
* `-h`
* `apropos`

---

# `ls`

The `ls` command lists files and directories.

```bash
menali@htb[/htb]$ ls

cacert.der  Documents  Music     Public     Videos
Desktop     Downloads  Pictures  Templates
```

---

# `man`

The `man` command displays the **manual page** of a command.

## Syntax

```bash
menali@htb[/htb]$ man <tool>
```

## Example

```bash
menali@htb[/htb]$ man ls
```

Output:

```bash
LS(1)                            User Commands                           LS(1)

NAME
       ls - list directory contents

SYNOPSIS
       ls [OPTION]... [FILE]...

DESCRIPTION
       List information about the FILEs
       (the current directory by default).

       -a, --all
              do not ignore entries starting with .

       -A, --almost-all
              do not list implied . and ..

       --author
              with -l, print the author of each file

Manual page ls(1) line 1
(press h for help or q to quit)
```

Useful keys inside a man page:

```bash
h    # Help
q    # Quit
```

---

# `--help`

Many Linux commands provide quick usage information using:

```bash
menali@htb[/htb]$ <tool> --help
```

Example:

```bash
menali@htb[/htb]$ ls --help
```

Output:

```bash
Usage: ls [OPTION]... [FILE]...

List information about the FILEs
(the current directory by default).

  -a, --all
        do not ignore entries starting with .

  -A, --almost-all
        do not list implied . and ..

      --author
        with -l, print the author of each file

  -b, --escape
        print C-style escapes for nongraphic characters

  -B, --ignore-backups
        do not list implied entries ending with ~

  -C
        list entries by columns
```

`--help` is useful when you need a quick overview of the available options without reading the complete manual.

---

# `-h`

Some tools provide a shorter help option using:

```bash
menali@htb[/htb]$ <tool> -h
```

Example with `curl`:

```bash
menali@htb[/htb]$ curl -h
```

Output:

```bash
Usage: curl [options...] <url>

     --abstract-unix-socket <path>
            Connect via abstract Unix domain socket

     --anyauth
            Pick any authentication method

 -a, --append
            Append to target file when uploading

     --basic
            Use HTTP Basic Authentication

     --cacert <file>
            CA certificate to verify peer against

     --capath <dir>
            CA directory to verify peer against

 -E, --cert <certificate[:password]>
            Client certificate file and password
```

Depending on the tool, `-h` may provide either the same information as `--help` or a shorter version.

---

# `apropos`

The `apropos` command searches manual page descriptions using a keyword.

## Syntax

```bash
menali@htb[/htb]$ apropos <keyword>
```

## Example

```bash
menali@htb[/htb]$ apropos sudo
```

Output:

```bash
sudo (8)             - execute a command as another user
sudo.conf (5)        - configuration for sudo front end
sudo_plugin (8)      - Sudo Plugin API
sudo_root (8)        - How to run administrative commands
sudoedit (8)         - execute a command as another user
sudoers (5)          - default sudo security policy plugin
sudoreplay (8)       - replay sudo session logs
visudo (8)           - edit the sudoers file
```

This is useful when you know **what you want to do**, but do not know the exact command.

---

# `man` vs `--help` vs `apropos`

| Method              | Purpose                        |
| ------------------- | ------------------------------ |
| `man <tool>`        | Detailed command documentation |
| `<tool> --help`     | Quick syntax and options       |
| `<tool> -h`         | Short help for some tools      |
| `apropos <keyword>` | Search commands by description |

A useful workflow is:

```text
Know the command?
      |
      +-- Yes → --help
      |           ↓
      |          man
      |
      +-- No → apropos
```

---

# External Help

**ExplainShell** can be useful when trying to understand long or complex shell commands.

It helps explain:

* Commands
* Options
* Pipes
* Redirections
* Complex command combinations

---

# Quick Reference

```bash
ls
# List files and directories

man ls
# Open the manual page for ls

ls --help
# Display quick help for ls

curl -h
# Display help for curl

apropos sudo
# Search manual descriptions containing "sudo"
```

---

## Key Takeaway

**We do not need to memorize every Linux command or option. Use `man` for detailed documentation, `--help` or `-h` for quick usage information, and `apropos` when you know the task but not the exact command.**
