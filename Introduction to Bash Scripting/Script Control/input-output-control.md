# Input and Output

Bash scripts often need to **receive input from us** and **control where command output goes**.

In this section, the two main ideas are:

```text
Input
→ WE provide information to the script

Output
→ The script displays and/or stores information
```

The main commands introduced here are:

```bash
read
tee
```

---

# Input Control

Sometimes a script should not continue automatically.

For example, WE may want the script to:

* wait for our decision
* let us choose between several actions
* ask before performing a specific operation
* select which function should run

The `CIDR.sh` script uses this idea by presenting a menu.

```bash
# Available options
<SNIP>
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

---

# Displaying the Menu

The first commands simply print the available options:

```bash
echo -e "Additional options available:"
echo -e "\t1) Identify the corresponding network range of target domain."
echo -e "\t2) Ping discovered hosts."
echo -e "\t3) All checks."
echo -e "\t*) Exit.\n"
```

The menu would look approximately like:

```bash
Additional options available:
    1) Identify the corresponding network range of target domain.
    2) Ping discovered hosts.
    3) All checks.
    *) Exit.
```

At this point, the script needs to know what WE want to do.

That is where `read` is used.

---

# The `read` Command

The command:

```bash
read -p "Select your option: " opt
```

waits for input from us.

The general idea is:

```text
read
 ↓
wait for input
 ↓
store input in a variable
```

Here, the variable is:

```bash
opt
```

So if WE enter:

```bash
2
```

then:

```text
opt = 2
```

---

# The `-p` Option

The option:

```bash
-p
```

allows `read` to display a prompt before waiting for input.

For example:

```bash
read -p "Select your option: " opt
```

could appear as:

```bash
Select your option: 2
```

The input remains on the same line as the prompt.

Without `-p`, WE could instead print a message separately and then call `read`.

---

# Storing the Input

The last word in:

```bash
read -p "Select your option: " opt
```

is the variable where the input will be stored.

So:

```text
Prompt
   ↓
Select your option: 3
                      ↓
                    opt
```

After WE enter `3`:

```bash
echo "$opt"
```

would produce:

```bash
3
```

---

# Using the Input

The script then uses:

```bash
case $opt in
```

to decide what action should be performed.

The section does not go deeply into `case` yet, since it will be discussed later.

For now, the important idea is:

```text
WE enter a value
      ↓
read stores it in opt
      ↓
case checks opt
      ↓
corresponding action runs
```

---

# Menu Flow

The complete logic can be visualized as:

```text
Display menu
     ↓
read input
     ↓
store in opt
     ↓
case $opt
     │
 ┌───┼────────────┐
 ↓   ↓            ↓
 1   2            3
 │   │            │
 ↓   ↓            ↓
network_range   both functions
    ping_host
```

---

# Example Choices

If WE enter:

```bash
1
```

the script runs:

```bash
network_range
```

If WE enter:

```bash
2
```

the script runs:

```bash
ping_host
```

If WE enter:

```bash
3
```

the script runs:

```bash
network_range && ping_host
```

This means the second function is executed after the first one succeeds.

---

# Output Control

We previously learned that output can be redirected into files.

For example:

```bash
command > file.txt
```

This stores the output in a file.

The problem is that WE normally do not see that output in the terminal because it is redirected.

For longer-running scripts, this can be inconvenient.

The `tee` utility solves this by allowing output to be:

```text
displayed in the terminal
        AND
written to a file
```

at the same time.

---

# The `tee` Command

The basic idea is:

```bash
command | tee file.txt
```

The pipe:

```bash
|
```

sends the command's output to `tee`.

Then `tee` sends that information to two places:

```text
Command output
      ↓
     tee
   ┌──┴──┐
   ↓     ↓
Terminal File
```

So WE can immediately see the result while also saving it.

---

# Redirection vs `tee`

With ordinary redirection:

```bash
command > results.txt
```

the flow is:

```text
Command
   ↓
results.txt
```

The output is stored, but WE do not normally see it in the terminal.

With:

```bash
command | tee results.txt
```

the flow becomes:

```text
        ┌→ Terminal
Command → tee
        └→ results.txt
```

This is the main reason `tee` is useful.

---

# `tee` in CIDR.sh

The material uses:

```bash
netrange=$(whois $ip | grep "NetRange\|CIDR" | tee -a CIDR.txt)
```

There are several steps here:

```text
whois $ip
    ↓
grep "NetRange\|CIDR"
    ↓
tee -a CIDR.txt
    ↓
output also stored in netrange
```

The first part:

```bash
whois $ip
```

retrieves information about the IP.

Then:

```bash
grep "NetRange\|CIDR"
```

filters the output for lines containing:

```text
NetRange
or
CIDR
```

Then:

```bash
tee -a CIDR.txt
```

displays the filtered result and appends it to `CIDR.txt`.

Finally, because everything is inside:

```bash
$(...)
```

the output is also captured into:

```bash
netrange
```

---

# The `-a` Option

The option:

```bash
-a
```

means:

```text
append
```

Without `-a`:

```bash
tee CIDR.txt
```

the file is written normally and existing contents may be replaced.

With:

```bash
tee -a CIDR.txt
```

new output is added to the end of the existing file.

Mental model:

```text
tee file.txt
→ write to file

tee -a file.txt
→ append to file
```

---

# Saving Discovered Hosts

The script also contains:

```bash
hosts=$(host $domain | grep "has address" | cut -d" " -f4 | tee discovered_hosts.txt)
```

The pipeline performs several steps.

First:

```bash
host $domain
```

obtains information about the domain.

Then:

```bash
grep "has address"
```

keeps only the relevant lines.

Next:

```bash
cut -d" " -f4
```

extracts the desired field.

Finally:

```bash
tee discovered_hosts.txt
```

does two things:

```text
show result in terminal
+
save result in discovered_hosts.txt
```

Because the entire pipeline is inside:

```bash
$(...)
```

the resulting output is also stored in:

```bash
hosts
```

---

# `tee` With and Without Append

Notice the difference between the two examples.

This one uses:

```bash
tee -a CIDR.txt
```

so results are appended.

This one uses:

```bash
tee discovered_hosts.txt
```

without `-a`.

Therefore:

```text
CIDR.txt
→ append new results

discovered_hosts.txt
→ normal write
```

---

# Viewing the Stored Results

The material checks the resulting files with:

```bash
menali@htb[/htb]$ cat discovered_hosts.txt CIDR.txt
```

Output:

```bash
165.22.119.202
NetRange:       165.22.0.0 - 165.22.255.255
CIDR:           165.22.0.0/16
```

This confirms that the output was successfully written to the files.

---

# Putting Input and Output Together

The section demonstrates two important forms of interaction.

### Input

```bash
read -p "Select your option: " opt
```

allows WE to send information into the script.

```text
WE
 ↓
read
 ↓
variable
```

### Output

```bash
command | tee file.txt
```

allows the script to send information to both the terminal and a file.

```text
command
   ↓
  tee
 ┌─┴─┐
 ↓   ↓
screen file
```

---

# Quick Reference

| Command                       | Purpose                                |
| ----------------------------- | -------------------------------------- |
| `read variable`               | Read input into a variable             |
| `read -p "Prompt: " variable` | Display a prompt and read input        |
| `tee file.txt`                | Display output and write it to a file  |
| `tee -a file.txt`             | Display output and append it to a file |
| `command \| tee file.txt`     | Send command output to `tee`           |

Example:

```bash
read -p "Enter domain: " domain
```

If WE enter:

```bash
inlanefreight.com
```

then:

```text
domain → inlanefreight.com
```

For output:

```bash
echo "HackTheBox" | tee output.txt
```

the terminal shows:

```bash
HackTheBox
```

and `output.txt` also contains:

```bash
HackTheBox
```

---

## Key Takeaway

**The `read` command allows our Bash scripts to pause and receive input from us, while `tee` allows command output to be displayed and saved at the same time. `read -p` provides an interactive prompt, and `tee -a` appends new results instead of replacing the existing file contents. Together, these tools make scripts more interactive and make it easier to monitor and store their output.**
