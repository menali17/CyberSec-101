# Conditional Execution

Conditional execution allows us to control the **flow of a Bash script**.

Without conditions, commands would simply execute one after another:

```text
Command 1
   ↓
Command 2
   ↓
Command 3
```

With conditional execution, the script can make decisions:

```text
        Condition
        /       \
      TRUE      FALSE
       │          │
       ▼          ▼
    Code A      Code B
```

Only the code associated with the matching condition is executed. After that section finishes, execution continues with the commands outside the conditional block.

---

# Main Example

The section starts with the argument-checking part of the previous script:

```bash
#!/bin/bash

# Check for given argument
if [ $# -eq 0 ]
then
    echo -e "You need to specify the target domain.\n"
    echo -e "Usage:"
    echo -e "\t$0 <domain>"
    exit 1
else
    domain=$1
fi
```

This section introduces:

```text
#!/bin/bash
→ Shebang

if / else / fi
→ Conditional execution

echo
→ Print output

$# / $0 / $1
→ Special variables

domain
→ Variable

-eq
→ Comparison operator
```

---

# Shebang

The **shebang** appears at the beginning of a script and starts with:

```bash
#!
```

For Bash:

```bash
#!/bin/bash
```

It specifies the interpreter that should process the script:

```text
Script
   │
   ▼
#!/bin/bash
   │
   ▼
/bin/bash
```

Other interpreters can also be specified.

Python:

```python
#!/usr/bin/env python
```

Perl:

```perl
#!/usr/bin/env perl
```

The important idea is:

```text
Shebang
→ identifies the interpreter for the script
```

---

# If-Else-Fi

The basic Bash conditional structure is:

```bash
if [ condition ]
then
    commands
else
    commands
fi
```

We can read it as:

```text
IF condition is true
    THEN execute these commands
ELSE
    execute these commands
FI
```

`fi` marks the end of the `if` statement.

A useful way to remember it is:

```text
if
 ↓
condition
 ↓
then
 ↓
commands
 ↓
fi
```

---

# Understanding the Original Condition

The script contains:

```bash
if [ $# -eq 0 ]
```

Here:

```text
$#
→ number of arguments

-eq
→ equals

0
→ value being compared
```

Therefore:

```bash
if [ $# -eq 0 ]
```

means:

> If the number of arguments is equal to zero.

---

# Special Variables

The example introduces three important special variables.

## `$#`

```bash
$#
```

represents the **number of arguments** passed to the script.

For:

```bash
bash script.sh
```

we have:

```text
$# → 0
```

For:

```bash
bash script.sh inlanefreight.com
```

we have:

```text
$# → 1
```

---

## `$0`

```bash
$0
```

represents the script name.

For example:

```bash
bash script.sh inlanefreight.com
```

conceptually:

```text
$0
→ script.sh
```

This is why the example uses:

```bash
echo -e "\t$0 <domain>"
```

to display how the script should be executed.

---

## `$1`

```bash
$1
```

represents the **first argument**.

For:

```bash
bash script.sh inlanefreight.com
```

we have:

```text
$1
→ inlanefreight.com
```

The script then stores this argument:

```bash
domain=$1
```

Conceptually:

```text
$1
 │
 ▼
inlanefreight.com
 │
 ▼
$domain
```

---

# Original Script Logic

We can now read:

```bash
if [ $# -eq 0 ]
then
    echo -e "You need to specify the target domain.\n"
    echo -e "Usage:"
    echo -e "\t$0 <domain>"
    exit 1
else
    domain=$1
fi
```

as:

```text
How many arguments were supplied?
           │
           ▼
        $# == 0?
        /      \
      YES       NO
       │         │
       ▼         ▼
Show error     domain=$1
and usage
       │
       ▼
    exit 1
```

So if we forget the domain, the script stops.

If we provide one, the first argument becomes the value of `domain`.

---

# If Without Else

An `if` does not necessarily need an `else`.

The material provides:

```bash
#!/bin/bash

value=$1

if [ $value -gt "10" ]
then
    echo "Given argument is greater than 10."
fi
```

Here:

```text
$value
→ supplied argument

-gt
→ greater than

10
→ compared value
```

Therefore:

```bash
if [ $value -gt "10" ]
```

means:

> If `$value` is greater than 10.

---

# Execution

With:

```bash
menali@htb[/htb]$ bash if-only.sh 5
```

there is no output because:

```text
5 > 10
→ FALSE
```

There is no alternative condition, so nothing inside the `if` executes.

With:

```bash
menali@htb[/htb]$ bash if-only.sh 12

Given argument is greater than 10.
```

the condition is true:

```text
12 > 10
→ TRUE
```

so the `echo` command executes.

---

# Elif

`elif` allows us to check another condition if the previous one was false.

General structure:

```bash
if [ condition1 ]
then
    commands
elif [ condition2 ]
then
    commands
else
    commands
fi
```

Mental model:

```text
Condition 1?
   │
   ├── TRUE → Code 1
   │
   └── FALSE
          │
          ▼
      Condition 2?
          │
          ├── TRUE → Code 2
          │
          └── FALSE → Else
```

---

# If-Elif-Else Example

The material provides:

```bash
#!/bin/bash

value=$1

if [ $value -gt "10" ]
then
    echo "Given argument is greater than 10."
elif [ $value -lt "10" ]
then
    echo "Given argument is less than 10."
else
    echo "Given argument is not a number."
fi
```

Here we have:

```text
-gt
→ greater than

-lt
→ less than
```

The comparison operators themselves will be explored further in the next section.

---

# Execution with 5

```bash
menali@htb[/htb]$ bash if-elif-else.sh 5

Given argument is less than 10.
```

Flow:

```text
5 > 10?
→ FALSE

5 < 10?
→ TRUE

Execute:
"Given argument is less than 10."
```

---

# Execution with 12

```bash
menali@htb[/htb]$ bash if-elif-else.sh 12

Given argument is greater than 10.
```

Flow:

```text
12 > 10?
→ TRUE

Execute first condition
→ remaining alternatives skipped
```

Only the first matching branch is executed.

---

# Execution with Text

The material also shows:

```bash
menali@htb[/htb]$ bash if-elif-else.sh HTB

if-elif-else.sh: line 5: [: HTB: integer expression expected
if-elif-else.sh: line 8: [: HTB: integer expression expected
Given argument is not a number.
```

The comparisons in this example expect integer values, but:

```text
HTB
```

is not an integer.

Therefore, Bash reports:

```text
integer expression expected
```

and eventually reaches the `else` branch.

---

# Several Conditions

The original script can be improved to distinguish between:

```text
No arguments
Exactly one argument
Too many arguments
```

The material provides:

```bash
#!/bin/bash

# Check for given argument
if [ $# -eq 0 ]
then
    echo -e "You need to specify the target domain.\n"
    echo -e "Usage:"
    echo -e "\t$0 <domain>"
    exit 1
elif [ $# -eq 1 ]
then
    domain=$1
else
    echo -e "Too many arguments given."
    exit 1
fi
```

---

# Logic of the Improved Version

```text
              $#
               │
               ▼
          Is $# == 0?
           /       \
         YES        NO
          │          │
          ▼          ▼
     Missing     Is $# == 1?
     argument      /     \
                  YES     NO
                   │       │
                   ▼       ▼
              domain=$1   Too many
                          arguments
```

So:

```bash
bash script.sh
```

means:

```text
0 arguments
→ error
```

while:

```bash
bash script.sh inlanefreight.com
```

means:

```text
1 argument
→ domain=$1
```

and something such as:

```bash
bash script.sh one two
```

means:

```text
more than 1 argument
→ "Too many arguments given."
→ exit 1
```

---

# Conditional Execution Mental Model

The core idea is:

```text
             CONDITION
                 │
          ┌──────┴──────┐
          ▼             ▼
        TRUE           FALSE
          │             │
          ▼             ▼
     execute A      check elif
                         │
                    or execute
                       else
```

Conditional execution allows our script to **make decisions instead of blindly executing every command**.

---

# Exercise Script

The section ends with the beginning of an exercise:

```bash
#!/bin/bash
# Count number of characters in a variable:
#     echo $variable | wc -m

# Variable to encode
var="nef892na9s1p9asn2aJs71nIsm"

for counter in {1..40}
do
        var=$(echo $var | base64)
done
```

This script introduces an exercise that repeatedly encodes a variable using Base64.

At this stage, we only need to recognize that:

```text
var
→ contains a value

for
→ repeats an operation

base64
→ encodes the current value
```

The detailed loop behavior belongs to the upcoming loop-related material.

---

# Quick Reference

| Syntax        | Meaning                                   |
| ------------- | ----------------------------------------- |
| `#!/bin/bash` | Use Bash as interpreter                   |
| `if`          | Start conditional                         |
| `then`        | Commands for matching condition           |
| `elif`        | Check another condition                   |
| `else`        | Alternative when previous conditions fail |
| `fi`          | End conditional                           |
| `$#`          | Number of arguments                       |
| `$0`          | Script name                               |
| `$1`          | First argument                            |
| `-eq`         | Equal                                     |
| `-gt`         | Greater than                              |
| `-lt`         | Less than                                 |
| `exit 1`      | Exit script with error status             |

---

# What to Remember First

The fundamental structure is:

```bash
if [ condition ]
then
    commands
else
    commands
fi
```

For multiple possibilities:

```bash
if [ condition1 ]
then
    commands
elif [ condition2 ]
then
    commands
else
    commands
fi
```

And remember these three special variables:

```text
$#
→ How many arguments?

$0
→ What is the script name?

$1
→ What is the first argument?
```

So the original:

```bash
if [ $# -eq 0 ]
```

can immediately be read as:

```text
IF
number of arguments
equals
zero
```

---

## Key Takeaway

**Conditional execution allows our Bash scripts to make decisions. `if` checks a condition, `elif` provides additional conditions, `else` handles the remaining case, and `fi` closes the structure. In the CIDR script, conditional execution is first used to verify how many arguments we supplied before the script continues.**
