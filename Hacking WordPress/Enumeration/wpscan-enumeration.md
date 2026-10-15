# WordPress - WPScan Enumeration

## Overview

WPScan automates the enumeration of WordPress components, including plugins, themes, users, and exposed files.

The `--enumerate` option controls which components are enumerated.

---

## 1. Basic Enumeration

```bash
wpscan --url http://TARGET --enumerate
```

**Options:**

- `--url`: Specifies the target website.
- `--enumerate`: Enables enumeration of WordPress components.

### Enumeration with Vulnerability Data

```bash
wpscan --url http://TARGET --enumerate --api-token YOUR_TOKEN
```

- `--api-token`: Authenticates to the WPScan vulnerability database API.
- `YOUR_TOKEN`: Personal API token.

The API token is used to retrieve information about known vulnerabilities.

It is not a WordPress login credential.

---

## 2. Enumeration Options

We can specify which components WPScan should enumerate.

### Enumerate All Plugins

```bash
wpscan --url http://TARGET -e ap
```

- `-e`: Short form of `--enumerate`.
- `ap`: All plugins.

### Enumerate Vulnerable Plugins

```bash
wpscan --url http://TARGET -e vp
```

- `vp`: Vulnerable plugins.

### Enumerate All Themes

```bash
wpscan --url http://TARGET -e at
```

- `at`: All themes.

### Enumerate Users

```bash
wpscan --url http://TARGET -e u
```

- `u`: WordPress users.

### Configure Threads

```bash
wpscan --url http://TARGET --enumerate -t 10
```

- `-t`: Specifies the number of threads.
- `10`: Number of threads to use.

The HTB material describes a default of 5 threads.

Increasing the number of threads may speed up scanning but can also increase the load on the target server.

---

## 3. Detection Techniques

WPScan uses different techniques to identify WordPress components.

### Passive Detection

Passive Detection analyzes information exposed through ordinary web responses.

Examples:

- HTTP response headers
- HTML source code
- JavaScript and CSS references
- RSS feeds
- Plugin references on webpages

Example:

```text
Found By: Headers (Passive Detection)
```

A server might reveal information through HTTP headers:

```http
Server: Apache/2.4.38
X-Powered-By: PHP/7.3.15
```

### Aggressive Detection

Aggressive Detection performs additional direct requests and checks.

Examples:

- Checking specific files and directories
- Probing known WordPress endpoints
- Enumerating author IDs
- Checking plugin files

Example:

```text
Found By: Direct Access (Aggressive Detection)
```

Aggressive detection may generate more HTTP requests than passive detection.

It does not necessarily involve exploiting vulnerabilities.

---

## 4. Understanding Scan Results

### Web Server Identification

```text
Server: Apache/2.4.38 (Debian)
X-Powered-By: PHP/7.3.15
```

WPScan identified information about the web server and PHP environment from HTTP headers.

### XML-RPC Detection

```text
XML-RPC seems to be enabled
```

WPScan detected the XML-RPC endpoint:

```text
/xmlrpc.php
```

XML-RPC can provide remote functionality, including authentication methods.

Its availability does not automatically indicate a vulnerability.

### WordPress Version Detection

```text
WordPress version 5.3.2 identified
Found By: Rss Generator (Passive Detection)
```

WPScan identified the version through an RSS generator tag.

Example:

```xml
<generator>https://wordpress.org/?v=5.3.2</generator>
```

Note: This is a historical HTB example, not a current WordPress version recommendation.

### Theme Detection

```text
WordPress theme in use: twentytwenty
```

WPScan identified the active WordPress theme.

It may also detect:

- Theme location
- Theme version
- Readme files
- Outdated theme versions
- Known vulnerabilities

---

## 5. Plugin Vulnerability Detection

Example:

```text
Plugin: mail-masta

2 vulnerabilities identified:

- Unauthenticated Local File Inclusion (LFI)
- Multiple SQL Injection
```

### Local File Inclusion (LFI)

A vulnerability that may allow an attacker to include or read local files through an application.

### SQL Injection (SQLi)

A vulnerability where improperly handled input can interfere with SQL queries executed by the application.

### Authentication Requirements

- `Unauthenticated`: The vulnerability may be exploitable without authentication.
- `Authenticated`: Exploitation requires an authenticated user, potentially with a specific permission level.

**Important:** Identifying a vulnerable plugin version does not automatically prove that exploitation is possible.

---

## 6. User Enumeration

WPScan can automatically identify WordPress users.

Example:

```text
User(s) Identified:

admin
david
roger
```

Possible detection techniques include:

- Author Posts
- Author ID Enumeration
- Login Error Analysis

Example:

```text
Found By: Author Posts - Display Name
Confirmed By: Author Id Brute Forcing
```

**Note:** Author ID Brute Forcing refers to testing numeric author IDs, not guessing passwords.

This is similar to the manual technique:

```bash
curl -s -I "http://TARGET/?author=1"
```

WPScan automates these checks.

---

## 7. Understanding WPScan Output Symbols

WPScan uses different markers to organize findings.

| Symbol | Meaning |
|--------|---------|
| `[+]` | Identified information or finding |
| `[i]` | Informational message |
| `[!]` | Warning or potentially important security finding |

A warning does not necessarily confirm a directly exploitable vulnerability.

---

## 8. Manual vs Automated Enumeration

### Manual Enumeration

```bash
curl -s http://TARGET/wp-json/wp/v2/users | jq
```

We manually request the WordPress REST API endpoint.

### Automated Enumeration

```bash
wpscan --url http://TARGET -e u
```

WPScan performs automated checks to identify users.

### Why Both Matter

Manual enumeration helps us:

- Understand HTTP requests.
- Analyze responses.
- Verify scanner findings.
- Troubleshoot unexpected results.

Automated enumeration helps us:

- Save time.
- Perform multiple checks.
- Identify potentially vulnerable components.
- Organize findings.

---

## Key Takeaways

1. WPScan automates WordPress security enumeration.
2. The `--enumerate` option controls the enumeration process.
3. `-e ap` enumerates all plugins.
4. `-e u` enumerates users.
5. `-t` configures the number of scanner threads.
6. Passive detection analyzes information exposed in normal responses.
7. Aggressive detection performs additional direct checks.
8. WPScan can identify known vulnerabilities through its vulnerability database.
9. Scanner findings must be analyzed and validated.
10. Enumeration and exploitation are different stages of a security assessment.