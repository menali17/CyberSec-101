# Flow Control - Loops

Loops allow our Bash scripts to **repeat commands automatically**.

Flow control structures can generally be divided into:

```text id="s6p3de"
Branches
├── If-Else
└── Case

Loops
├── For
├── While
└── Until
```

Branches choose between different paths, while loops repeat actions.

---

# For Loops

A `for` loop repeats commands for each value in a list or data source.

Basic structure:

```bash id="nxb33s"
for variable in value1 value2 value3
do
    commands
done
```

The logic is:

```text id="v68jsr"
Take first value
      ↓
Run commands
      ↓
Take next value
      ↓
Run commands
      ↓
Continue until no values remain
```

---

# Simple For Loop

Example:

```bash id="8mdaka"
for variable in 1 2 3 4
do
    echo $variable
done
```

The loop processes one value at a time:

```text id="dm2syk"
variable = 1
↓
echo 1

variable = 2
↓
echo 2

variable = 3
↓
echo 3

variable = 4
↓
echo 4
```

Output:

```bash id="e99tly"
1
2
3
4
```

---

# For Loop with Files

We can also iterate over filenames:

```bash id="5q6x32"
for variable in file1 file2 file3
do
    echo $variable
done
```

Output:

```bash id="3c948u"
file1
file2
file3
```

The variable receives each value individually.

---

# For Loop with IP Addresses

A useful security-related example is:

```bash id="uvrnov"
for ip in 10.10.10.170 10.10.10.174 10.10.10.175
do
    ping -c 1 $ip
done
```

The variable:

```bash id="u6ww7g"
$ip
```

changes on each iteration:

```text id="8v4ose"
First iteration
$ip = 10.10.10.170

Second iteration
$ip = 10.10.10.174

Third iteration
$ip = 10.10.10.175
```

So Bash effectively performs:

```bash id="5vgnsg"
ping -c 1 10.10.10.170
ping -c 1 10.10.10.174
ping -c 1 10.10.10.175
```

without us writing every command separately.

---

# Writing a For Loop on One Line

A loop can also be written on a single line:

```bash id="l1xmh4"
for ip in 10.10.10.170 10.10.10.174; do ping -c 1 $ip; done
```

Execution:

```bash id="dlzzqj"
menali@htb[/htb]$ for ip in 10.10.10.170 10.10.10.174;do ping -c 1 $ip;done
```

The semicolons separate the parts of the loop.

Conceptually:

```text id="mlmv1n"
for ...
;
do ...
;
done
```

The multi-line and single-line forms represent the same logic.

---

# For Loops in CIDR.sh

The material uses:

```bash id="7jsa4j"
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

The important loop is:

```bash id="p6xx99"
for ip in $ipaddr
```

This means:

> For every IP contained in `ipaddr`, place the current IP in the variable `ip`.

Then the commands inside the loop are executed for that IP.

The flow is approximately:

```text id="61se5m"
ipaddr
  ↓
IP #1
  ↓
whois / CIDR processing
  ↓
IP #2
  ↓
whois / CIDR processing
  ↓
...
```

This lets the script process several IP addresses automatically.

---

# While Loops

A `while` loop repeats commands **while a condition remains true**.

Basic structure:

```bash id="49ts5u"
while [ condition ]
do
    commands
done
```

Mental model:

```text id="cbo16h"
Check condition
      ↓
    TRUE?
   /     \
 yes      no
  ↓        ↓
Run       Stop
commands
  ↓
Check again
```

---

# While Loop in CIDR.sh

The material provides:

```bash id="4ixglp"
stat=1

while [ $stat -eq 1 ]
do
    ping -c 2 $host > /dev/null 2>&1

    if [ $? -eq 0 ]
    then
        echo "$host is up."
        ((stat--))
        ((hosts_up++))
        ((hosts_total++))
    else
        echo "$host is down."
        ((stat--))
        ((hosts_total++))
    fi
done
```

Initially:

```bash id="h2gip5"
stat=1
```

The loop condition is:

```bash id="0o19rx"
[ $stat -eq 1 ]
```

which means:

> Is `stat` equal to `1`?

At the beginning:

```text id="0xw08w"
stat = 1
```

so the condition is true and the loop runs.

Later:

```bash id="cx9e5k"
((stat--))
```

changes:

```text id="dutmpe"
stat = 1
↓
stat = 0
```

The next condition becomes:

```text id="ulj2fn"
0 -eq 1
```

which is false.

The loop ends.

---

# Avoiding Infinite Loops

A `while` loop needs some way for its condition eventually to become false.

For example:

```bash id="bfpdl3"
counter=0

while [ $counter -lt 10 ]
do
    ((counter++))
done
```

The variable changes each time:

```text id="v68p4h"
0
↓
1
↓
2
↓
3
↓
...
↓
10
```

Eventually:

```bash id="c6hd99"
[ 10 -lt 10 ]
```

becomes false and the loop stops.

If the condition never changes, the loop may continue indefinitely.

---

# `break`

The command:

```bash id="coqf3m"
break
```

immediately exits the current loop.

Example from the material:

```bash id="1wcuiv"
#!/bin/bash

counter=0

while [ $counter -lt 10 ]
do
    ((counter++))
    echo "Counter: $counter"

    if [ $counter == 2 ]
    then
        continue
    elif [ $counter == 4 ]
    then
        break
    fi
done
```

Execution:

```bash id="v4x9qx"
menali@htb[/htb]$ ./WhileBreaker.sh

Counter: 1
Counter: 2
Counter: 3
Counter: 4
```

Even though the original condition would allow the loop to continue until `10`, this command:

```bash id="8nn6p1"
break
```

forces the loop to stop when:

```text id="fscoqm"
counter = 4
```

---

# `continue`

The command:

```bash id="fryxzh"
continue
```

does **not** terminate the loop.

Instead, it skips the remaining commands in the current iteration and moves to the next iteration.

In the example:

```bash id="mm7tdi"
if [ $counter == 2 ]
then
    continue
```

when:

```text id="zdlyh0"
counter = 2
```

Bash moves directly to the next loop iteration.

A useful distinction is:

```text id="9ah56y"
continue
→ skip current iteration

break
→ stop entire loop
```

---

# Following WhileBreaker.sh

Starting with:

```bash id="7dj4sm"
counter=0
```

### First iteration

```bash id="sdq19h"
((counter++))
```

gives:

```text id="rnd40i"
counter = 1
```

Then:

```bash id="hbg6lq"
echo "Counter: $counter"
```

prints:

```bash id="h3567p"
Counter: 1
```

No special condition is met.

---

### Second iteration

The counter becomes:

```text id="xzdcxj"
2
```

Output:

```bash id="f5ualf"
Counter: 2
```

Then:

```bash id="it55d6"
[ $counter == 2 ]
```

is true, so:

```bash id="lr6vek"
continue
```

starts the next iteration.

---

### Third iteration

```text id="bbrmd4"
counter = 3
```

Output:

```bash id="ragxs1"
Counter: 3
```

Neither condition is true.

---

### Fourth iteration

```text id="v78ji3"
counter = 4
```

Output:

```bash id="sobswu"
Counter: 4
```

Then:

```bash id="clh49o"
[ $counter == 4 ]
```

is true.

The script executes:

```bash id="wl6qkc"
break
```

and the loop ends.

---

# Until Loops

An `until` loop is similar to a `while` loop, but it uses the opposite idea.

A `while` loop runs:

> while the condition is true.

An `until` loop runs:

> until the condition becomes true.

In other words, the commands inside an `until` loop continue while its condition is false.

---

# Until Example

The material uses:

```bash id="3uykoa"
#!/bin/bash

counter=0

until [ $counter -eq 10 ]
do
    ((counter++))
    echo "Counter: $counter"
done
```

Initially:

```text id="csmh64"
counter = 0
```

The condition is:

```bash id="oyms0c"
[ $counter -eq 10 ]
```

At the beginning:

```text id="9k24kj"
0 == 10
↓
FALSE
```

Since the `until` condition is false, the loop runs.

---

# Following the Until Loop

First iteration:

```text id="39bimj"
counter = 0
↓
counter++
↓
counter = 1
```

Output:

```bash id="ox21sf"
Counter: 1
```

The loop keeps running because:

```text id="utcu9g"
1 == 10
↓
FALSE
```

Eventually:

```text id="nipru3"
counter = 10
```

Output:

```bash id="joplnz"
Counter: 10
```

Now the condition:

```bash id="awpl8j"
[ $counter -eq 10 ]
```

is true.

The `until` loop stops.

---

# While vs Until

These two loops are easiest to understand together.

### While

```bash id="61pzhb"
while [ $counter -lt 10 ]
do
    ...
done
```

Read as:

> Keep running **while** the counter is less than 10.

---

### Until

```bash id="z791bo"
until [ $counter -eq 10 ]
do
    ...
done
```

Read as:

> Keep running **until** the counter equals 10.

Mental comparison:

```text id="5al39i"
WHILE
→ run while condition is TRUE

UNTIL
→ run while condition is FALSE
→ stop when condition becomes TRUE
```

---

# For vs While vs Until

| Loop    | Main Idea                           |
| ------- | ----------------------------------- |
| `for`   | Repeat for each value               |
| `while` | Repeat while condition is true      |
| `until` | Repeat until condition becomes true |

A useful way to choose between them:

```text id="pyam1e"
Known list of values?
        ↓
       FOR

Repeat based on a condition?
        ↓
      WHILE

Repeat until something happens?
        ↓
      UNTIL
```

---

# Pentesting Example

Suppose WE have several approved targets:

```bash id="7mo5xz"
for ip in 10.10.10.10 10.10.10.20 10.10.10.30
do
    ping -c 1 $ip
done
```

This is a natural use for `for` because WE already know the list of values.

For a counter-based process:

```bash id="0trvl2"
counter=0

while [ $counter -lt 3 ]
do
    echo "Attempt $counter"
    ((counter++))
done
```

A `while` loop makes sense because repetition is controlled by a condition.

---

# Nested Control Structures

Loops can contain other control structures.

For example:

```bash id="9878ro"
while [ condition ]
do

    if [ another_condition ]
    then
        command
    fi

done
```

The `CIDR.sh` example already does this:

```text id="ovx9em"
WHILE
  │
  └── IF
      ├── host up
      └── host down
```

Loops can also exist inside other loops, but the material warns that excessive nesting can make scripts difficult to understand and debug.

---

# Quick Reference

### For

```bash id="0i2rbj"
for item in value1 value2 value3
do
    echo "$item"
done
```

Meaning:

```text id="dkz2vy"
Repeat once for every value
```

---

### While

```bash id="h870f3"
while [ condition ]
do
    commands
done
```

Meaning:

```text id="8cd691"
Repeat while condition is true
```

---

### Until

```bash id="jqc12s"
until [ condition ]
do
    commands
done
```

Meaning:

```text id="xk3s14"
Repeat while condition is false
Stop when it becomes true
```

---

### Break

```bash id="92vakl"
break
```

Means:

```text id="4jxjc1"
Exit the loop immediately
```

---

### Continue

```bash id="kxsuxi"
continue
```

Means:

```text id="t6z42s"
Skip the rest of this iteration
Continue with the next one
```

---

# Core Mental Model

```text id="tn5olm"
FOR
"for each value, do this"

WHILE
"while this is true, do this"

UNTIL
"until this becomes true, do this"

BREAK
"stop the loop"

CONTINUE
"skip to the next iteration"
```

---

## Key Takeaway

**Loops allow our Bash scripts to repeat tasks efficiently. `for` loops process values from a list one by one, `while` loops continue as long as a condition remains true, and `until` loops continue while their condition is false and stop once it becomes true. The `break` command terminates a loop immediately, while `continue` skips the remainder of the current iteration and starts the next one.**
