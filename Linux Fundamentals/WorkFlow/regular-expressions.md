# Regular Expressions

A **Regular Expression (RegEx)** is a search pattern used to find, filter, replace, and manipulate text.

Instead of searching only for exact words, RegEx allows us to describe **patterns**.

For example, instead of searching for one specific string, we can create patterns that represent:

* Multiple possible words
* Character ranges
* Repeated characters
* Words appearing in a specific order
* Alternative patterns

RegEx is supported by many tools and programming languages.

In Linux, two important tools that support RegEx are:

```bash
grep
sed
```

---

# Basic Idea

A normal search looks for literal text.

For example:

```bash
grep "false" /etc/passwd
```

This searches for lines containing:

```text
false
```

RegEx allows us to make the search more flexible.

For example:

```bash
grep -E "(my|false)" /etc/passwd
```

Now we are not searching for only one exact word.

We are searching for:

```text
my
OR
false
```

---

# Metacharacters

Regular expressions use special characters called **metacharacters**.

These characters have a special meaning inside a RegEx pattern instead of necessarily representing themselves literally.

Some important operators introduced in this section are:

| Operator | Purpose                                    |
| -------- | ------------------------------------------ |
| `(a)`    | Group a pattern                            |
| `[a-z]`  | Define a character class/range             |
| `{1,10}` | Define how many times a pattern can repeat |
| `\|`     | OR — match one expression or another       |
| `.*`     | Match characters between patterns          |

---

# Parentheses `()`

Parentheses are used to **group parts of a regular expression**.

Example:

```text
(my|false)
```

Here we create a group containing:

```text
my
```

and:

```text
false
```

The group can then be processed as one part of the RegEx.

---

# Square Brackets `[]`

Square brackets define a **character class**.

Example:

```text
[a-z]
```

This represents characters from:

```text
a
```

through:

```text
z
```

Therefore, `[a-z]` allows us to search for characters belonging to that range instead of one specific character.

---

# Curly Brackets `{}`

Curly brackets define **quantifiers**.

They specify how many times the previous pattern should repeat.

For example:

```text
{1,10}
```

defines a repetition range from:

```text
1
```

to:

```text
10
```

Conceptually:

```text
PATTERN{1,10}
       │
       └── Previous pattern must repeat
           between 1 and 10 times
```

---

# Extended Regular Expressions with `grep -E`

The examples in this section use:

```bash
grep -E
```

The:

```text
-E
```

option enables **Extended Regular Expressions**.

This allows us to use operators such as grouping and OR more conveniently.

General syntax:

```bash
grep -E "REGEX" file
```

---

# OR Operator `|`

The OR operator allows us to match **one pattern or another**.

The basic idea is:

```text
pattern1|pattern2
```

which means:

```text
pattern1
    OR
pattern2
```

The HTB example is:

```bash
cry0l1t3@htb:~$ grep -E "(my|false)" /etc/passwd

lxd:x:105:65534::/var/lib/lxd/:/bin/false
pollinate:x:109:1::/var/cache/pollinate:/bin/false
mysql:x:116:120:MySQL Server,,:/nonexistent:/bin/false
```

The pattern is:

```text
(my|false)
```

Breaking it down:

```text
(
 my
 |
 false
)
```

which means:

> Match a line containing `my` OR `false`.

Therefore, a line does not need to contain both patterns.

If either one matches, `grep` displays the line.

---

# `.*`

The pattern:

```text
.*
```

is very common in RegEx.

In the context of this section, it allows us to search for two expressions appearing in a specific order with characters between them.

For example:

```text
my.*false
```

can be understood as:

```text
my
↓
anything between
↓
false
```

So we want:

```text
my ... false
```

on the same line and in that order.

---

# Searching for Two Patterns

The HTB example uses:

```bash
cry0l1t3@htb:~$ grep -E "(my.*false)" /etc/passwd

mysql:x:116:120:MySQL Server,,:/nonexistent:/bin/false
```

Here the pattern is:

```text
my.*false
```

The line must contain:

```text
my
```

followed later by:

```text
false
```

The matching line is:

```text
mysql:x:116:120:MySQL Server,,:/nonexistent:/bin/false
```

Conceptually:

```text
mysql ... false
^^          ^^^^^
│              │
my             false
```

So:

```text
my.*false
```

means:

> Find `my`, followed by any matching characters, followed by `false`.

---

# OR vs Two Required Patterns

This distinction is important.

## OR

```bash
grep -E "(my|false)" /etc/passwd
```

means:

```text
my
OR
false
```

A line only needs to match one of them.

---

## Both Patterns in Order

```bash
grep -E "(my.*false)" /etc/passwd
```

means:

```text
my
↓
then later
↓
false
```

Both must occur in the matching line in that order.

---

# Achieving the Same Result with Pipes

Instead of using:

```bash
grep -E "(my.*false)" /etc/passwd
```

the material shows that we can also use two `grep` commands:

```bash
cry0l1t3@htb:~$ grep -E "my" /etc/passwd | grep -E "false"

mysql:x:116:120:MySQL Server,,:/nonexistent:/bin/false
```

The first command:

```bash
grep -E "my" /etc/passwd
```

keeps lines containing:

```text
my
```

Then the pipe:

```text
|
```

sends those lines to:

```bash
grep -E "false"
```

which keeps only the lines that also contain:

```text
false
```

The flow is:

```text
/etc/passwd
     │
     ▼
grep "my"
     │
     ▼
Lines containing "my"
     │
     │ |
     ▼
grep "false"
     │
     ▼
Lines also containing "false"
```

---

# Important Distinction: RegEx `|` vs Shell `|`

These look similar but are being used in different contexts.

### Inside the RegEx

```bash
grep -E "(my|false)" /etc/passwd
```

Here:

```text
|
```

belongs to the **regular expression**.

It means:

```text
OR
```

---

### Between Commands

```bash
grep "my" /etc/passwd | grep "false"
```

Here:

```text
|
```

belongs to the **shell**.

It means:

> Send the STDOUT of the first command to the STDIN of the second command.

Therefore:

```text
Inside RegEx:

(my|false)
    ↑
    OR


Between commands:

command1 | command2
         ↑
        PIPE
```

The symbol is the same, but the context changes its meaning.

---

# Practice Patterns

The section suggests practicing with:

```text
/etc/ssh/sshd_config
```

## Lines That Do Not Contain `#`

We can use the inverse option from `grep`:

```bash
grep -v "#" /etc/ssh/sshd_config
```

`-v` excludes matching lines.

Therefore:

```text
grep "#"
→ Lines containing #

grep -v "#"
→ Lines NOT containing #
```

---

# Lines Containing a Word Starting with `Permit`

A pattern beginning with:

```text
Permit
```

can be searched using:

```bash
grep -E "Permit.*" /etc/ssh/sshd_config
```

This looks for `Permit` followed by additional characters.

---

# Words Ending with `Authentication`

We can search for patterns containing characters before:

```text
Authentication
```

For example:

```bash
grep -E ".*Authentication" /etc/ssh/sshd_config
```

---

# Lines Containing `Key`

For a simple literal search:

```bash
grep "Key" /etc/ssh/sshd_config
```

---

# Lines Beginning with `Password` and Containing `yes`

We can search for:

```bash
grep -E "Password.*yes" /etc/ssh/sshd_config
```

Conceptually:

```text
Password
    ↓
 characters
    ↓
   yes
```

---

# Quick Reference

| Pattern   | Meaning                            |
| --------- | ---------------------------------- |
| `(abc)`   | Group a pattern                    |
| `[a-z]`   | Character range                    |
| `{1,10}`  | Repeat previous pattern 1–10 times |
| `a\|b`    | Match `a` OR `b`                   |
| `a.*b`    | Match `a`, followed later by `b`   |
| `grep -E` | Use extended RegEx with grep       |

Examples:

```bash
grep -E "(my|false)" /etc/passwd
```

Match:

```text
my OR false
```

```bash
grep -E "my.*false" /etc/passwd
```

Match:

```text
my ... false
```

```bash
grep -E "my" /etc/passwd | grep -E "false"
```

Keep lines containing both patterns through two filtering stages.

---

# Mental Model

Instead of trying to memorize RegEx as random symbols, we can read patterns piece by piece.

For example:

```text
(my|false)
```

Read as:

> `my` OR `false`.

And:

```text
my.*false
```

Read as:

> `my`, followed later by `false`.

As RegEx becomes more complex, the same principle applies: break the expression into smaller pieces and understand what each part matches.

---

## Key Takeaway

**Regular Expressions allow us to describe patterns instead of searching only for exact text. In this section, the main concepts are grouping with `()`, character classes with `[]`, quantifiers with `{}`, alternatives with `|`, and matching patterns in sequence using `.*`. Combined with tools such as `grep` and `sed`, RegEx gives us much more precise control over text filtering and manipulation.**
