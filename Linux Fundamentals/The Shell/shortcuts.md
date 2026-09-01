# Shortcuts

Linux terminal shortcuts help us work faster, reduce typing, and avoid unnecessary mouse usage.

The most useful shortcuts in this section can be grouped into:

```text
Auto-completion
Cursor movement
Text editing
Process control
Terminal control
Command history
Application switching
Zoom
```

---

# Auto-Complete

```text
[TAB]
```

initiates auto-completion.

It can suggest or complete:

```text
Commands
Directories
Files
Options
```

For example, if we type:

```bash
cd /ho
```

and press:

```text
[TAB]
```

the shell may complete it to:

```bash
cd /home/
```

This is one of the most useful shortcuts to develop as a habit.

---

# Cursor Movement

## Beginning of Line

```text
[CTRL] + A
```

moves the cursor to the:

```text
beginning of the current line
```

Example:

```text
sudo apt install nginx
^
```

Instead of pressing the left arrow many times, we can immediately jump to the beginning.

---

## End of Line

```text
[CTRL] + E
```

moves the cursor to the:

```text
end of the current line
```

Mental model:

```text
CTRL + A
→ start

CTRL + E
→ end
```

---

# Move Between Words

```text
[CTRL] + [←]
[CTRL] + [→]
```

jump between words.

The material also provides:

```text
[ALT] + B
→ move backward one word

[ALT] + F
→ move forward one word
```

So instead of moving:

```text
character by character
```

we can move:

```text
word by word
```

---

# Erase Text

## `CTRL + U`

```text
[CTRL] + U
```

erases everything from the cursor to the:

```text
beginning of the line
```

Example:

```text
sudo apt install nginx
         ^
```

After `CTRL + U`, everything before the cursor is erased.

---

# `CTRL + K`

```text
[CTRL] + K
```

erases everything from the cursor to the:

```text
end of the line
```

Mental model:

```text
CTRL + U
← erase left

CTRL + K
erase right →
```

---

# `CTRL + W`

```text
[CTRL] + W
```

erases the word immediately before the cursor.

Example:

```text
sudo apt install nginx
                     ^
```

Pressing:

```text
CTRL + W
```

removes:

```text
nginx
```

---

# Paste Erased Text

```text
[CTRL] + Y
```

pastes text that was previously erased using shortcuts such as:

```text
CTRL + U
CTRL + K
CTRL + W
```

This behavior is often described as:

```text
kill
→ remove text

yank
→ restore/paste text
```

So:

```text
CTRL + Y
→ paste previously erased content
```

---

# Stop a Running Process

```text
[CTRL] + C
```

sends:

```text
SIGINT
```

to the current foreground process.

This is commonly used to stop commands such as:

```bash
ping 8.8.8.8
```

or a running scan.

Conceptually:

```text
Running Process
      │
      │ CTRL + C
      ▼
    SIGINT
      │
      ▼
Process interrupted
```

---

# End-of-File

```text
[CTRL] + D
```

signals:

```text
EOF
```

which stands for:

```text
End-of-File
```

It closes the current standard input stream.

A useful mental model is:

```text
CTRL + C
→ interrupt process

CTRL + D
→ no more input / EOF
```

This distinction is important.

---

# Clear Terminal

```text
[CTRL] + L
```

clears the terminal display.

It is similar to running:

```bash
clear
```

So:

```text
CTRL + L
≈ clear
```

This is usually faster than typing the command.

---

# Suspend a Process

```text
[CTRL] + Z
```

sends:

```text
SIGTSTP
```

to the current foreground process.

This suspends the process instead of terminating it.

Conceptually:

```text
Running Process
      │
      │ CTRL + Z
      ▼
   Suspended
```

We previously saw that suspended jobs can later be managed with commands such as:

```bash
jobs
```

```bash
bg
```

```bash
fg
```

---

# `CTRL + C` vs `CTRL + Z`

This distinction is important:

```text
CTRL + C
→ interrupt / usually terminate

CTRL + Z
→ suspend
```

Conceptually:

```text
CTRL + C

Process
   │
   X
Stopped
```

versus:

```text
CTRL + Z

Process
   │
   ▼
Paused
   │
   ▼
Can potentially resume later
```

---

# Search Command History

```text
[CTRL] + R
```

searches backward through command history.

For example, after pressing:

```text
CTRL + R
```

we can type:

```text
ssh
```

and the shell searches previously executed commands containing that pattern.

This is useful when we remember part of a command but not the complete syntax.

---

# Previous and Next Commands

```text
[↑]
```

moves to the previous command in history.

```text
[↓]
```

moves toward the next command.

Mental model:

```text
↑
→ older commands

↓
→ newer commands
```

---

# Application Switching

```text
[ALT] + [TAB]
```

switches between open applications.

This is not specifically a shell command but is useful while working between:

```text
Terminal
Browser
Editor
Other applications
```

---

# Zoom

```text
[CTRL] + [+]
```

zooms in.

```text
[CTRL] + [-]
```

zooms out.

These shortcuts depend on the terminal environment but are commonly available.

---

# Shortcut Mental Map

```text
NAVIGATION

CTRL + A
→ beginning of line

CTRL + E
→ end of line

ALT + B
→ previous word

ALT + F
→ next word
```

```text
EDITING

CTRL + U
→ erase left

CTRL + K
→ erase right

CTRL + W
→ erase previous word

CTRL + Y
→ paste erased text
```

```text
PROCESS CONTROL

CTRL + C
→ interrupt process

CTRL + Z
→ suspend process

CTRL + D
→ EOF / close input
```

```text
TERMINAL

CTRL + L
→ clear screen

CTRL + R
→ search history

↑ / ↓
→ browse command history
```

---

# Quick Reference

| Shortcut       | Action                         |
| -------------- | ------------------------------ |
| `TAB`          | Auto-complete                  |
| `CTRL + A`     | Beginning of line              |
| `CTRL + E`     | End of line                    |
| `CTRL + ← / →` | Move between words             |
| `ALT + B / F`  | Move backward/forward one word |
| `CTRL + U`     | Erase to beginning             |
| `CTRL + K`     | Erase to end                   |
| `CTRL + W`     | Erase previous word            |
| `CTRL + Y`     | Paste erased text              |
| `CTRL + C`     | Send SIGINT / interrupt        |
| `CTRL + D`     | EOF                            |
| `CTRL + L`     | Clear terminal                 |
| `CTRL + Z`     | Send SIGTSTP / suspend         |
| `CTRL + R`     | Search command history         |
| `↑ / ↓`        | Previous/next history entry    |
| `ALT + TAB`    | Switch applications            |
| `CTRL + +`     | Zoom in                        |
| `CTRL + -`     | Zoom out                       |

---

# What to Remember First

The shortcuts most worth turning into muscle memory are:

```text
TAB
→ auto-complete

CTRL + C
→ stop current process

CTRL + L
→ clear terminal

CTRL + R
→ search command history

↑ / ↓
→ browse command history
```

Then:

```text
CTRL + A
→ beginning

CTRL + E
→ end

CTRL + W
→ delete previous word
```

And especially remember the difference:

```text
CTRL + C
→ SIGINT / interrupt

CTRL + Z
→ SIGTSTP / suspend

CTRL + D
→ EOF
```

---

## Key Takeaway

**Terminal shortcuts are mainly about reducing repetitive typing and controlling the current shell efficiently. `TAB`, `CTRL+C`, `CTRL+L`, `CTRL+R`, and the history arrows are the most immediately useful shortcuts, while `CTRL+A`, `CTRL+E`, `CTRL+U`, `CTRL+K`, and `CTRL+W` make editing long commands significantly faster.**
