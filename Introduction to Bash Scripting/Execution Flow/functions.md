# Functions

As our Bash scripts become larger, repeating the same commands multiple times makes them harder to read and maintain.

**Functions** solve this problem by grouping commands into a reusable block.

Instead of repeating:

```bash
command1
command2
command3
```

several times, WE can define them once:

```bash
function example {
    command1
    command2
    command3
}
```

and execute the entire block simply by calling:

```bash
example
```

---

# Why Functions Are Useful

Functions help us:

* Avoid repeating code.
* Keep scripts shorter.
* Improve readability.
* Organize different parts of a script.
* Execute the same routine with different values.

The basic idea is:

```text
Repeated commands
       ↓
Put them inside a function
       ↓
Give the function a name
       ↓
Call that name whenever needed
```

---

# Defining Functions

Bash provides two common ways to define functions.

## Method 1

```bash
function name {
    <commands>
}
```

Example:

```bash
function hello {
    echo "Hello"
}
```

---

## Method 2

```bash
name() {
    <commands>
}
```

Example:

```bash
hello() {
    echo "Hello"
}
```

Both methods define a function called:

```bash
hello
```

and WE can execute it with:

```bash
hello
```

The HTB material uses the first method because the `function` keyword makes the purpose of the block easy to recognize.

---

# Function Definition vs Function Call

It is important to distinguish these two concepts.

This:

```bash
function hello {
    echo "Hello"
}
```

**defines** the function.

It tells Bash:

> Whenever WE call `hello`, execute these commands.

It does not execute the function yet.

To execute it, WE write:

```bash
hello
```

So:

```text
FUNCTION DEFINITION
        ↓
Store a reusable block of commands

FUNCTION CALL
        ↓
Execute that block
```

---

# Function Order

Bash scripts are processed from top to bottom.

Therefore, according to the structure used in this material, functions should be defined **before they are first called**.

For example:

```bash
#!/bin/bash

function hello {
    echo "Hello"
}

hello
```

Flow:

```text
Define hello
     ↓
Call hello
     ↓
echo "Hello"
```

Output:

```bash
Hello
```

---

# Functions in CIDR.sh

The `CIDR.sh` script contains the function:

```bash
function network_range {
    for ip in $ipaddr
    do
        netrange=$(whois $ip | grep "NetRange\|CIDR" | tee -a CIDR.txt)
        cidr=$(whois $ip | grep "CIDR" | awk '{print $2}')
        cidr_ips=$(prips $cidr)
        echo -e "\nNetRange for $ip:"
        echo -e "$netrange"
    done
}
```

Everything between:

```bash
{
```

and:

```bash
}
```

belongs to the function.

The function is named:

```bash
network_range
```

Its general purpose is to process IP addresses and identify their corresponding network ranges.

---

# Calling the Function

Later, the `CIDR.sh` script contains:

```bash
case $opt in
    "1") network_range ;;
    "2") ping_host ;;
    "3") network_range && ping_host ;;
    "*") exit 0 ;;
esac
```

Notice:

```bash
"1") network_range ;;
```

There are no parentheses or braces when WE call the function.

WE simply use its name:

```bash
network_range
```

The script then executes all commands defined inside:

```bash
function network_range {
    ...
}
```

So the relationship is:

```text
function network_range {
    commands
}

          ↓

network_range

          ↓

Execute commands
```

---

# Functions and Reusability

Suppose WE needed these commands several times:

```bash
whoami
hostname
ip addr
```

Instead of repeating them:

```bash
whoami
hostname
ip addr

...

whoami
hostname
ip addr

...

whoami
hostname
ip addr
```

WE could create:

```bash
function system_info {
    whoami
    hostname
    ip addr
}
```

Then call:

```bash
system_info
```

whenever WE need those commands.

This is the main reason functions are useful.

---

# Parameter Passing

Functions can also receive **arguments**.

This works similarly to the arguments WE previously passed to Bash scripts.

Inside a function:

```text
$1 → first argument
$2 → second argument
$3 → third argument
...
$9 → ninth argument
```

The number of arguments can be obtained with:

```bash
$#
```

---

# Function Parameters Example

The material provides:

```bash
#!/bin/bash

function print_pars {
    echo $1 $2 $3
}

one="First parameter"
two="Second parameter"
three="Third parameter"

print_pars "$one" "$two" "$three"
```

First, three variables are created:

```bash
one="First parameter"
two="Second parameter"
three="Third parameter"
```

Then the function is called:

```bash
print_pars "$one" "$two" "$three"
```

The values become the function's positional parameters:

```text
$1 = "First parameter"

$2 = "Second parameter"

$3 = "Third parameter"
```

Inside the function:

```bash
echo $1 $2 $3
```

therefore prints:

```bash
First parameter Second parameter Third parameter
```

---

# Script Arguments vs Function Arguments

This distinction is important.

Suppose WE call:

```bash
print_pars "$one" "$two" "$three"
```

Inside `print_pars`:

```text
$1
$2
$3
```

refer to the arguments passed specifically to that function.

Conceptually:

```text
Function call:

print_pars "A" "B" "C"
              │   │   │
              │   │   └── $3
              │   └────── $2
              └────────── $1
```

Each function has its own positional parameters.

---

# Variables and Scope

The material points out an important Bash behavior:

> Variables are generally global unless WE explicitly declare them as `local`.

For example:

```bash
function example {
    name="HTB"
}
```

After the function executes, `name` can still be available outside the function.

To explicitly create a variable local to a function, Bash provides:

```bash
local
```

For example:

```bash
function example {
    local name="HTB"
}
```

For this section, the important distinction is:

```text
Normal variable
→ generally global

local variable
→ limited to the function
```

---

# Return Values

Functions can also communicate whether their execution succeeded or failed.

Bash uses an **exit/return status code**.

The usual convention is:

```text
0     → success
non-0 → some kind of failure/error
```

For example:

```bash
return 0
```

indicates success.

While:

```bash
return 1
```

indicates failure.

---

# Common Return Codes

The material provides the following codes:

| Return Code | Description                    |
| ----------: | ------------------------------ |
|         `1` | General errors                 |
|         `2` | Misuse of shell builtins       |
|       `126` | Command invoked cannot execute |
|       `127` | Command not found              |
|       `128` | Invalid argument to `exit`     |
|     `128+n` | Fatal error signal `n`         |
|       `130` | Script terminated with Ctrl+C  |
|      `255*` | Exit status out of range       |

For now, the most important distinction is:

```text
0 = success

non-zero = failure/error
```

---

# `$?` — Reading the Status Code

The special variable:

```bash
$?
```

contains the status code of the **previously executed command or function**.

For example:

```bash
given_args
echo $?
```

If `given_args` returns:

```bash
return 1
```

then:

```bash
echo $?
```

prints:

```bash
1
```

---

# Return.sh

The material uses:

```bash
#!/bin/bash

function given_args {

        if [ $# -lt 1 ]
        then
                echo -e "Number of arguments: $#"
                return 1
        else
                echo -e "Number of arguments: $#"
                return 0
        fi
}

# No arguments given
given_args
echo -e "Function status code: $?\n"

# One argument given
given_args "argument"
echo -e "Function status code: $?\n"

# Pass the results of the function into a variable
content=$(given_args "argument")

echo -e "Content of the variable: \n\t$content"
```

This example combines several concepts WE have already studied.

---

# Understanding `given_args`

The function starts with:

```bash
function given_args {
```

Then it checks:

```bash
if [ $# -lt 1 ]
```

Remember:

```bash
$#
```

means:

> Number of arguments received.

And:

```bash
-lt
```

means:

> Less than.

Therefore:

```bash
[ $# -lt 1 ]
```

asks:

> Did the function receive fewer than one argument?

---

# No Arguments

The script first calls:

```bash
given_args
```

No arguments are provided.

Therefore:

```text
$# = 0
```

The condition:

```bash
[ $# -lt 1 ]
```

becomes conceptually:

```bash
[ 0 -lt 1 ]
```

which is true.

The function executes:

```bash
echo -e "Number of arguments: $#"
return 1
```

Output:

```bash
Number of arguments: 0
```

Then:

```bash
echo -e "Function status code: $?\n"
```

reads the function's return status.

Since the function executed:

```bash
return 1
```

the output is:

```bash
Function status code: 1
```

---

# One Argument

Next:

```bash
given_args "argument"
```

Now:

```text
$1 = "argument"

$# = 1
```

The condition:

```bash
[ $# -lt 1 ]
```

becomes:

```bash
[ 1 -lt 1 ]
```

which is false.

Therefore, the `else` block executes:

```bash
echo -e "Number of arguments: $#"
return 0
```

Output:

```bash
Number of arguments: 1
```

And:

```bash
$?
```

contains:

```bash
0
```

So WE get:

```bash
Function status code: 0
```

---

# Capturing Function Output

The final example introduces another important distinction.

The script executes:

```bash
content=$(given_args "argument")
```

We previously learned that:

```bash
$(...)
```

is **command substitution**.

It captures the standard output produced by the command inside it.

The function executes:

```bash
echo -e "Number of arguments: $#"
```

which produces:

```bash
Number of arguments: 1
```

Because the function is inside:

```bash
$(...)
```

that output is captured and assigned to:

```bash
content
```

So:

```text
given_args "argument"
        ↓
echo produces:
"Number of arguments: 1"
        ↓
$(...)
captures it
        ↓
content="Number of arguments: 1"
```

Finally:

```bash
echo -e "Content of the variable: \n\t$content"
```

produces:

```bash
Content of the variable:
    Number of arguments: 1
```

---

# `return` vs `echo`

This example demonstrates an important distinction.

### `return`

```bash
return 0
```

provides a **status code**.

WE can inspect it with:

```bash
$?
```

---

### `echo`

```bash
echo "Some value"
```

produces output.

WE can capture that output with:

```bash
result=$(function_name)
```

Therefore:

```text
return
   ↓
status code
   ↓
$?


echo
   ↓
standard output
   ↓
$(...)
   ↓
variable
```

This distinction is especially important in Bash.

---

# Complete Return.sh Flow

The entire example can be visualized as:

```text
given_args
(no arguments)
      ↓
$# = 0
      ↓
return 1
      ↓
$? = 1


given_args "argument"
      ↓
$# = 1
      ↓
return 0
      ↓
$? = 0


content=$(given_args "argument")
      ↓
function prints:
"Number of arguments: 1"
      ↓
$(...) captures output
      ↓
content="Number of arguments: 1"
```

---

# Connection with Other Bash Concepts

Functions bring together many concepts WE have already studied:

```text
Variables
    +
Arguments
    +
If-Else
    +
Loops
    +
Commands
    +
Return Codes
    ↓
Functions
```

For example:

```bash
function check_host {
    if ping -c 1 "$1" > /dev/null 2>&1
    then
        echo "$1 is up"
        return 0
    else
        echo "$1 is down"
        return 1
    fi
}
```

A function can therefore contain the same Bash structures WE already know.

---

# Quick Reference

### Define a function — Method 1

```bash
function name {
    commands
}
```

### Define a function — Method 2

```bash
name() {
    commands
}
```

### Call a function

```bash
name
```

### Pass arguments

```bash
name "argument1" "argument2"
```

Inside the function:

```bash
$1
$2
```

### Number of arguments

```bash
$#
```

### Return success

```bash
return 0
```

### Return failure

```bash
return 1
```

### Read the previous status code

```bash
$?
```

### Capture function output

```bash
result=$(function_name)
```

### Local variable

```bash
local variable="value"
```

---

# Core Mental Model

```text
FUNCTION
"Store commands under a name"

CALL
"Execute those commands"

ARGUMENTS
"Give values to the function"

$1, $2, $3...
"Access those values"

$#
"How many arguments?"

return
"Report success/failure"

$?
"What status did it return?"

$(function)
"Capture what the function printed"
```

---

## Key Takeaway

**Functions allow us to group commands into reusable blocks, reducing duplicated code and making our Bash scripts easier to organize. Functions can receive arguments through `$1`, `$2`, and other positional parameters, report execution status with `return`, and have their printed output captured through command substitution `$()`.**
