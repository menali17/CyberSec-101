
# Finding Files and Directories

---

## Searching with CMD

During system enumeration, we often need to locate specific files, applications, directories, or sensitive information.

Windows provides several built-in utilities to search for files and text without installing additional tools.

### Where

The `where` command allows us to locate files and executables by searching the current directory and the directories specified in the `PATH` environment variable.

For example:

```cmd
where calc.exe
```

Output:

```text
C:\Windows\System32\calc.exe
```

However, if the file is located outside our current directory and the directories listed in `PATH`, the command may not find it.

#### Recursive Search

We can use the `/R` parameter to recursively search a specified directory and its subdirectories.

```cmd
where /R C:\Users\student\ bio.txt
```

Output:

```text
C:\Users\student\Downloads\bio.txt
```

#### Using Wildcards

We can combine recursive searches with wildcards to find files matching specific patterns.

For example, to locate every CSV file within a user's directory:

```cmd
where /R C:\Users\student\ *.csv
```

Output:

```text
C:\Users\student\AppData\Local\live-hosts.csv
```

This is useful when searching for configuration files, documents, scripts, and other files of interest during system enumeration.

---

## Searching File Contents

Locating a file is not always sufficient. We may also need to search for specific strings or patterns within its contents.

Windows provides two primary utilities for this purpose: `find` and `findstr`.

### Find

The `find` command searches for a specified text string within files or command output.

```cmd
find "password" "C:\Users\student\not-passwords.txt"
```

This searches the specified file for lines containing the string `password`.

#### Find Modifiers

| Modifier | Description |
|---|---|
| `/I` | Performs a case-insensitive search. |
| `/V` | Displays lines that do not contain the specified string. |
| `/N` | Displays line numbers. |
| `/C` | Displays the number of lines containing the specified string. |

We can combine these modifiers to customize our search.

```cmd
find /N /I /V "IP Address" example.txt
```

This displays the line numbers of all lines that do not contain `IP Address`, ignoring case sensitivity.

We can also combine `find` with pipelines:

```cmd
ipconfig /all | find /I "IPv4"
```

This filters the output of `ipconfig` to display lines containing `IPv4`.

### Findstr

The `findstr` command provides more advanced search capabilities than `find`.

It supports searching for multiple strings, regular expression patterns, and recursive searches across directories.

For those familiar with Linux, `findstr` is conceptually similar to `grep`, although its regular expression capabilities are more limited.

For example:

```cmd
findstr /I "password" *.txt
```

This searches all `.txt` files in our current directory for the string `password`, ignoring case sensitivity.

Common parameters:

| Parameter | Description |
|---|---|
| `/I` | Performs a case-insensitive search. |
| `/S` | Searches the current directory and all subdirectories. |
| `/N` | Displays matching line numbers. |
| `/R` | Interprets search strings as regular expressions. |
| `/C:` | Searches for a specified literal string. |
| `/M` | Displays only the names of files containing matches. |

We can combine these options to perform more specific searches:

```cmd
findstr /S /I /N /C:"password" *.txt
```

This recursively searches text files for `password` and displays the matching lines with their line numbers.

---

## Evaluating and Comparing Files

Windows provides several utilities for comparing files, detecting differences, and organizing text.

These capabilities are useful when inspecting scripts, analyzing configuration changes, and comparing files during security assessments.

### Comp

The `comp` command compares two files byte by byte and identifies differences between them.

```cmd
comp file-1.md file-2.md
```

If both files are identical, the command returns:

```text
Files compare OK
```

If differences exist, `comp` identifies their locations.

We can use the `/A` parameter to display differences as ASCII characters.

```cmd
comp file-1.md file-2.md /A
```

Example output:

```text
Compare error at OFFSET 2
file1 = a
file2 = b
```

The `/L` parameter can also display the line numbers associated with differences.

### FC (File Compare)

The `fc` command also compares files, but it provides more detailed information about differences between their contents.

Unlike `comp`, which performs byte-by-byte comparisons, `fc` can compare text files line by line.

For example:

```cmd
fc passwords.txt modded.txt /N
```

The `/N` parameter displays line numbers when performing a text comparison.

Common parameters:

| Parameter | Description |
|---|---|
| `/A` | Displays only the first and last lines of each set of differences. |
| `/B` | Performs a binary comparison. |
| `/C` | Ignores case differences. |
| `/L` | Compares files as ASCII text. |
| `/N` | Displays line numbers during text comparisons. |
| `/U` | Compares files as Unicode text. |
| `/W` | Compresses whitespace for comparison. |

We can display all available parameters using:

```cmd
fc /?
```

During security assessments, file comparison can help us identify modified configuration files, unexpected changes to scripts, or differences between collected datasets.

---

## Sorting Files

The `sort` command allows us to organize text alphabetically.

We can provide input directly from a file, through a pipeline, or through standard input redirection.

### Basic Sorting

Suppose we have a file containing the following data:

```text
a
b
d
h
w
a
q
h
g
```

We can sort its contents using:

```cmd
sort file-1.md
```

Alternatively, we can use `/O` to save the results to another file.

```cmd
sort file-1.md /O sorted.md
```

The resulting file contains:

```text
a
a
b
d
g
h
h
q
w
```

### Removing Duplicate Entries

The `/UNIQUE` parameter allows us to sort the input while removing duplicate lines.

```cmd
sort /UNIQUE sorted.md
```

Output:

```text
a
b
d
g
h
q
w
```

This is particularly useful when organizing datasets containing repeated entries.

For example, we may collect information about network hosts, usernames, or file paths and need to organize the results before comparing them.

---

## Command Comparison

| Command | Primary Purpose |
|---|---|
| `where` | Locates files and executables. |
| `where /R` | Searches recursively for files matching a specified pattern. |
| `find` | Searches for text strings within files or command output. |
| `findstr` | Performs more advanced text and pattern searches. |
| `comp` | Compares files byte by byte. |
| `fc` | Compares files and displays their differences. |
| `sort` | Sorts text alphabetically. |
| `sort /UNIQUE` | Sorts text and removes duplicate lines. |

---

## Key Takeaways

- `where` helps us locate files and executables, while `/R` enables recursive searches.
- `find` searches for specific text strings within files or command output.
- `findstr` supports more advanced searches, including regular expressions and recursive searches.
- `comp` compares files byte by byte, while `fc` provides more detailed file comparisons.
- `sort` organizes text alphabetically and can eliminate duplicate entries.
- These utilities allow us to search, inspect, compare, and organize information during Windows host enumeration without installing additional software.
