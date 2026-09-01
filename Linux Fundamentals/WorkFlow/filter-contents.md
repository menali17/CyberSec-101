# Filter Contents

Linux provides several command-line tools for reading, filtering, transforming, sorting, and processing text.

These tools become especially powerful when combined using **pipes (`|`)**, allowing us to send the output of one command directly into another.

A common workflow looks like:

```text id="g4q8t2"
Command
   ↓
Filter
   ↓
Transform
   ↓
Extract
   ↓
Count
```

---

# More

`more` is a **pager** used to view large amounts of text one screen at a time.

Example:

```bash id="h7m2p5"
menali@htb[/htb]$ cat /etc/passwd | more
```

Output:

```bash id="k3v9c1"
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
<SNIP>
--More--
```

Here:

```text id="x6r1n8"
cat /etc/passwd
        │
        │ STDOUT
        ↓
       more
```

`more` allows us to navigate through output that does not fit on one screen.

We can quit with:

```text id="b9f4w2"
Q
```

When we exit `more`, the displayed output remains visible in the terminal.

---

# Less

`less` is another pager, but it provides more functionality than `more`.

Example:

```bash id="z2k7d4"
menali@htb[/htb]$ less /etc/passwd
```

Output:

```bash id="c5p1m9"
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
<SNIP>
:
```

We can exit using:

```text id="f8q3v6"
Q
```

Unlike `more`, after we exit `less`, the content we viewed does not remain displayed in the terminal.

### More vs Less

| Tool   | Purpose                             |
| ------ | ----------------------------------- |
| `more` | Basic interactive pager             |
| `less` | More feature-rich interactive pager |

Both are useful when we need to inspect large files without opening them in an editor.

---

# Head

`head` displays the **beginning of a file or input**.

By default, it displays the first **10 lines**.

```bash id="n1w6r3"
menali@htb[/htb]$ head /etc/passwd
```

Output:

```bash id="t4m8x2"
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
```

Conceptually:

```text id="q7c2k5"
Large File
│
├── line 1   ←
├── line 2   ←
├── ...      ← head
├── line 10  ←
├── line 11
├── line 12
└── ...
```

---

# Tail

`tail` is the counterpart of `head`.

Instead of displaying the beginning, it displays the **end of a file or input**.

By default, it displays the last **10 lines**.

```bash id="d9v4p1"
menali@htb[/htb]$ tail /etc/passwd
```

Output:

```bash id="m2x8f6"
miredo:x:115:65534::/var/run/miredo:/usr/sbin/nologin
usbmux:x:116:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin
rtkit:x:117:119:RealtimeKit,,,:/proc:/usr/sbin/nologin
nm-openvpn:x:118:120:NetworkManager OpenVPN,,,:/var/lib/openvpn/chroot:/usr/sbin/nologin
nm-openconnect:x:119:121:NetworkManager OpenConnect plugin,,,:/var/lib/NetworkManager:/bin/bash
pulse:x:120:122:PulseAudio daemon,,,:/var/run/pulse:/usr/sbin/nologin
beef-xss:x:121:124::/var/lib/beef-xss:/usr/sbin/nologin
lightdm:x:122:125:Light Display Manager:/var/lib/lightdm:/bin/false
do-agent:x:998:998::/home/do-agent:/bin/false
user6:x:1000:1000:,,,:/home/user6:/bin/bash
```

Conceptually:

```text id="s5n1w7"
Large File
│
├── line 1
├── line 2
├── ...
├── line 91  ←
├── line 92  ←
├── ...      ← tail
└── line 100 ←
```

Therefore:

```text id="v3r8q4"
head → beginning

tail → end
```

---

# Sort

`sort` organizes text output.

It is commonly used to sort results alphabetically or numerically.

Example:

```bash id="p6k2c9"
menali@htb[/htb]$ cat /etc/passwd | sort
```

Output begins:

```bash id="a1m7x5"
_apt:x:104:65534::/nonexistent:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
cry0l1t3:x:1001:1001::/home/cry0l1t3:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
dnsmasq:x:107:65534:dnsmasq,,,:/var/lib/misc:/usr/sbin/nologin
```

The flow is:

```text id="e4q9f2"
/etc/passwd
     ↓
    cat
     ↓
    sort
     ↓
Alphabetically sorted output
```

---

# Grep

`grep` searches text for lines matching a **pattern**.

This is one of the most important filtering tools in Linux.

For example, `/etc/passwd` contains information about system users.

If we want only users whose shell is `/bin/bash`:

```bash id="j8c3n6"
menali@htb[/htb]$ cat /etc/passwd | grep "/bin/bash"
```

Output:

```bash id="r5w1p7"
root:x:0:0:root:/root:/bin/bash
mrb3n:x:1000:1000:mrb3n:/home/mrb3n:/bin/bash
cry0l1t3:x:1001:1001::/home/cry0l1t3:/bin/bash
htb-student:x:1002:1002::/home/htb-student:/bin/bash
```

Conceptually:

```text id="x2m6k9"
All lines
    ↓
grep "/bin/bash"
    ↓
Only matching lines
```

---

# Grep with `-v`

Normally, `grep` keeps matching lines.

The option:

```text id="b7q4d1"
-v
```

does the opposite.

It **excludes matching lines**.

Example:

```bash id="f9n2c5"
menali@htb[/htb]$ cat /etc/passwd | grep -v "false\|nologin"
```

Output:

```bash id="u3m8r6"
root:x:0:0:root:/root:/bin/bash
sync:x:4:65534:sync:/bin:/bin/sync
postgres:x:111:117:PostgreSQL administrator,,,:/var/lib/postgresql:/bin/bash
user6:x:1000:1000:,,,:/home/user6:/bin/bash
```

Here we are excluding lines containing:

```text id="k5x1p8"
false
OR
nologin
```

Therefore:

```text id="q9d4v2"
grep pattern
→ Keep matching lines

grep -v pattern
→ Remove matching lines
```

---

# Cut

`cut` extracts specific fields from structured text.

This is especially useful when values are separated by a consistent **delimiter**.

For example, `/etc/passwd` uses:

```text id="c1m7w5"
:
```

as its delimiter.

A line looks like:

```text id="h8q2f4"
htb-student:x:1002:1002::/home/htb-student:/bin/bash
```

Separated into fields:

```text id="n6p3k9"
htb-student : x : 1002 : 1002 : ... : /home/htb-student : /bin/bash
     1        2     3      4                 6                7
```

We can extract the first field:

```bash id="z4r8c2"
menali@htb[/htb]$ cat /etc/passwd | grep -v "false\|nologin" | cut -d":" -f1
```

Output:

```bash id="v7m1q6"
root
sync
postgres
mrb3n
cry0l1t3
htb-student
```

Breaking down:

```text id="f2k9x5"
-d":"
→ Delimiter is :

-f1
→ Select field 1
```

Therefore:

```bash id="a5n3p8"
cut -d":" -f1
```

means:

> Split each line using `:` and return the first field.

---

# Tr

`tr` is used to **translate or replace characters**.

Example:

```bash id="w1q6m4"
menali@htb[/htb]$ cat /etc/passwd | grep -v "false\|nologin" | tr ":" " "
```

Output:

```bash id="d8p2k7"
root x 0 0 root /root /bin/bash
sync x 4 65534 sync /bin /bin/sync
postgres x 111 117 PostgreSQL administrator,,, /var/lib/postgresql /bin/bash
mrb3n x 1000 1000 mrb3n /home/mrb3n /bin/bash
cry0l1t3 x 1001 1001 /home/cry0l1t3 /bin/bash
htb-student x 1002 1002 /home/htb-student /bin/bash
```

Here:

```bash id="y5c9r3"
tr ":" " "
```

means:

> Replace every `:` with a space.

Conceptually:

```text id="t3n7v1"
Before:

root:x:0:0:root:/root:/bin/bash

            ↓ tr ":" " "

After:

root x 0 0 root /root /bin/bash
```

---

# Column

`column` helps display text in a more readable **tabular format**.

The HTB example uses:

```bash id="k8m4q2"
column -t
```

The option:

```text id="r1p6x9"
-t
```

formats the input as a table.

Example:

```bash id="c7w2n5"
menali@htb[/htb]$ cat /etc/passwd | grep -v "false\|nologin" | tr ":" " " | column -t
```

Output:

```bash id="f4q9m1"
root         x  0     0      root               /root                /bin/bash
sync         x  4     65534  sync               /bin                 /bin/sync
postgres     x  111   117    PostgreSQL         administrator,,,    /var/lib/postgresql  /bin/bash
mrb3n        x  1000  1000   mrb3n              /home/mrb3n          /bin/bash
htb-student  x  1002  1002   /home/htb-student  /bin/bash
```

The purpose is mainly **readability**.

---

# Awk

`awk` is a powerful text-processing tool.

In this section, we use it to extract specific columns from each line.

Example:

```bash id="m9x3p6"
menali@htb[/htb]$ cat /etc/passwd | grep -v "false\|nologin" | tr ":" " " | awk '{print $1, $NF}'
```

Output:

```bash id="q2k7v4"
root /bin/bash
sync /bin/sync
postgres /bin/bash
mrb3n /bin/bash
cry0l1t3 /bin/bash
htb-student /bin/bash
```

The important part is:

```bash id="n5r1c8"
awk '{print $1, $NF}'
```

Here:

```text id="z8m4w2"
$1
→ First field

$NF
→ Last field

print
→ Display them
```

So:

```bash id="b3q9f5"
awk '{print $1, $NF}'
```

means:

> For every line, print the first field and the last field.

---

# Sed

`sed` is a **stream editor**.

It can modify text as it passes through a command pipeline.

One common use is **substitution**.

Example:

```bash id="x6p2m7"
sed 's/bin/HTB/g'
```

Breaking down:

```text id="d1q8k4"
s
│
└── substitute

bin
│
└── pattern we want to replace

HTB
│
└── replacement

g
│
└── replace all matches
```

The general structure is:

```bash id="v9c5r3"
sed 's/OLD/NEW/g'
```

For example:

```text id="h4m7n1"
/bin/bash
```

becomes:

```text id="p2x8q6"
/HTB/bash
```

The complete HTB example is:

```bash id="k7w3f9"
menali@htb[/htb]$ cat /etc/passwd | grep -v "false\|nologin" | tr ":" " " | awk '{print $1, $NF}' | sed 's/bin/HTB/g'
```

Output:

```bash id="a1m6v4"
root /HTB/bash
sync /HTB/sync
postgres /HTB/bash
mrb3n /HTB/bash
cry0l1t3 /HTB/bash
htb-student /HTB/bash
```

---

# Wc

`wc` is used to count data.

In this section, the important option is:

```text id="r5q2k8"
-l
```

which counts **lines**.

Example:

```bash id="f8n4c1"
menali@htb[/htb]$ cat /etc/passwd | grep -v "false\|nologin" | tr ":" " " | awk '{print $1, $NF}' | wc -l

6
```

The final:

```bash id="z3p7m5"
wc -l
```

means:

> Count how many lines were produced.

This is especially useful for counting search results.

For example:

```bash id="q6x1w9"
find / -type f -name "*.log" 2>/dev/null | wc -l
```

counts how many `.log` files were found.

---

# Understanding the Complete Pipeline

One of the most important skills in this section is learning to read pipelines from **left to right**.

Consider:

```bash id="m4k8r2"
cat /etc/passwd | grep -v "false\|nologin" | tr ":" " " | awk '{print $1, $NF}' | wc -l
```

We can break it down as:

```text id="c9q3n6"
cat /etc/passwd
        │
        ▼
Read /etc/passwd
        │
        │
        ▼
grep -v "false\|nologin"
        │
        ▼
Remove unwanted lines
        │
        │
        ▼
tr ":" " "
        │
        ▼
Replace : with spaces
        │
        │
        ▼
awk '{print $1, $NF}'
        │
        ▼
Keep first + last fields
        │
        │
        ▼
wc -l
        │
        ▼
Count the results
```

Instead of trying to understand the entire command at once, we can analyze each stage independently.

---

# Quick Reference

| Command   | Main Purpose                       |
| --------- | ---------------------------------- |
| `more`    | View long output page by page      |
| `less`    | Interactively navigate long text   |
| `head`    | Show the first lines               |
| `tail`    | Show the last lines                |
| `sort`    | Sort lines                         |
| `grep`    | Keep lines matching a pattern      |
| `grep -v` | Exclude lines matching a pattern   |
| `cut`     | Extract fields                     |
| `tr`      | Replace/translate characters       |
| `column`  | Format output as a table           |
| `awk`     | Process fields and structured text |
| `sed`     | Transform/substitute text          |
| `wc -l`   | Count lines                        |

---

# Commands to Remember First

The most important commands from this section to become comfortable with are:

```bash id="t7m2p5"
less file.txt
```

```bash id="w9q4c1"
head file.txt
```

```bash id="k3x8n6"
tail file.txt
```

```bash id="f5r1m7"
grep "pattern" file.txt
```

```bash id="p2v9q4"
grep -v "pattern" file.txt
```

```bash id="a8m3k5"
sort file.txt
```

```bash id="c1q7w9"
cut -d":" -f1 file.txt
```

```bash id="n6x2r4"
awk '{print $1}'
```

```bash id="v4m8p3"
sed 's/OLD/NEW/g'
```

```bash id="q9k5c2"
wc -l file.txt
```

And especially, we should become comfortable combining commands:

```bash id="d7r1n6"
command | grep "pattern" | wc -l
```

---

## Key Takeaway

**Linux text processing is based on combining small tools. We can read data with commands such as `cat`, inspect it with `more`, `less`, `head`, or `tail`, filter it with `grep`, extract fields with `cut` or `awk`, transform it with `tr` or `sed`, sort it with `sort`, and count results with `wc`. Pipes (`|`) allow us to connect these tools into powerful processing pipelines.**
