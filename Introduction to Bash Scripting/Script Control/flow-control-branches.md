# Flow Control - Branches

Flow control determines **which path our script follows** depending on conditions or values.

The main control structures introduced so far are:

```text
Flow Control
│
├── Branches
│   ├── If-Else
│   └── Case
│
└── Loops
    ├── For
    ├── While
    └── Until
```

We already studied `if-else`, so this section focuses on **case statements**.

---

# Case Statements

A `case` statement allows our script to select an action depending on the value of an expression.

In other programming languages, this concept is often called:

```text
switch-case
```

For example, in C we may encounter a `switch`, while Bash uses:

```bash
case
```

The basic structure is:

```bash
case <expression> in
    pattern_1 ) statements ;;
    pattern_2 ) statements ;;
    pattern_3 ) statements ;;
esac
```

The important structure is:

```text
case
 ↓
value to check
 ↓
compare against patterns
 ↓
execute matching commands
 ↓
esac
```

Notice that:

```text
case
```

is closed with:

```text
esac
```

which is simply `case` written backwards.

This is similar to:

```text
if → fi
```

from our previous sections.

---

# How `case` Works

Suppose WE have:

```bash
option=2
```

Then:

```bash
case $option in
    "1") echo "Option 1" ;;
    "2") echo "Option 2" ;;
    "3") echo "Option 3" ;;
esac
```

Bash compares:

```text
$option
   ↓
   2
```

against the available patterns:

```text
"1" → No

"2" → Yes
       ↓
       execute:
       echo "Option 2"

"3" → not needed
```

The result is:

```bash
Option 2
```

---

# Case Syntax

A case statement follows this general pattern:

```bash
case $variable in

    pattern1)
        commands
        ;;

    pattern2)
        commands
        ;;

    pattern3)
        commands
        ;;

esac
```

Each section contains:

```text
pattern
   ↓
)
   ↓
commands
   ↓
;;
```

For example:

```bash
"1") echo "Selected option 1" ;;
```

means:

> If the value matches `"1"`, execute `echo "Selected option 1"`.

---

# The Double Semicolon `;;`

Each case branch normally ends with:

```bash
;;
```

For example:

```bash
"1") network_range ;;
```

This separates that branch from the next one.

Conceptually:

```text
"1")
   ↓
network_range
   ↓
;;
   ↓
this branch is finished
```

---

# If-Else vs Case

Both structures allow our script to make decisions, but they are useful in different situations.

An `if-else` statement can evaluate conditions such as:

```bash
if [ $number -gt 10 ]
```

or:

```bash
if [[ -e "$file" && -r "$file" ]]
```

These involve conditions such as:

```text
greater than
file exists
file is readable
multiple logical conditions
```

A `case` statement instead chooses a branch according to the value being matched against its patterns.

For the type of menu shown in this section:

```text
1 → perform action A
2 → perform action B
3 → perform action C
```

`case` provides a clear structure.

---

# Simple Example

Consider:

```bash
read -p "Select an option: " option

case $option in
    "1") echo "Scan selected" ;;
    "2") echo "Enumeration selected" ;;
    "3") echo "Exit selected" ;;
esac
```

If WE enter:

```bash
2
```

then:

```text
option = 2
```

Bash matches:

```text
1 ? No
2 ? Yes
```

and executes:

```bash
echo "Enumeration selected"
```

Output:

```bash
Enumeration selected
```

---

# Case Statement in CIDR.sh

The material uses:

```bash
echo -e "Additional options available:"
echo -e "\t1) Identify the corresponding network range of target domain."
echo -e "\t2) Ping discovered hosts."
echo -e "\t3) All checks."
echo -e "\t*) Exit.\n"

read -p "Select your option: " opt

case $opt in
    "1") network_range ;;
    "2") ping_host ;;
    "3") network_range && ping_host ;;
    "*") exit 0 ;;
esac
```

There are two main stages:

```text
1. read
   ↓
Receive our selection

2. case
   ↓
Decide what to execute
```

---

# Receiving the Option

From the previous section, WE know:

```bash
read -p "Select your option: " opt
```

displays:

```bash
Select your option:
```

and stores our input inside:

```bash
opt
```

If WE enter:

```bash
2
```

then:

```text
opt = 2
```

The script then reaches:

```bash
case $opt in
```

and compares that value against the available options.

---

# Option 1

The first branch is:

```bash
"1") network_range ;;
```

If:

```text
opt = 1
```

the script executes:

```bash
network_range
```

This is a function defined elsewhere in the script.

Its purpose is to identify the corresponding network range.

Flow:

```text
Input: 1
   ↓
$opt = 1
   ↓
case
   ↓
"1" matches
   ↓
network_range
```

---

# Option 2

The second branch is:

```bash
"2") ping_host ;;
```

If WE enter:

```bash
2
```

the script executes:

```bash
ping_host
```

Flow:

```text
Input: 2
   ↓
$opt = 2
   ↓
"2" matches
   ↓
ping_host
```

---

# Option 3

The third branch is:

```bash
"3") network_range && ping_host ;;
```

This executes:

```bash
network_range
```

and then, if that succeeds:

```bash
ping_host
```

So:

```text
Input: 3
   ↓
network_range
   ↓
success?
   ↓
ping_host
```

This combines the first two available actions.

---

# Other Input

The final branch is intended to terminate the script for other selections:

```bash
"*") exit 0 ;;
```

The material presents this as the exit option.

Conceptually:

```text
1 → network_range

2 → ping_host

3 → network_range + ping_host

other → exit
```

---

# Complete CIDR.sh Decision Flow

We can visualize the menu as:

```text
          Script displays menu
                  │
                  ▼
        "Select your option:"
                  │
                  ▼
             read → opt
                  │
                  ▼
             case $opt
                  │
       ┌──────────┼──────────┬──────────┐
       │          │          │          │
       ▼          ▼          ▼          ▼
      "1"        "2"        "3"       other
       │          │          │          │
       ▼          ▼          ▼          ▼
   network_     ping_     network_     exit
    range       host       range
                            &&
                          ping_host
```

---

# Why Case Is Useful for Menus

Suppose WE used several `if-else` branches:

```bash
if [ "$opt" == "1" ]
then
    network_range

elif [ "$opt" == "2" ]
then
    ping_host

elif [ "$opt" == "3" ]
then
    network_range && ping_host
fi
```

This works conceptually, but when WE are simply selecting an action based on one value, `case` can make the structure easier to read:

```bash
case $opt in
    "1") network_range ;;
    "2") ping_host ;;
    "3") network_range && ping_host ;;
esac
```

The relationship is immediately visible:

```text
Value → Action
```

---

# Reading a Case Statement

Whenever WE encounter something like:

```bash
case $opt in
    "1") command1 ;;
    "2") command2 ;;
    "3") command3 ;;
esac
```

WE can mentally translate it to:

```text
Look at $opt.

If it matches 1:
    run command1

If it matches 2:
    run command2

If it matches 3:
    run command3
```

That is the core idea of this section.

---

# Connection with Previous Sections

The complete menu combines several concepts WE have already studied:

```bash
read -p "Select your option: " opt
```

uses **Input Control**.

Then:

```bash
case $opt in
```

uses **Flow Control**.

And:

```bash
"3") network_range && ping_host ;;
```

uses the logical execution operator:

```text
&&
```

So the flow becomes:

```text
INPUT
  ↓
read
  ↓
VARIABLE
  ↓
opt
  ↓
BRANCH
  ↓
case
  ↓
ACTION
```

---

# Quick Reference

Basic syntax:

```bash
case $variable in
    pattern1) commands ;;
    pattern2) commands ;;
    pattern3) commands ;;
esac
```

Important components:

| Syntax      | Meaning                   |
| ----------- | ------------------------- |
| `case`      | Starts the case statement |
| `$variable` | Value being evaluated     |
| `pattern)`  | Value/pattern to match    |
| `;;`        | Ends the current branch   |
| `esac`      | Ends the case statement   |

Example:

```bash
case $opt in
    "1") network_range ;;
    "2") ping_host ;;
    "3") network_range && ping_host ;;
esac
```

Mental model:

```text
CASE

"What is the value?"

      ↓

Match it with an option

      ↓

Execute the corresponding commands
```

---

## Key Takeaway

**A `case` statement allows our Bash script to choose between different actions based on the value being evaluated. It is especially useful for menus and situations where one value can correspond to several predefined actions. A case statement begins with `case`, contains patterns followed by their commands and `;;`, and ends with `esac`.**
