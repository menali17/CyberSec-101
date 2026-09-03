# Bourne Again Shell

`Bash` stands for **Bourne Again Shell** and is a shell and scripting language commonly used to communicate with Unix-based operating systems and execute commands.

Bash can also be used on Windows through the **Windows Subsystem for Linux (WSL)**.

For penetration testing, Bash is important because it helps us work efficiently with Unix-based systems, especially when we need to:

* Work with the terminal
* Filter data
* Combine commands
* Process large amounts of information
* Automate repetitive tasks

---

# Bash Scripting

Instead of repeatedly executing commands manually, we can combine them into a:

```text
Bash Script
```

This allows us to automate tasks and process information more efficiently.

Conceptually:

```text
Manual Work

Command 1
Command 2
Command 3
Command 4
```

can become:

```text
Bash Script
    │
    ├── Command 1
    ├── Command 2
    ├── Command 3
    └── Command 4
```

---

# Main Scripting Concepts

The material introduces the main components that we will study in Bash scripting:

```text
Input & Output
Arguments
Variables
Arrays
Conditional Execution
Arithmetic
Loops
Comparison Operators
Functions
```

These concepts will be explored in more detail in the following sections.

For now, the important idea is that they allow us to control how a script receives, processes, and returns information.

---

# Script Interpreter

A Bash script is executed by an:

```text
Interpreter
```

In this case:

```text
Bash
```

Conceptually:

```text
Script
   │
   ▼
Bash Interpreter
   │
   ▼
Commands Executed
```

Unlike a typical compiled workflow, Bash scripts can be interpreted and executed without first compiling them into a separate executable program.

---

# Executing a Script

The material presents three examples.

Using Bash:

```bash
menali@htb[/htb]$ bash script.sh <optional arguments>
```

Using `sh`:

```bash
menali@htb[/htb]$ sh script.sh <optional arguments>
```

Executing the script directly:

```bash
menali@htb[/htb]$ ./script.sh <optional arguments>
```

Arguments can also be supplied to the script:

```bash
menali@htb[/htb]$ ./CIDR.sh inlanefreight.com
```

Here:

```text
./CIDR.sh
→ Script

inlanefreight.com
→ Argument supplied to the script
```

---

# CIDR.sh Example

The material introduces `CIDR.sh` as an example of what we can build with Bash scripting.

Running:

```bash
menali@htb[/htb]$ ./CIDR.sh inlanefreight.com

Discovered IP address(es):
165.22.119.202

Additional options available:
    1) Identify the corresponding network range of target domain.
    2) Ping discovered hosts.
    3) All checks.
    *) Exit.

Select your option: 3

NetRange for 165.22.119.202:
NetRange:       165.22.0.0 - 165.22.255.255
CIDR:           165.22.0.0/16

Pinging host(s):
165.22.119.202 is up.

1 out of 1 hosts are up.
```

shows that the script can automate several operations using only a domain as input.

---

# What CIDR.sh Does

At a high level:

```text
Domain
  │
  ▼
CIDR.sh
  │
  ├── Discover IP address
  │
  ├── Identify network range
  │
  ├── Ping discovered hosts
  │
  └── Present available options
```

The complete script combines many Bash concepts, but these will be analyzed individually in the next sections.

For now, we only need to understand the overall structure.

---

# Script Structure

The material divides `CIDR.sh` into five parts:

```text
1. Check for given arguments

2. Identify network range for the specified IP address(es)

3. Ping discovered IP address(es)

4. Identify IP address(es) of the specified domain

5. Available options
```

---

# 1. Check for Given Arguments

The script first checks whether we provided a target domain.

Conceptually:

```text
Was a domain provided?
       │
       ├── Yes → Continue
       │
       └── No  → Display usage information
```

The detailed `if-else` syntax will be explored later.

---

# 2. Identify Network Range

The script contains a function that performs a:

```text
whois query
```

for discovered IP addresses.

Its purpose is to identify information such as:

```text
NetRange
CIDR
```

and store relevant information in:

```text
CIDR.txt
```

---

# 3. Ping Discovered Hosts

Another function checks whether discovered hosts are reachable.

Conceptually:

```text
Network Range
     │
     ▼
IP Addresses
     │
     ▼
Ping each host
     │
     ├── Host is up
     └── Host is down
```

The script then counts the results.

The detailed loop logic will be covered later.

---

# 4. Identify IP Addresses

The script identifies the IPv4 address associated with the supplied domain.

Conceptually:

```text
inlanefreight.com
        │
        ▼
Domain Resolution
        │
        ▼
165.22.119.202
```

This IP address can then be used by the other parts of the script.

---

# 5. Available Options

Finally, the script presents several options:

```text
1 → Identify network range

2 → Ping discovered hosts

3 → Perform all checks

* → Exit
```

This allows us to choose which operations should be performed.

---

# CIDR.sh Mental Model

We do **not** need to understand every line of `CIDR.sh` yet.

The script is mainly demonstrating how different Bash concepts can work together:

```text
              Bash Script
                  │
       ┌──────────┼──────────┐
       ▼          ▼          ▼
    Input     Processing    Output
       │          │          │
    Domain     Commands    Results
                  │
            ┌─────┴─────┐
            ▼           ▼
        Functions      Loops
```

The following sections will explain these individual components.

---

# Why This Matters for Pentesting

Without scripting, we might repeatedly perform tasks manually:

```text
Resolve domain
      ↓
Copy IP
      ↓
Run WHOIS
      ↓
Extract network
      ↓
Check hosts
      ↓
Organize results
```

With scripting:

```text
Input
  │
  ▼
Script
  │
  ▼
Automated processing
  │
  ▼
Useful information
```

This becomes increasingly valuable when we need to process large amounts of information during penetration testing.

---

# Quick Reference

| Concept          | Meaning                                      |
| ---------------- | -------------------------------------------- |
| Bash             | Shell and scripting language                 |
| Script           | Commands organized for automated execution   |
| Interpreter      | Program that processes the script            |
| `bash script.sh` | Execute using Bash                           |
| `sh script.sh`   | Execute using `sh`                           |
| `./script.sh`    | Execute the script directly                  |
| Arguments        | Values supplied when executing the script    |
| CIDR.sh          | Example automation script used by the module |

---

# What to Remember First

At this point, the most important idea is:

```text
Bash
→ execute commands
→ combine commands
→ process information
→ automate tasks
```

We should also recognize the major scripting concepts:

```text
Input / Output
Arguments
Variables / Arrays
Conditionals
Arithmetic
Loops
Comparisons
Functions
```

But we **do not need to master their syntax yet**.

Finally:

```bash
bash script.sh
```

```bash
sh script.sh
```

```bash
./script.sh
```

are the execution forms introduced in this section.

---

## Key Takeaway

**Bash scripting allows us to combine commands and automate repetitive tasks on Unix-based systems. This is especially useful in penetration testing, where we often need to filter, process, and analyze large amounts of information efficiently. The `CIDR.sh` script is introduced as an example of how arguments, conditions, functions, loops, commands, and user input can work together; the following sections will explain these components individually.**
