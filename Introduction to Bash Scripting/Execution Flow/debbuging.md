# Debugging

**Debugging** is the process of finding, tracking, and fixing errors (**bugs**) in our code.

In Bash, debugging can help us:

* Find syntax or typing mistakes.
* Follow the execution of a script.
* See which commands are being executed.
* Inspect the values being used by commands.
* Understand why a script behaves unexpectedly.

Bash provides two useful options for this:

```bash
-x
-v
```

---

# Bash Debugging Options

The two options introduced in this section are:

| Option | Name     | Purpose                               |
| ------ | -------- | ------------------------------------- |
| `-x`   | `xtrace` | Shows commands as Bash executes them  |
| `-v`   | verbose  | Shows script lines as Bash reads them |

They can also be combined:

```bash
bash -x -v script.sh
```

---

# Debugging with `-x`

To execute a script with debugging enabled, we can use:

```bash
bash -x CIDR.sh
```

The HTB example produces:

```bash
menali@htb[/htb]$ bash -x CIDR.sh

+ '[' 0 -eq 0 ']'
+ echo -e 'You need to specify the target domain.\n'
You need to specify the target domain.

+ echo -e Usage:
Usage:
+ echo -e '\tCIDR.sh <domain>'
    CIDR.sh <domain>
+ exit 1
```

The lines beginning with:

```bash
+
```

represent commands Bash is executing.

For example:

```bash
+ '[' 0 -eq 0 ']'
```

shows the evaluated condition.

The original script contained:

```bash
if [ $# -eq 0 ]
```

Since no arguments were provided:

```text
$# = 0
```

Bash effectively evaluates:

```bash
[ 0 -eq 0 ]
```

The condition is true, so the script enters the corresponding branch.

---

# Understanding `-x`

Suppose our script contains:

```bash
name="HTB"
echo "$name"
```

Running normally would only show:

```bash
HTB
```

Running with:

```bash
bash -x script.sh
```

would show something similar to:

```bash
+ name=HTB
+ echo HTB
HTB
```

This allows us to distinguish between:

```text
+ command
```

and:

```text
command output
```

So:

```text
-x
 ↓
Shows what Bash is executing
 ↓
Includes expanded variable values
 ↓
Helps us follow program flow
```

---

# The `+` Sign

During `-x` debugging, Bash places a:

```bash
+
```

before traced commands.

For example:

```bash
+ echo -e Usage:
```

means Bash executed:

```bash
echo -e "Usage:"
```

The next line:

```bash
Usage:
```

is the actual output produced by that command.

Therefore:

```text
+ echo -e Usage:
│
└── Command being executed

Usage:
│
└── Output produced by the command
```

This distinction is useful when reading debugging output.

---

# Debugging CIDR.sh

The original part of `CIDR.sh` is:

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

When WE execute:

```bash
bash -x CIDR.sh
```

without passing a domain, Bash shows:

```bash
+ '[' 0 -eq 0 ']'
```

Because:

```text
$# = number of arguments
$# = 0
```

The condition:

```bash
[ $# -eq 0 ]
```

therefore becomes:

```bash
[ 0 -eq 0 ]
```

which is true.

The script then executes:

```bash
echo -e "You need to specify the target domain.\n"
echo -e "Usage:"
echo -e "\t$0 <domain>"
exit 1
```

The trace allows us to follow every step.

---

# Debugging with `-v`

Bash also provides:

```bash
-v
```

for **verbose** execution.

The `-v` option displays the script lines as Bash reads them.

The material combines it with `-x`:

```bash
bash -x -v CIDR.sh
```

Output:

```bash
menali@htb[/htb]$ bash -x -v CIDR.sh

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
+ '[' 0 -eq 0 ']'
+ echo -e 'You need to specify the target domain.\n'
You need to specify the target domain.

+ echo -e Usage:
Usage:
+ echo -e '\tCIDR.sh <domain>'
    CIDR.sh <domain>
+ exit 1
```

Now WE can see both:

```text
Original script lines
        +
Commands being executed
        +
Program output
```

---

# `-x` vs `-v`

The difference is easiest to understand like this:

### `-x`

```bash
bash -x script.sh
```

shows **what Bash executes after evaluating variables and expressions**.

Mental model:

```text
What is Bash doing?
```

---

### `-v`

```bash
bash -v script.sh
```

shows **the script lines Bash reads**.

Mental model:

```text
What code is Bash reading?
```

---

### `-x -v`

```bash
bash -x -v script.sh
```

combines both views:

```text
Source code being read
        ↓
Commands being executed
        ↓
Result/output
```

This provides a more detailed view of the script's execution.

---

# Reading the Debugging Output

Consider:

```bash
+ '[' 0 -eq 0 ']'
```

This tells us that Bash evaluated the test using:

```text
0
```

as the number of arguments.

Then:

```bash
+ echo -e 'You need to specify the target domain.\n'
```

shows the command Bash executed.

The next line:

```bash
You need to specify the target domain.
```

is the output from that command.

Finally:

```bash
+ exit 1
```

shows that the script terminated with:

```text
status code = 1
```

So WE can reconstruct the program flow:

```text
Start script
    ↓
Check number of arguments
    ↓
$# = 0
    ↓
0 -eq 0 → TRUE
    ↓
Print error message
    ↓
Print usage
    ↓
exit 1
```

---

# Why Debugging Is Useful

Suppose WE expected a condition to be true:

```bash
if [ "$number" -eq 10 ]
```

but the script behaves unexpectedly.

With:

```bash
bash -x script.sh
```

WE might see:

```bash
+ '[' 5 -eq 10 ']'
```

Now the problem is immediately clearer:

```text
Expected:
number = 10

Actual:
number = 5
```

This is why `-x` is useful when WE need to understand the actual values and execution path of a script.

---

# Debugging and Security

The material also points out that debugging concepts are relevant in cybersecurity.

Errors can reveal information about how programs behave. During security analysis, unexpected inputs may be used to observe how a program handles errors and behaves internally.

More advanced topics involving areas such as:

```text
CPU behavior
Assembler
Memory
Program vulnerabilities
Code execution
```

are covered in other modules.

For this section, the important point is simply:

> Debugging helps us understand what a program is doing when something goes wrong.

---

# Quick Reference

### Normal execution

```bash
bash script.sh
```

### Trace executed commands

```bash
bash -x script.sh
```

### Verbose script reading

```bash
bash -v script.sh
```

### Combine both

```bash
bash -x -v script.sh
```

### Debug trace indicator

```bash
+
```

Example:

```bash
+ echo HTB
HTB
```

Meaning:

```text
+ echo HTB → command executed
HTB        → command output
```

---

# Core Mental Model

```text
DEBUGGING
"Why is our script behaving this way?"


-x
"What commands is Bash executing?"


-v
"What code is Bash reading?"


-x -v
"Show us both"
```

A useful way to remember the difference is:

```text
-v → source/code being read

-x → execution being traced
```

---

## Key Takeaway

**Debugging helps us find and understand errors in Bash scripts. The `-x` (`xtrace`) option shows the commands Bash executes and their evaluated values, while `-v` shows the script lines as Bash reads them. Combining `-x` and `-v` gives us a more detailed view of both the code and its execution flow.**
