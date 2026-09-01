# File Descriptors and Redirections

A **File Descriptor (FD)** is a reference maintained by the Linux kernel to identify an open Input/Output (`I/O`) resource.

These resources can include:

* Files
* Sockets
* Terminal input/output
* Other I/O resources

We can think of a file descriptor as an identifier that tells the operating system which I/O resource a process is interacting with.

---

# Standard File Descriptors

By default, Linux processes have three important file descriptors:

|  FD | Name     | Purpose         |
| --: | -------- | --------------- |
| `0` | `STDIN`  | Standard Input  |
| `1` | `STDOUT` | Standard Output |
| `2` | `STDERR` | Standard Error  |

The most important relationship to remember is:

```text id="t1a6u7"
0 → STDIN  → Input
1 → STDOUT → Normal output
2 → STDERR → Error output
```

Conceptually:

```text id="m2n7w4"
             ┌── STDOUT (1) → Normal result
             │
STDIN (0) → PROGRAM
             │
             └── STDERR (2) → Error message
```

---

# STDIN — Standard Input

`STDIN` represents data being provided **to a program**.

Its file descriptor is:

```text id="p4r8k2"
FD 0
```

For example, if we execute:

```bash id="v9c3s1"
cat
```

`cat` waits for input.

If we type:

```bash id="b5x1q8"
Think Outside The Box
```

that text is provided to `cat` through:

```text id="h6d2m9"
STDIN (0)
```

The program then displays the text back through `STDOUT`.

Conceptually:

```text id="f8j4z3"
Keyboard
   │
   │ STDIN (0)
   ↓
  cat
   │
   │ STDOUT (1)
   ↓
Terminal
```

---

# STDOUT — Standard Output

`STDOUT` represents the normal output produced by a program.

Its file descriptor is:

```text id="k3w7n5"
FD 1
```

For example:

```bash id="q7a2f6"
menali@htb[/htb]$ find /etc/ -name shadow
```

A successful result such as:

```bash id="e1m8r4"
/etc/shadow
```

is sent through:

```text id="u5v9c2"
STDOUT (1)
```

---

# STDERR — Standard Error

`STDERR` is used for error messages.

Its file descriptor is:

```text id="y4t6p1"
FD 2
```

For example, the same `find` command may encounter directories we cannot access:

```bash id="g2k8d5"
menali@htb[/htb]$ find /etc/ -name shadow

/etc/shadow
find: '/etc/ssl/private': Permission denied
```

Here we have two different output streams:

```text id="s9b3x7"
/etc/shadow
     ↓
STDOUT (1)

Permission denied
     ↓
STDERR (2)
```

Even though both appear in the same terminal, they are separate streams.

This distinction is what allows us to redirect them independently.

---

# Redirection

Redirection allows us to change where input and output streams go.

The main symbols introduced in this section are:

| Symbol | Purpose                   |                                |
| ------ | ------------------------- | ------------------------------ |
| `>`    | Redirect output           |                                |
| `>>`   | Append output             |                                |
| `<`    | Redirect input            |                                |
| `<<`   | Provide a stream of input |                                |
| `      | `                         | Send output to another command |

The direction of the symbols can help us remember their purpose:

```text id="j6n1v8"
>  → Output goes somewhere

<  → Input comes from somewhere
```

---

# Redirecting STDERR to `/dev/null`

Suppose we execute:

```bash id="c4p7m2"
find /etc/ -name shadow
```

We may receive both:

```text id="a8q5f3"
STDOUT → /etc/shadow

STDERR → Permission denied
```

We can redirect only the error stream using:

```bash id="r1x9k6"
menali@htb[/htb]$ find /etc/ -name shadow 2>/dev/null

/etc/shadow
```

Breaking down:

```text id="w3d7s4"
2 > /dev/null
│ │      │
│ │      └── Destination
│ │
│ └── Redirect
│
└── STDERR
```

Because:

```text id="n5f2z8"
2 = STDERR
```

we are saying:

> Redirect the error stream to `/dev/null`.

---

# `/dev/null`

`/dev/null` is a special Linux device that discards anything written to it.

Conceptually:

```text id="l7c4q1"
STDERR
   │
   │ 2>
   ↓
/dev/null
   │
   ↓
Discarded
```

Therefore:

```bash id="h9m6b3"
2>/dev/null
```

means:

> Discard STDERR.

This is why errors such as:

```text id="v2k8r5"
Permission denied
```

disappear while valid results remain visible.

---

# Redirect STDOUT to a File

Normal output can also be redirected.

For example:

```bash id="x6p3a9"
menali@htb[/htb]$ find /etc/ -name shadow 2>/dev/null > results.txt
```

Here:

```text id="q4t1n7"
2>/dev/null
→ Send STDERR to /dev/null

> results.txt
→ Send STDOUT to results.txt
```

The terminal does not need to display the normal result because it is written into:

```text id="e8w5c2"
results.txt
```

We can then inspect it:

```bash id="d1r7m4"
menali@htb[/htb]$ cat results.txt

/etc/shadow
```

The flow is:

```text id="o5z2k8"
                     ┌── STDOUT (1) ──→ results.txt
find /etc/ ...
                     │
                     └── STDERR (2) ──→ /dev/null
```

---

# `1>` — Explicit STDOUT Redirection

We learned that:

```text id="b7n3x6"
1 = STDOUT
```

Therefore, we can explicitly write:

```bash id="f4m9q1"
1> stdout.txt
```

to redirect standard output.

However:

```bash id="t6c2v8"
> stdout.txt
```

already means STDOUT by default.

Therefore:

```bash id="y8k5d3"
> stdout.txt
```

and:

```bash id="p2r7w4"
1> stdout.txt
```

both redirect `STDOUT`.

---

# Redirect STDOUT and STDERR Separately

Because STDOUT and STDERR have different file descriptors, we can send them to different files.

```bash id="m1q6f9"
menali@htb[/htb]$ find /etc/ -name shadow 2> stderr.txt 1> stdout.txt
```

Breaking it down:

```text id="z3v8k5"
2> stderr.txt
→ STDERR goes to stderr.txt

1> stdout.txt
→ STDOUT goes to stdout.txt
```

The resulting flow is:

```text id="a7d4n2"
                     ┌── STDOUT (1) ──→ stdout.txt
find /etc/ ...
                     │
                     └── STDERR (2) ──→ stderr.txt
```

This demonstrates that normal results and errors are independent output streams.

---

# Redirect STDIN

The `<` operator redirects input **into a program**.

For example:

```bash id="c9x2m6"
menali@htb[/htb]$ cat < stdout.txt

/etc/shadow
```

Here:

```text id="f5q8r1"
stdout.txt
    │
    │ <
    ↓
STDIN (0)
    │
    ↓
   cat
    │
    ↓
STDOUT (1)
    │
    ↓
Terminal
```

Instead of receiving input from the keyboard, `cat` receives its input from:

```text id="w4n7p3"
stdout.txt
```

---

# `>` vs `>>`

This distinction is extremely important.

## `>` — Overwrite

When we use:

```bash id="r8m1z5"
command > file.txt
```

STDOUT is written to `file.txt`.

If the file does not exist, it is created.

If the file already exists, its previous contents are **overwritten**.

Conceptually:

```text id="k2d6v9"
Before:

file.txt
────────
Old Data


command > file.txt


After:

file.txt
────────
New Data
```

The old content is lost.

---

# `>>` — Append

The double operator:

```text id="q5x9c3"
>>
```

appends data instead of overwriting the file.

Example:

```bash id="u7f4m1"
menali@htb[/htb]$ find /etc/ -name passwd >> stdout.txt 2>/dev/null
```

Here:

```text id="h3r8n6"
>> stdout.txt
→ Add STDOUT to the end of stdout.txt

2>/dev/null
→ Discard errors
```

Conceptually:

```text id="s1k5w7"
Before:

stdout.txt
──────────
/etc/shadow


command >> stdout.txt


After:

stdout.txt
──────────
/etc/shadow
/etc/pam.d/passwd
/etc/cron.daily/passwd
/etc/passwd
```

The existing content remains.

Therefore:

```text id="g9m2q4"
>  → Replace

>> → Append
```

---

# Redirecting an Input Stream with `<<`

The operator:

```text id="v6c1p8"
<<
```

allows us to provide multiple lines of input to a command.

The material demonstrates:

```bash id="x4n7d2"
menali@htb[/htb]$ cat << EOF > stream.txt
```

We can then type:

```bash id="j8q3f5"
Hack The Box
EOF
```

The first:

```text id="l2m9r6"
EOF
```

defines the marker that will indicate where our input ends.

The final:

```text id="a5w1k7"
EOF
```

terminates the input.

The resulting file contains:

```bash id="e7p4v2"
menali@htb[/htb]$ cat stream.txt

Hack The Box
```

Conceptually:

```text id="b3x8n1"
cat << EOF
      │
      ↓
Start receiving input

Hack The Box

EOF
 │
 ↓
Stop receiving input
```

`EOF` means:

```text id="r9c5m4"
End Of File
```

---

# Pipes `|`

A pipe connects the output of one command to the input of another command.

The operator is:

```text id="d1q6z8"
|
```

Conceptually:

```text id="f4n2w7"
COMMAND 1
    │
    │ STDOUT
    ↓
    |
    ↓
COMMAND 2
    │
    ↓
Result
```

Instead of sending the first command's output to the terminal or a file, we send it directly to another program.

---

# Pipe with `grep`

`grep` can filter text based on a pattern.

The HTB example is:

```bash id="m8k3p5"
menali@htb[/htb]$ find /etc/ -name *.conf 2>/dev/null | grep systemd
```

First:

```bash id="y2r7c4"
find /etc/ -name *.conf 2>/dev/null
```

finds `.conf` files while discarding errors.

Its STDOUT is then passed through:

```text id="q6v1n9"
|
```

to:

```bash id="t5x8d3"
grep systemd
```

`grep` keeps only lines containing:

```text id="w7m4k2"
systemd
```

The complete flow is:

```text id="c3p9f6"
find
 │
 │ STDOUT
 ↓
grep systemd
 │
 │ Filter matching lines
 ↓
Terminal
```

So we can read:

```bash id="n1z5q8"
find /etc/ -name *.conf 2>/dev/null | grep systemd
```

as:

> Find `.conf` files, discard errors, and show only results containing `systemd`.

---

# Chaining Multiple Pipes

Pipes can be chained together.

The material uses:

```bash id="k4r8m2"
menali@htb[/htb]$ find /etc/ -name *.conf 2>/dev/null | grep systemd | wc -l

6
```

Let's break it down.

First:

```bash id="e9q3v7"
find /etc/ -name *.conf 2>/dev/null
```

finds `.conf` files.

Then:

```bash id="p2x6n1"
grep systemd
```

keeps only results containing `systemd`.

Finally:

```bash id="a7m5c4"
wc -l
```

counts the number of lines.

The complete flow is:

```text id="h8d1w6"
find
 │
 ↓
All .conf files
 │
 │ |
 ↓
grep systemd
 │
 ↓
Only systemd results
 │
 │ |
 ↓
wc -l
 │
 ↓
6
```

Therefore:

```bash id="u3k9r5"
find /etc/ -name *.conf 2>/dev/null | grep systemd | wc -l
```

means:

> Find `.conf` files → remove errors → keep lines containing `systemd` → count those lines.

---

# Redirection vs Pipes

Both manipulate data flow, but they serve different purposes.

## Redirection

Redirection generally moves data between:

```text id="f6x2q8"
Program ↔ File
```

For example:

```bash id="z5m1n7"
find /etc/ -name shadow > results.txt
```

means:

```text id="r4c8p3"
find
 ↓
results.txt
```

## Pipe

A pipe connects:

```text id="b9w3k6"
Program → Program
```

For example:

```bash id="q1v7d5"
find /etc/ -name "*.conf" | grep systemd
```

means:

```text id="n8m4x2"
find
 ↓
grep
```

A useful mental model is:

```text id="c2p6r9"
>  → Send output to a file

|  → Send output to another program
```

---

# Data Flow Overview

The concepts from this section can be summarized as:

```text id="j7q1m5"
                   PROGRAM
                  /   │   \
                 /    │    \
                /     │     \
        STDIN (0) STDOUT (1) STDERR (2)
           ↑         ↓          ↓
         Input     Normal      Errors
                   Output
```

We can then manipulate those streams:

```text id="v3n8f4"
<  → Provide STDIN

>  → Redirect STDOUT

2> → Redirect STDERR

>> → Append STDOUT

|  → Send STDOUT to another program
```

---

# Quick Reference

```bash id="s5k2w9"
command > output.txt
# Redirect STDOUT to a file (overwrite)

command 1> output.txt
# Explicitly redirect STDOUT

command 2> errors.txt
# Redirect STDERR

command 2>/dev/null
# Discard STDERR

command > output.txt 2>/dev/null
# Save normal output and discard errors

command 1> stdout.txt 2> stderr.txt
# Save STDOUT and STDERR separately

command >> output.txt
# Append STDOUT instead of overwriting

cat < input.txt
# Use a file as STDIN

command1 | command2
# Send STDOUT from command1 to command2

command | grep pattern
# Filter output

command | wc -l
# Count output lines
```

---

# Essential Reference

| Syntax        | Meaning                        |
| ------------- | ------------------------------ |
| `0`           | STDIN                          |
| `1`           | STDOUT                         |
| `2`           | STDERR                         |
| `<`           | Redirect STDIN                 |
| `>`           | Redirect STDOUT and overwrite  |
| `1>`          | Explicitly redirect STDOUT     |
| `2>`          | Redirect STDERR                |
| `>>`          | Append STDOUT                  |
| `2>/dev/null` | Discard errors                 |
| `<< EOF`      | Provide multiline input        |
| `\|`          | Pipe STDOUT to another command |
| `grep`        | Filter lines by pattern        |
| `wc -l`       | Count lines                    |

---

## Key Takeaway

**Linux commands work with separate input and output streams. `STDIN (0)` provides input to a process, `STDOUT (1)` carries normal results, and `STDERR (2)` carries errors. Redirections such as `>`, `>>`, `<`, and `2>` allow us to control where these streams go, while pipes (`|`) allow us to send the output of one command directly into another command for further processing.**
