# Arithmetic

Bash supports arithmetic operations that allow us to perform calculations and modify numerical variables directly inside scripts.

---

# Arithmetic Operators

The main arithmetic operators introduced in this section are:

| Operator     | Meaning                |
| ------------ | ---------------------- |
| `+`          | Addition               |
| `-`          | Subtraction            |
| `*`          | Multiplication         |
| `/`          | Division               |
| `%`          | Modulus                |
| `variable++` | Increase variable by 1 |
| `variable--` | Decrease variable by 1 |

---

# Arithmetic Expansion

Bash can evaluate arithmetic expressions using:

```bash id="rsf48m"
$(( expression ))
```

For example:

```bash id="h4wsm3"
echo $((10 + 10))
```

Output:

```bash id="1rs8a4"
20
```

---

# Arithmetic.sh

The material combines the operators in this script:

```bash id="f94n7b"
#!/bin/bash

increase=1
decrease=1

echo "Addition: 10 + 10 = $((10 + 10))"
echo "Subtraction: 10 - 10 = $((10 - 10))"
echo "Multiplication: 10 * 10 = $((10 * 10))"
echo "Division: 10 / 10 = $((10 / 10))"
echo "Modulus: 10 % 4 = $((10 % 4))"

((increase++))
echo "Increase Variable: $increase"

((decrease--))
echo "Decrease Variable: $decrease"
```

Execution:

```bash id="v4n6en"
menali@htb[/htb]$ ./Arithmetic.sh

Addition: 10 + 10 = 20
Subtraction: 10 - 10 = 0
Multiplication: 10 * 10 = 100
Division: 10 / 10 = 1
Modulus: 10 % 4 = 2
Increase Variable: 2
Decrease Variable: 0
```

---

# Addition

```bash id="r7i2se"
$((10 + 10))
```

Result:

```bash id="jkw715"
20
```

---

# Subtraction

```bash id="o65sg6"
$((10 - 10))
```

Result:

```bash id="coc902"
0
```

---

# Multiplication

```bash id="4ab4jv"
$((10 * 10))
```

Result:

```bash id="1j9hg4"
100
```

---

# Division

```bash id="frb5t7"
$((10 / 10))
```

Result:

```bash id="zb0ib3"
1
```

In this section, Bash is working with integer arithmetic.

---

# Modulus

The modulus operator:

```bash id="websxj"
%
```

returns the remainder of a division.

For example:

```bash id="bqim3u"
$((10 % 4))
```

Since:

```text id="zn425i"
10 / 4 = 2 remainder 2
```

the result is:

```bash id="8kmxlt"
2
```

A useful way to think about modulus is:

```text id="e85sgh"
10 % 4
   ↓
remainder after dividing 10 by 4
   ↓
2
```

---

# Increment Operator

The expression:

```bash id="ktz5r0"
((increase++))
```

increases the value of `increase` by `1`.

If:

```bash id="4p0wpq"
increase=1
```

then:

```bash id="7xlhw4"
((increase++))
```

changes it to:

```text id="820xgk"
2
```

So:

```bash id="q2h3na"
echo "$increase"
```

prints:

```bash id="8eftyl"
2
```

Conceptually:

```text id="f4q087"
increase = 1
      ↓
increase++
      ↓
increase = 2
```

---

# Decrement Operator

The expression:

```bash id="tqog00"
((decrease--))
```

decreases the value by `1`.

If:

```bash id="7n3r13"
decrease=1
```

then:

```bash id="8fxu9n"
((decrease--))
```

changes it to:

```text id="10g3nq"
0
```

Conceptually:

```text id="1xfixn"
decrease = 1
      ↓
decrease--
      ↓
decrease = 0
```

---

# Arithmetic Syntax

The material shows two arithmetic forms.

To calculate a value and use the result:

```bash id="mtwng3"
$((10 + 10))
```

For example:

```bash id="5fhd0c"
echo $((10 + 10))
```

To directly modify a variable:

```bash id="o7v17j"
((variable++))
```

or:

```bash id="a8hnpe"
((variable--))
```

A simple distinction is:

```text id="qt8dbm"
$(( ... ))
→ calculate and expand the result

(( ... ))
→ perform an arithmetic operation
```

---

# Variable Length

Bash also allows us to calculate the number of characters stored in a variable.

The syntax is:

```bash id="lhj51u"
${#variable}
```

For example:

```bash id="7pshhb"
#!/bin/bash

htb="HackTheBox"

echo ${#htb}
```

Execution:

```bash id="s4bi4y"
menali@htb[/htb]$ ./VarLength.sh

10
```

The string:

```text id="03xrhk"
HackTheBox
```

contains:

```text id="67o3sa"
H a c k T h e B o x
1 2 3 4 5 6 7 8 9 10
```

Therefore:

```bash id="ejgjqy"
${#htb}
```

returns:

```bash id="0cpijp"
10
```

This is the same syntax we used in the previous exercise when checking whether `var` contained more than a certain number of characters.

For example:

```bash id="2xhfou"
${#var}
```

means:

> Return the number of characters stored in `var`.

---

# Arithmetic in CIDR.sh

The material also shows arithmetic operators inside the `CIDR.sh` script:

```bash id="x9nnw4"
for host in $cidr_ips
do
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
done
```

We do not need to study the `while` loop in depth yet because it will be discussed later.

For this section, the important part is how the variables are being modified.

---

# `stat--`

Initially:

```bash id="7j4ibd"
stat=1
```

The loop checks:

```bash id="esbjht"
while [ $stat -eq 1 ]
```

which means:

> Continue while `stat` equals `1`.

Later, both branches perform:

```bash id="cdvvcw"
((stat--))
```

So:

```text id="eh5d1w"
stat = 1
   ↓
stat--
   ↓
stat = 0
```

Once `stat` becomes `0`, the condition:

```bash id="8cuv3d"
[ $stat -eq 1 ]
```

is no longer true.

---

# `hosts_total++`

Each tested host increases:

```bash id="ob9oia"
hosts_total
```

using:

```bash id="eyx3xw"
((hosts_total++))
```

Conceptually:

```text id="ovz3vb"
hosts_total = 5
       ↓
hosts_total++
       ↓
hosts_total = 6
```

This allows the script to count how many hosts were tested.

---

# `hosts_up++`

When:

```bash id="7s15z2"
[ $? -eq 0 ]
```

is true, the previous `ping` command succeeded.

The script then performs:

```bash id="tndfkx"
((hosts_up++))
```

This counts another host as available.

So the variables have different purposes:

```text id="8vq65z"
hosts_total
→ number of hosts tested

hosts_up
→ number of hosts that responded successfully
```

---

# Following the Successful Ping Branch

Suppose:

```text id="gu177b"
stat = 1
hosts_up = 3
hosts_total = 5
```

and the ping succeeds.

The script executes:

```bash id="3gouhn"
((stat--))
((hosts_up++))
((hosts_total++))
```

The new values become:

```text id="wjgkif"
stat        → 0
hosts_up    → 4
hosts_total → 6
```

---

# Following the Failed Ping Branch

If the ping fails:

```bash id="f78xkn"
((stat--))
((hosts_total++))
```

Then, using the same starting values:

```text id="s2b9kx"
stat = 1
hosts_up = 3
hosts_total = 5
```

we get:

```text id="0jcxe0"
stat        → 0
hosts_up    → 3
hosts_total → 6
```

`hosts_up` does not increase because the host did not respond successfully.

---

# Connection with Previous Sections

Arithmetic combines naturally with the comparison operators we just studied.

For example:

```bash id="n1llwx"
if [ ${#var} -gt 100 ]
```

contains two different concepts:

```text id="t6y0th"
${#var}
   ↓
Arithmetic-related variable length
   ↓
Number of characters


-gt
 ↓
Integer comparison
 ↓
Greater than
```

So:

```bash id="iyl8rw"
[ ${#var} -gt 100 ]
```

can be read as:

> Does `var` contain more than 100 characters?

---

# Quick Reference

| Syntax       | Meaning                       |
| ------------ | ----------------------------- |
| `$((a + b))` | Addition                      |
| `$((a - b))` | Subtraction                   |
| `$((a * b))` | Multiplication                |
| `$((a / b))` | Division                      |
| `$((a % b))` | Remainder/modulus             |
| `((var++))`  | Increase `var` by 1           |
| `((var--))`  | Decrease `var` by 1           |
| `${#var}`    | Number of characters in `var` |

Useful examples:

```bash id="ea4yo1"
echo $((5 + 5))
```

```bash id="4euw1i"
10
```

```bash id="5vbnyn"
counter=1
((counter++))
echo "$counter"
```

```bash id="zqtgwa"
2
```

```bash id="6fpurc"
name="HackTheBox"
echo ${#name}
```

```bash id="o7hykg"
10
```

---

## Key Takeaway

**Bash arithmetic allows us to calculate values using operators such as `+`, `-`, `*`, `/`, and `%`, while `++` and `--` modify numerical variables by one. Arithmetic expansion uses `$((...))`, direct arithmetic operations can use `((...))`, and `${#variable}` returns the number of characters stored in a variable. These operations are especially useful for counters, loops, conditions, and tracking values inside scripts.**
