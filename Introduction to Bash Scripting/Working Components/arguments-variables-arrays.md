# Arguments, Variables, and Arrays

Bash scripts can receive information through **arguments**, store information in **variables**, and organize multiple values using **arrays**.

A useful mental model is:

```text
Arguments
    ↓
Information enters the script
    ↓
Variables
    ↓
Information is stored and used
    ↓
Arrays
    ↓
Multiple values can be organized together
```

---

# Arguments

Arguments are values we provide when executing a script.

For example:

```bash
menali@htb[/htb]$ ./script.sh ARG1 ARG2 ARG3
```

Bash automatically makes these arguments available through **special variables**.

Conceptually:

```text
./script.sh ARG1 ARG2 ARG3
     │       │    │    │
     ▼       ▼    ▼    ▼
    $0      $1   $2   $3
```

So:

```text
$0 → script name
$1 → first argument
$2 → second argument
$3 → third argument
...
$9 → ninth argument
```

The material presents positional arguments from `$0` through `$9`, with `$0` reserved for the script itself.

---

# Arguments in CIDR.sh

The previous `CIDR.sh` example contains:

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

If we execute:

```bash
./cidr.sh inlanefreight.com
```

we can think of it as:

```text
$0 → ./cidr.sh

$1 → inlanefreight.com

$# → 1
```

The script then performs:

```bash
domain=$1
```

Therefore:

```text
$1
 │
 ▼
inlanefreight.com
 │
 ▼
domain
```

---

# Execution Permissions

Before directly executing a script, the material gives it execution permission:

```bash
menali@htb[/htb]$ chmod +x cidr.sh
```

Then we can execute:

```bash
menali@htb[/htb]$ ./cidr.sh
```

Output:

```bash
You need to specify the target domain.

Usage:
    cidr.sh <domain>
```

Since no domain was supplied:

```text
$# = 0
```

and the conditional from the previous section displays the error.

---

# Running with Bash

The material also executes:

```bash
menali@htb[/htb]$ bash cidr.sh

You need to specify the target domain.

Usage:
    cidr.sh <domain>
```

Here we explicitly invoke Bash to interpret the script.

The distinction to remember is:

```bash
./cidr.sh
```

executes the script directly and therefore requires appropriate execution permission.

While:

```bash
bash cidr.sh
```

explicitly asks Bash to read and execute the script.

---

# Special Variables

Bash provides special variables that give us useful information about arguments, processes, and command execution.

| Special Variable | Meaning                                       |
| ---------------- | --------------------------------------------- |
| `$#`             | Number of arguments                           |
| `$@`             | List of command-line arguments                |
| `$n`             | Argument at position `n`, such as `$1`        |
| `$$`             | Process ID of the currently executing process |
| `$?`             | Exit status of the previous command           |

---

## `$#` — Number of Arguments

```bash
$#
```

contains the number of arguments supplied.

For:

```bash
./cidr.sh inlanefreight.com
```

we have:

```text
$# → 1
```

This is why the previous script can check:

```bash
if [ $# -eq 0 ]
```

which means:

> If the number of arguments equals zero.

---

## `$@` — All Arguments

```bash
$@
```

represents the list of command-line arguments.

If we execute:

```bash
./script.sh one two three
```

`$@` represents the supplied arguments:

```text
one two three
```

This becomes useful when a script needs to work with several arguments.

---

## `$n` — Specific Argument

We can retrieve individual arguments according to their position.

```text
$1 → first argument
$2 → second argument
$3 → third argument
...
```

For example:

```bash
./script.sh google.com 443
```

gives us:

```text
$1 → google.com
$2 → 443
```

---

## `$$` — Process ID

```bash
$$
```

contains the Process ID (**PID**) associated with the currently executing shell process.

For example:

```bash
echo $$
```

could produce a numeric PID such as:

```bash
12345
```

The exact number depends on the running process.

---

## `$?` — Exit Status

We previously encountered:

```bash
$?
```

It contains the exit status of the previously executed command.

The basic interpretation introduced by the material is:

```text
0 → successful execution

non-zero → failure/error
```

For example:

```bash
ping -c 2 $host > /dev/null 2>&1

if [ $? -eq 0 ]
```

checks whether the previous `ping` command succeeded.

---

# Special Variables in CIDR.sh

The conditional from `CIDR.sh` mainly uses:

```text
$#
$0
$1
```

Their roles are:

```text
$#
→ How many arguments did we receive?

$0
→ What script is being executed?

$1
→ What is the first argument?
```

For:

```bash
./cidr.sh inlanefreight.com
```

we can visualize:

```text
./cidr.sh    inlanefreight.com
    │                │
    ▼                ▼
   $0               $1

$# = 1
```

---

# Variables

Variables allow us to store values that can later be used by the script.

In `CIDR.sh`:

```bash
domain=$1
```

we create a variable named:

```text
domain
```

and assign the value of `$1` to it.

If:

```text
$1 = inlanefreight.com
```

then:

```text
domain = inlanefreight.com
```

---

# Assigning vs Using Variables

This distinction is very important in Bash.

When **assigning** a variable:

```bash
domain=$1
```

we do not place `$` before `domain`.

When **using its value**:

```bash
echo $domain
```

we use `$`.

Mental model:

```text
domain="value"
      ↑
   assignment


$domain
   ↑
retrieve/use the value
```

---

# No Spaces During Assignment

Bash variable assignments must not contain spaces around `=`.

This is incorrect:

```bash
menali@htb[/htb]$ variable = "this will result with an error."

command not found: variable
```

Bash interprets `variable` as if it were a command.

The correct syntax is:

```bash
menali@htb[/htb]$ variable="Declared without an error."
menali@htb[/htb]$ echo $variable

Declared without an error.
```

Therefore:

```text
WRONG

variable = "value"
```

```text
CORRECT

variable="value"
```

This is one of the most important Bash syntax rules to remember.

---

# Variable Values

The material explains that Bash does not directly distinguish variable types in the same way as many other programming languages.

We can assign:

```bash
name="HTB"
```

or:

```bash
number=10
```

and Bash can perform arithmetic operations when the contents are appropriate for them.

For this section, the important idea is that we generally assign values directly without declaring a type beforehand.

---

# Arrays

An **array** allows us to store several values under a single variable name.

Instead of:

```bash
domain1="www.inlanefreight.com"
domain2="ftp.inlanefreight.com"
domain3="vpn.inlanefreight.com"
```

we can organize the values together:

```bash
domains=(www.inlanefreight.com ftp.inlanefreight.com vpn.inlanefreight.com www2.inlanefreight.com)
```

Conceptually:

```text
domains
   │
   ├── [0] www.inlanefreight.com
   ├── [1] ftp.inlanefreight.com
   ├── [2] vpn.inlanefreight.com
   └── [3] www2.inlanefreight.com
```

---

# Array Indexes

Array indexes start at:

```text
0
```

Therefore:

```text
First element  → index 0
Second element → index 1
Third element  → index 2
Fourth element → index 3
```

This is important because:

```bash
${domains[0]}
```

retrieves the **first** element, not the second.

---

# Creating an Array

The material provides:

```bash
#!/bin/bash

domains=(www.inlanefreight.com ftp.inlanefreight.com vpn.inlanefreight.com www2.inlanefreight.com)

echo ${domains[0]}
```

Execution:

```bash
menali@htb[/htb]$ ./Arrays.sh

www.inlanefreight.com
```

Since:

```text
domains[0]
      │
      ▼
www.inlanefreight.com
```

---

# Curly Braces

When accessing an array element, the material uses:

```bash
${domains[0]}
```

The curly braces:

```text
${...}
```

are used for variable expansion.

So:

```bash
echo ${domains[0]}
```

means:

> Expand the value stored at index `0` of the `domains` array and pass it to `echo`.

---

# Quotes and Array Elements

Quotes affect how spaces are interpreted when creating arrays.

Consider:

```bash
domains=(www.inlanefreight.com ftp.inlanefreight.com vpn.inlanefreight.com www2.inlanefreight.com)
```

We have four separate elements:

```text
[0] → www.inlanefreight.com
[1] → ftp.inlanefreight.com
[2] → vpn.inlanefreight.com
[3] → www2.inlanefreight.com
```

But now consider:

```bash
domains=("www.inlanefreight.com ftp.inlanefreight.com vpn.inlanefreight.com" www2.inlanefreight.com)
```

The quoted portion is treated as **one element**, even though it contains spaces.

Therefore:

```text
[0]
→ www.inlanefreight.com ftp.inlanefreight.com vpn.inlanefreight.com

[1]
→ www2.inlanefreight.com
```

Running:

```bash
echo ${domains[0]}
```

produces:

```bash
www.inlanefreight.com ftp.inlanefreight.com vpn.inlanefreight.com
```

The key idea is:

```text
Without quotes:

A B C
↓ ↓ ↓
3 elements
```

versus:

```text
With quotes:

"A B C"
   ↓
1 element
```

---

# Arguments vs Variables vs Arrays

These three concepts are related but serve different purposes.

| Concept  | Purpose                                    | Example                  |
| -------- | ------------------------------------------ | ------------------------ |
| Argument | Provide information when starting a script | `./script.sh google.com` |
| Variable | Store one value for later use              | `domain=$1`              |
| Array    | Store multiple values together             | `domains=(a b c)`        |

A common workflow is:

```text
Command line
     │
     ▼
  Argument
     │
     ▼
     $1
     │
     ▼
  Variable
     │
     ▼
Used throughout script
```

And when several related values are needed:

```text
Multiple values
      │
      ▼
     Array
      │
 ┌────┼────┐
 ▼    ▼    ▼
[0]  [1]  [2]
```

---

# Quick Reference

| Syntax               | Meaning                         |
| -------------------- | ------------------------------- |
| `$0`                 | Script name                     |
| `$1`                 | First argument                  |
| `$2`                 | Second argument                 |
| `$#`                 | Number of arguments             |
| `$@`                 | All command-line arguments      |
| `$$`                 | Current shell/process PID       |
| `$?`                 | Exit status of previous command |
| `name=value`         | Assign variable                 |
| `$name`              | Use variable value              |
| `array=(a b c)`      | Create array                    |
| `${array[0]}`        | First array element             |
| `chmod +x script.sh` | Add execution permission        |

---

# What to Remember First

The most important argument variables are:

```text
$#
→ number of arguments

$0
→ script name

$1
→ first argument

$@
→ all arguments

$?
→ previous command's exit status
```

For variables:

```bash
domain=$1
```

means:

```text
Assign $1 to domain
```

while:

```bash
echo $domain
```

means:

```text
Use the value of domain
```

And remember:

```bash
variable="value"
```

not:

```bash
variable = "value"
```

For arrays:

```bash
domains=(domain1 domain2 domain3)
```

and:

```bash
${domains[0]}
```

retrieves the first element because Bash array indexing begins at `0`.

---

## Key Takeaway

**Arguments provide values to our script, variables store values for later use, and arrays allow us to organize multiple values under a single name. Bash provides special variables such as `$#`, `$0`, `$1`, `$@`, `$$`, and `$?` to access information about arguments and execution. Variable assignments use no spaces around `=`, and Bash arrays use indexes beginning at `0`.**
