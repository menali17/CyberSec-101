# Common Web Vulnerabilities

## Overview

When testing a web application, we may not always find a public exploit.

In these cases, we may need to manually identify vulnerabilities caused by:

* Insecure application logic
* Poor input handling
* Misconfigurations
* Weak authentication
* Weak authorization
* Unsafe file handling

Many of these vulnerabilities are related to the **OWASP Top 10**.

---

# Broken Authentication

**Broken Authentication** occurs when authentication mechanisms can be bypassed or abused.

This may allow us to:

* Log in without valid credentials
* Bypass login checks
* Impersonate another user
* Gain higher privileges

Conceptually:

```text
Attacker
   ↓
Authentication Mechanism
   ↓
Validation Fails
   ↓
Unauthorized Login
```

Example:

```text
Expected:
Valid username + password

Vulnerable:
Special input bypasses authentication
```

The important idea is:

```text
Broken Authentication
→ We should not be authenticated,
  but the application authenticates us.
```

---

# Broken Access Control

**Broken Access Control** occurs when an authenticated user can access resources or functionality they should not be allowed to access.

Example:

```text
Normal User
    ↓
/admin
    ↓
Admin Panel Accessible
```

Another example:

```text
/user/10
```

If we change it to:

```text
/user/11
```

and access another user's information without permission, access control is broken.

The key distinction is:

```text
Authentication
→ Who are we?

Authorization / Access Control
→ What are we allowed to do?
```

---

# Broken Authentication vs Broken Access Control

A useful way to remember the difference:

```text
Broken Authentication
→ Entering when we should not be able to enter.

Broken Access Control
→ Accessing something we should not be allowed to access.
```

Example:

```text
Authentication Bypass
→ Login as admin without valid credentials

Access Control Failure
→ Login as normal user and open the admin page
```

---

# Malicious File Upload

A **Malicious File Upload** vulnerability occurs when an application allows files to be uploaded without properly validating them.

For example, an application may expect:

```text
.jpg
.png
```

but allow:

```text
.php
```

If the web server executes the uploaded file, this may allow command execution.

Conceptually:

```text
Upload Feature
    ↓
Insufficient Validation
    ↓
Malicious File Uploaded
    ↓
Server Executes File
    ↓
Remote Command Execution
```

---

## File Upload Validation

Applications may attempt to validate:

* File extension
* MIME type
* File content
* File size
* File name

However, weak checks may sometimes be bypassed.

For example:

```text
shell.php.jpg
```

uses multiple extensions.

The important idea is:

```text
Uploaded file
     ↓
Can the server execute it?
```

---

# Command Injection

**Command Injection** occurs when user-controlled input is included in an operating system command without proper sanitization.

Suppose an application executes:

```text
download-plugin <plugin-name>
```

and our input is used directly as:

```text
<plugin-name>
```

If the application handles our input insecurely, we may modify the command being executed.

Conceptually:

```text
User Input
    ↓
Application
    ↓
OS Command
    ↓
Operating System
```

If the input can modify the command:

```text
User Input
    ↓
Command Injection
    ↓
Arbitrary OS Commands
```

---

# Command Injection Impact

Command Injection may lead to:

* Executing system commands
* Reading files
* Modifying files
* Accessing system information
* Compromising the back-end server

This is particularly dangerous because the input reaches the operating system directly.

---

# SQL Injection (SQLi)

**SQL Injection** occurs when user-controlled input is inserted into a SQL query without being handled securely.

Example vulnerable code:

```php
$query = "select * from users where name like '%$searchInput%'";
```

The value:

```text
$searchInput
```

comes directly from the user.

Conceptually:

```text
User Input
    ↓
Application
    ↓
SQL Query
    ↓
Database
```

If the input can modify the SQL query:

```text
User Input
    ↓
SQL Injection
    ↓
Database Executes Modified Query
```

---

# SQL Injection Impact

SQL Injection may potentially allow us to:

* Bypass authentication
* Read database information
* Modify database data
* Delete information
* Extract credentials
* Access sensitive information
* In some cases, gain further control of the server

The exact impact depends on the database, configuration, and application privileges.

---

# SQL Injection vs Command Injection

These vulnerabilities are conceptually very similar.

```text
SQL Injection

User Input
    ↓
SQL Query
    ↓
Database
```

```text
Command Injection

User Input
    ↓
OS Command
    ↓
Operating System
```

The main difference is the target:

```text
SQLi
→ Database

Command Injection
→ Operating System
```

Both often occur because:

```text
Untrusted User Input
        ↓
Inserted Into Command
        ↓
No Proper Sanitization
```

---

# Common Pattern

Several web vulnerabilities follow the same general pattern:

```text
User-Controlled Input
        ↓
Application Trusts Input
        ↓
Input Reaches Sensitive Operation
        ↓
Unexpected Behavior
```

Examples:

```text
Input → HTML
→ HTML Injection

Input → JavaScript
→ XSS

Input → SQL Query
→ SQL Injection

Input → OS Command
→ Command Injection
```

This pattern is extremely important in web penetration testing.

---

# Key Takeaways

* Many vulnerabilities must be manually discovered.
* Misconfigurations can create vulnerabilities even in otherwise secure applications.
* Broken Authentication allows authentication mechanisms to be bypassed.
* Broken Access Control allows users to access unauthorized resources.
* Authentication determines who we are.
* Authorization determines what we are allowed to access.
* Unsafe file uploads may lead to code execution.
* Command Injection targets operating system commands.
* SQL Injection targets database queries.
* Improper handling of user input is a common root cause of web vulnerabilities.

---

# Pentesting Mindset

When we identify user-controlled input, we should ask:

```text
Where does our input go?

Is it used in an SQL query?

Is it passed to an OS command?

Is it stored in a database?

Is it rendered as HTML?

Does it affect authentication?

Does it affect authorization?

Can we upload files?

How does the server validate them?
```

A useful mental model is:

```text
INPUT
  ↓
PROCESSING
  ↓
SENSITIVE FUNCTION
  ↓
POSSIBLE VULNERABILITY
```

Examples:

```text
Input → SQL       → SQL Injection

Input → OS        → Command Injection

Input → HTML/DOM  → HTML Injection / XSS

Input → Access    → Broken Access Control

Input → Login     → Broken Authentication
```
