# Editing Files

Linux provides several ways to edit files directly from the terminal.

Two common terminal-based text editors are:

| Editor | Description                              |
| ------ | ---------------------------------------- |
| `Nano` | Simple and beginner-friendly text editor |
| `Vim`  | Powerful modal text editor based on Vi   |

Nano is generally easier to understand when we are starting, while Vim provides significantly more advanced editing capabilities.

---

# Nano

`Nano` is a terminal-based text editor that provides a relatively simple interface for creating and modifying text files.

We can open an existing file or create a new one by passing its name to `nano`.

## Syntax

```bash
nano <filename>
```

For example:

```bash
menali@htb[/htb]$ nano notes.txt
```

If `notes.txt` does not exist, Nano allows us to create it.

If it already exists, Nano opens it for editing.

---

# Nano Interface

After opening:

```bash
nano notes.txt
```

we see an interface similar to:

```bash
GNU nano 2.9.3                                    notes.txt

Here we can type everything we want and make our notes.


^G Get Help    ^O Write Out   ^W Where Is    ^K Cut Text    ^J Justify     ^C Cur Pos     M-U Undo
^X Exit        ^R Read File   ^\ Replace     ^U Uncut Text  ^T To Spell    ^_ Go To Line  M-E Redo
```

The commands available in Nano are displayed at the bottom of the screen.

---

# The Caret `^`

In Nano, the caret symbol:

```text
^
```

represents the:

```text
Ctrl
```

key.

For example:

```text
^W
```

means:

```text
Ctrl + W
```

Similarly:

```text
^O → Ctrl + O
^X → Ctrl + X
^G → Ctrl + G
```

---

# Searching in Nano

We can search for text using:

```text
Ctrl + W
```

Nano displays a search field at the bottom:

```bash
Search:
```

We can then enter the text we want to find and press:

```text
Enter
```

For example:

```bash
Search: we
```

Nano moves the cursor to the first matching occurrence.

To move to the next occurrence, we can press:

```text
Ctrl + W
```

again and then:

```text
Enter
```

without entering a new search term.

---

# Saving a File in Nano

To save our changes, we use:

```text
Ctrl + O
```

This corresponds to Nano's:

```text
Write Out
```

option.

Nano then asks for the filename:

```bash
File Name to Write: notes.txt
```

We confirm it by pressing:

```text
Enter
```

So the basic saving process is:

```text
Ctrl + O
    ↓
Confirm filename
    ↓
Enter
```

---

# Exiting Nano

After saving the file, we can leave Nano using:

```text
Ctrl + X
```

A useful basic Nano workflow is therefore:

```text
nano notes.txt
      ↓
Edit the file
      ↓
Ctrl + O
      ↓
Enter
      ↓
Ctrl + X
```

---

# `cat` — Display File Contents

After returning to the shell, we can display the contents of a file using:

```bash
cat <filename>
```

For example:

```bash
menali@htb[/htb]$ cat notes.txt

Here we can type everything we want and make our notes.
```

In this context, `cat` allows us to quickly verify the contents of the file we edited.

---

# Security-Relevant Files

Some Linux files can provide valuable information during security assessments, especially when permissions are incorrectly configured.

One important example is:

```text
/etc/passwd
```

The `/etc/passwd` file contains information about system users, including:

* Usernames
* User IDs (`UID`)
* Group IDs (`GID`)
* Home directories
* Other account information

Historically, password hashes were also stored in `/etc/passwd`.

Modern Linux systems typically store password hashes in:

```text
/etc/shadow
```

`/etc/shadow` has more restrictive permissions to protect password-related information.

Misconfigured permissions on sensitive files can expose information or potentially contribute to privilege escalation opportunities.

For penetration testing, checking file permissions is therefore an important part of evaluating a Linux system.

---

# Vim

`Vim` is an open-source text editor and an improved version of the older `Vi` editor.

The name stands for:

**Vi Improved**

Vim is designed to be:

* Fast
* Powerful
* Flexible
* Compact

Unlike Nano, Vim is a **modal editor**.

This means the same keyboard keys perform different operations depending on the currently active mode.

We can start Vim using:

```bash
menali@htb[/htb]$ vim
```

---

# Unix Philosophy and Vim

Vim follows an important Unix principle:

> Small specialized programs can be combined to perform complex tasks.

Instead of implementing every possible feature directly inside the editor, Vim can interact with external tools such as:

```text
grep
awk
sed
```

Each tool specializes in a particular task.

Conceptually:

```text
Vim
 │
 ├── grep → Search/filter text
 ├── awk  → Process structured text
 └── sed  → Transform text
```

This contributes to Vim's flexibility and power.

---

# Vim Modes

Vim has six fundamental modes.

| Mode      | Purpose                                |
| --------- | -------------------------------------- |
| `Normal`  | Execute editor commands                |
| `Insert`  | Insert text                            |
| `Visual`  | Select text                            |
| `Command` | Execute single-line Vim commands       |
| `Replace` | Overwrite existing text                |
| `Ex`      | Execute multiple commands sequentially |

Understanding Vim's modes is essential because keyboard input behaves differently depending on the active mode.

---

# Normal Mode

When Vim starts, we are normally placed in:

```text
Normal Mode
```

In this mode, keyboard input is interpreted primarily as **commands**, not text.

Therefore, simply typing characters does not work like a conventional text editor.

Normal mode is used to navigate and execute editing commands.

---

# Insert Mode

Insert mode allows us to enter text into the file.

Conceptually:

```text
Normal Mode
     ↓
Insert Mode
     ↓
Type text
```

In this mode, characters typed on the keyboard are inserted into the editor buffer.

---

# Visual Mode

Visual mode allows us to select portions of text.

The selected area can then be manipulated.

For example, selected text can be:

```text
Deleted
Copied
Replaced
```

This is conceptually similar to highlighting text with a mouse in graphical editors.

---

# Command Mode

Command mode allows us to enter commands at the bottom of Vim.

We enter it from Normal mode using:

```text
:
```

For example:

```text
:q
```

tells Vim to quit.

The workflow is:

```text
Normal Mode
     ↓
     :
     ↓
Command Mode
     ↓
     q
     ↓
Enter
```

---

# Exiting Vim

To exit Vim, we can enter Command mode and execute:

```text
:q
```

Steps:

```text
:
q
Enter
```

or simply:

```text
:q + Enter
```

The HTB material uses this as the first example of interacting with Vim's Command mode.

---

# Replace Mode

Replace mode allows newly entered characters to overwrite existing characters.

Conceptually:

```text
Existing:

hello

Replace input:

J

Result:

Jello
```

Instead of inserting characters and shifting existing text, characters at the cursor position are replaced.

---

# Ex Mode

Ex mode emulates the behavior of the older `Ex` text editor, one of Vim's predecessors.

It allows us to execute multiple commands sequentially without returning to Normal mode after each command.

This is a more advanced Vim feature.

---

# VimTutor

Vim includes an interactive tutorial for learning and practicing its commands.

We can start it from the shell using:

```bash
menali@htb[/htb]$ vimtutor
```

The tutorial opens an interactive lesson:

```bash
===============================================================================
=    W e l c o m e   t o   t h e   V I M   T u t o r    -    Version 1.7      =
===============================================================================

Vim is a very powerful editor that has many commands, too many to
explain in a tutor such as this.

The approximate time required to complete the tutor is 25-30 minutes,
depending upon how much time is spent with experimentation.
```

The tutorial is designed around actually executing Vim commands rather than only reading about them.

VimTutor can also be accessed from inside Vim through Command mode using:

```text
:Tutor
```

---

# Nano vs Vim

| Feature           | Nano                | Vim                              |
| ----------------- | ------------------- | -------------------------------- |
| Learning curve    | Easier              | Steeper                          |
| Modal editing     | No                  | Yes                              |
| Basic editing     | Simple              | Powerful                         |
| Advanced editing  | Limited             | Extensive                        |
| Commands          | Displayed on screen | Requires learning modes/commands |
| Beginner-friendly | Yes                 | Requires practice                |

Nano is useful when we need to quickly edit a file.

Vim becomes much more efficient after we become familiar with its modes and commands.

---

# Nano Quick Reference

```bash
nano notes.txt
# Open or create notes.txt

cat notes.txt
# Display the contents of notes.txt
```

Inside Nano:

```text
Ctrl + W
→ Search

Ctrl + O
→ Write Out / Save

Enter
→ Confirm filename

Ctrl + X
→ Exit
```

Basic workflow:

```text
nano file.txt
     ↓
Edit
     ↓
Ctrl + O
     ↓
Enter
     ↓
Ctrl + X
```

---

# Vim Quick Reference

```bash
vim
# Start Vim

vimtutor
# Start the interactive Vim tutorial
```

Inside Vim:

```text
:
→ Enter Command mode

:q
→ Quit Vim

:Tutor
→ Open VimTutor
```

Fundamental modes:

```text
Normal
Insert
Visual
Command
Replace
Ex
```

---

# Quick Reference

| Action                     | Command / Shortcut |
| -------------------------- | ------------------ |
| Open/create file with Nano | `nano file.txt`    |
| Search in Nano             | `Ctrl + W`         |
| Save in Nano               | `Ctrl + O`         |
| Exit Nano                  | `Ctrl + X`         |
| Display file               | `cat file.txt`     |
| Start Vim                  | `vim`              |
| Enter Vim Command mode     | `:`                |
| Quit Vim                   | `:q`               |
| Start VimTutor             | `vimtutor`         |
| Open VimTutor from Vim     | `:Tutor`           |

---

## Key Takeaway

**Nano provides a simple way for us to create and edit files directly from the terminal, while Vim provides a more powerful modal editing environment. In Nano, the most important shortcuts are `Ctrl + W` for searching, `Ctrl + O` for saving, and `Ctrl + X` for exiting. Vim separates editing into different modes, with Normal mode used for commands and Command mode allowing operations such as `:q` to exit.**
