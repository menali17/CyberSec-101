# Comparison Operators

Comparison operators allow Bash scripts to **compare values and make decisions based on the result**.

They are commonly used inside conditions such as:

```bash
if [ condition ]
then
    # commands
fi
```

or:

```bash
if [[ condition ]]
then
    # commands
fi
```

The main categories introduced in this section are:

* String operators
* Integer operators
* File operators
* Boolean and logical operators

---

# String Operators

String operators compare text values.

| Operator | Meaning                                  |
| -------- | ---------------------------------------- |
| `==`     | Equal to                                 |
| `!=`     | Not equal to                             |
| `<`      | Less than in ASCII alphabetical order    |
| `>`      | Greater than in ASCII alphabetical order |
| `-z`     | String is empty                          |
| `-n`     | String is not empty                      |

For example:

```bash
#!/bin/bash

if [ "$1" != "HackTheBox" ]
then
    echo -e "You need to give 'HackTheBox' as argument."
    exit 1

elif [ $# -gt 1 ]
then
    echo -e "Too many arguments given."
    exit 1

else
    domain=$1
    echo -e "Success!"
fi
```

The first condition is:

```bash
[ "$1" != "HackTheBox" ]
```

This means:

> Check whether the first argument is **different from** `HackTheBox`.

If we execute:

```bash
./script.sh Google
```

then:

```text
$1 → Google
```

and Bash effectively evaluates:

```text
"Google" != "HackTheBox"
```

The condition is true, so we receive:

```bash
You need to give 'HackTheBox' as argument.
```

But if we execute:

```bash
./script.sh HackTheBox
```

the first comparison becomes false because:

```text
"HackTheBox" == "HackTheBox"
```

The script can continue to the next condition.

---

## Quoting Strings

The material places variables inside double quotes:

```bash
"$1"
```

Therefore, string comparisons commonly appear as:

```bash
[ "$1" == "HackTheBox" ]
```

or:

```bash
[ "$1" != "HackTheBox" ]
```

Quoting variable expansions is especially important when using `[ ... ]`, because an empty value or values containing spaces can otherwise change how the expression is parsed.

---

# Empty and Non-Empty Strings

We can test whether a string contains anything.

### `-z`

```bash
-z "$1"
```

means:

> Is this string empty?

For example:

```bash
if [ -z "$1" ]
then
    echo "No argument was provided."
fi
```

If we execute:

```bash
./script.sh
```

then `$1` is empty and the condition is true.

---

### `-n`

```bash
-n "$1"
```

means:

> Is this string non-empty?

For example:

```bash
if [ -n "$1" ]
then
    echo "An argument was provided."
fi
```

---

# String Ordering

The operators:

```text
<
>
```

can compare strings according to their lexical/ASCII ordering.

The material uses them inside:

```bash
[[ condition ]]
```

For example:

```bash
[[ "$value1" < "$value2" ]]
```

The ASCII table can be consulted with:

```bash
menali@htb[/htb]$ man ascii
```

ASCII assigns numeric values to characters.

For example:

| Decimal | Hexadecimal | Character |
| ------: | ----------: | --------- |
|      65 |          41 | `A`       |
|      66 |          42 | `B`       |
|      67 |          43 | `C`       |
|      68 |          44 | `D`       |

ASCII stands for:

**American Standard Code for Information Interchange**

It is a 7-bit character encoding containing 128 possible values:

```text
0 → 127
```

---

# Integer Operators

Integer operators compare numerical values.

| Operator | Meaning                  |
| -------- | ------------------------ |
| `-eq`    | Equal to                 |
| `-ne`    | Not equal to             |
| `-lt`    | Less than                |
| `-le`    | Less than or equal to    |
| `-gt`    | Greater than             |
| `-ge`    | Greater than or equal to |

These are especially important in Bash scripts.

---

## `-eq`

```bash
[ "$number" -eq 10 ]
```

means:

> Is `number` equal to 10?

---

## `-ne`

```bash
[ "$number" -ne 10 ]
```

means:

> Is `number` different from 10?

---

## `-lt`

```bash
[ "$number" -lt 10 ]
```

means:

> Is `number` less than 10?

---

## `-le`

```bash
[ "$number" -le 10 ]
```

means:

> Is `number` less than or equal to 10?

---

## `-gt`

```bash
[ "$number" -gt 10 ]
```

means:

> Is `number` greater than 10?

---

## `-ge`

```bash
[ "$number" -ge 10 ]
```

means:

> Is `number` greater than or equal to 10?

---

# Integer Comparison Example

The material uses:

```bash
#!/bin/bash

if [ $# -lt 1 ]
then
    echo -e "Number of given arguments is less than 1"
    exit 1

elif [ $# -gt 1 ]
then
    echo -e "Number of given arguments is greater than 1"
    exit 1

else
    domain=$1
    echo -e "Number of given arguments equals 1"
fi
```

Remember:

```text
$# → number of arguments
```

The first condition:

```bash
[ $# -lt 1 ]
```

means:

> Is the number of arguments less than 1?

The second:

```bash
[ $# -gt 1 ]
```

means:

> Is the number of arguments greater than 1?

Therefore:

```text
0 arguments
     ↓
$# -lt 1
     ↓
TRUE
```

```text
2+ arguments
     ↓
$# -gt 1
     ↓
TRUE
```

If neither condition is true, there must be exactly one argument:

```text
$# = 1
```

and the `else` block executes.

---

# File Operators

File operators allow us to test properties of files and directories.

| Operator | Meaning                                  |
| -------- | ---------------------------------------- |
| `-e`     | File/path exists                         |
| `-f`     | Is a regular file                        |
| `-d`     | Is a directory                           |
| `-L`     | Is a symbolic link                       |
| `-N`     | Modified since it was last read          |
| `-O`     | Current user owns the file               |
| `-G`     | File group ID matches the current user's |
| `-s`     | File has a size greater than zero        |
| `-r`     | File is readable                         |
| `-w`     | File is writable                         |
| `-x`     | File is executable                       |

These allow scripts to verify something about a file **before attempting to work with it**.

---

# Checking if a File Exists

The material provides:

```bash
#!/bin/bash

if [ -e "$1" ]
then
    echo -e "The file exists."
    exit 0

else
    echo -e "The file does not exist."
    exit 2
fi
```

The important condition is:

```bash
[ -e "$1" ]
```

which means:

> Does the path supplied as the first argument exist?

For example:

```bash
./script.sh /etc/passwd
```

makes:

```text
$1 → /etc/passwd
```

and the condition checks:

```bash
[ -e "/etc/passwd" ]
```

If it exists:

```bash
The file exists.
```

Otherwise:

```bash
The file does not exist.
```

---

# Common File Tests

We can mentally read these expressions as questions.

```bash
[ -e "$file" ]
```

> Does it exist?

```bash
[ -f "$file" ]
```

> Is it a regular file?

```bash
[ -d "$file" ]
```

> Is it a directory?

```bash
[ -r "$file" ]
```

> Can we read it?

```bash
[ -w "$file" ]
```

> Can we write to it?

```bash
[ -x "$file" ]
```

> Can we execute it?

---

# Boolean Conditions

A condition ultimately evaluates as either:

```text
TRUE
```

or:

```text
FALSE
```

The material demonstrates this with:

```bash
#!/bin/bash

if [[ -z $1 ]]
then
    echo -e "Boolean value: True (is null)"
    exit 1

elif [[ $# > 1 ]]
then
    echo -e "Boolean value: True (is greater than)"
    exit 1

else
    domain=$1
    echo -e "Boolean value: False (is equal to)"
fi
```

The first condition:

```bash
[[ -z $1 ]]
```

checks whether `$1` is empty.

So:

```text
No first argument
       ↓
$1 is empty
       ↓
-z $1
       ↓
TRUE
```

---

# Logical Operators

Logical operators allow us to combine multiple conditions.

| Operator | Meaning |
| -------- | ------- |
| `!`      | NOT     |
| `&&`     | AND     |
| `\|\|`   | OR      |

---

# NOT — `!`

The NOT operator reverses a condition.

For example:

```bash
[[ ! -e "$1" ]]
```

Without `!`:

```bash
-e "$1"
```

means:

> The specified path exists.

Adding `!`:

```bash
! -e "$1"
```

means:

> The specified path does **not** exist.

Mental model:

```text
TRUE  → ! → FALSE
FALSE → ! → TRUE
```

---

# AND — `&&`

AND requires **both conditions to be true**.

For example:

```bash
[[ -e "$1" && -r "$1" ]]
```

means:

> The specified path exists **AND** we can read it.

Conceptually:

```text
Exists?     Readable?      Result

TRUE        TRUE       →   TRUE
TRUE        FALSE      →   FALSE
FALSE       TRUE       →   FALSE
FALSE       FALSE      →   FALSE
```

Both must be true.

---

# OR — `||`

OR requires **at least one condition to be true**.

Conceptually:

```text
Condition A    Condition B    Result

TRUE           TRUE       →   TRUE
TRUE           FALSE      →   TRUE
FALSE          TRUE       →   TRUE
FALSE          FALSE      →   FALSE
```

So:

```text
AND → both must be true

OR  → at least one must be true
```

---

# Combining File Conditions

The material provides:

```bash
#!/bin/bash

if [[ -e "$1" && -r "$1" ]]
then
    echo -e "We can read the file that has been specified."
    exit 0

elif [[ ! -e "$1" ]]
then
    echo -e "The specified file does not exist."
    exit 2

elif [[ -e "$1" && ! -r "$1" ]]
then
    echo -e "We don't have read permission for this file."
    exit 1

else
    echo -e "Error occured."
    exit 5
fi
```

Let's break down each condition.

### First condition

```bash
[[ -e "$1" && -r "$1" ]]
```

means:

```text
File exists
    AND
File is readable
```

If both are true:

```bash
We can read the file that has been specified.
```

---

### Second condition

```bash
[[ ! -e "$1" ]]
```

means:

```text
NOT exists
```

or simply:

> The specified path does not exist.

---

### Third condition

```bash
[[ -e "$1" && ! -r "$1" ]]
```

means:

```text
File exists
    AND
NOT readable
```

Therefore:

> The path exists, but we do not have read permission.

---

# `[ ]` vs `[[ ]]`

In this section we encounter both:

```bash
[ condition ]
```

and:

```bash
[[ condition ]]
```

For now, the important point is that both are used to evaluate conditions in the examples.

The material particularly uses `[[ ... ]]` when combining logical conditions:

```bash
[[ -e "$1" && -r "$1" ]]
```

and for string ordering with:

```text
<
>
```

We do not need to go deeper into their implementation yet.

---

# Reading Conditions Naturally

A very useful way to learn these operators is to translate the condition into English.

For example:

```bash
[ $# -eq 1 ]
```

becomes:

> Is the number of arguments equal to one?

---

```bash
[ "$1" != "HackTheBox" ]
```

becomes:

> Is the first argument different from HackTheBox?

---

```bash
[ -e "$1" ]
```

becomes:

> Does the specified path exist?

---

```bash
[[ -e "$1" && -r "$1" ]]
```

becomes:

> Does the specified path exist AND can we read it?

---

```bash
[[ ! -e "$1" ]]
```

becomes:

> Does the specified path NOT exist?

This makes Bash conditions much easier to understand than trying to memorize the symbols without context.

---


# Quick Reference

### Strings

| Operator | Meaning   |
| -------- | --------- |
| `==`     | Equal     |
| `!=`     | Not equal |
| `-z`     | Empty     |
| `-n`     | Not empty |

### Integers

| Operator | Meaning |
| -------- | ------- |
| `-eq`    | `=`     |
| `-ne`    | `≠`     |
| `-lt`    | `<`     |
| `-le`    | `≤`     |
| `-gt`    | `>`     |
| `-ge`    | `≥`     |

An easy way to remember the integer operators:

```text
eq → equal
ne → not equal
lt → less than
le → less/equal
gt → greater than
ge → greater/equal
```

### Files

| Operator | Meaning                |
| -------- | ---------------------- |
| `-e`     | Exists                 |
| `-f`     | Regular file           |
| `-d`     | Directory              |
| `-L`     | Symbolic link          |
| `-s`     | Size greater than zero |
| `-r`     | Readable               |
| `-w`     | Writable               |
| `-x`     | Executable             |

### Logic

```text
!   → NOT

&&  → AND

||  → OR
```

---

## Key Takeaway

**Comparison operators allow our Bash scripts to make decisions based on strings, numbers, files, and multiple combined conditions. For integers, operators such as `-eq`, `-lt`, and `-gt` perform numerical comparisons; file operators such as `-e` and `-r` test file properties; and logical operators such as `!`, `&&`, and `||` allow us to negate or combine conditions. The easiest way to understand a Bash condition is to translate it into a simple question before reading the code that follows it.**
