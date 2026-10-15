# WPScan Overview

## 1. What is WPScan?

WPScan is an automated WordPress security scanner used to identify potential security issues in WordPress installations.

It can enumerate:

- WordPress versions
- Installed plugins
- Installed themes
- WordPress users
- Exposed files and backup directories
- Known vulnerabilities associated with WordPress components

WPScan automates many of the manual enumeration techniques we have previously studied.

---

## 2. Installation

WPScan is available in several penetration testing distributions, including Parrot OS and Kali Linux.

We can also install it using RubyGems:

```bash
gem install wpscan
```

**Explanation:**

- `gem`: Ruby package manager.
- `install`: Installs a package.
- `wpscan`: Name of the package.

### Verify Installation

```bash
wpscan --version
```

Displays the installed WPScan version.

### Display Help

```bash
wpscan --help
```

Displays the basic help menu.

```bash
wpscan --hh
```

Displays the complete help menu, including additional command-line options.

---

## 3. Basic Scanning

To scan a WordPress website:

```bash
wpscan --url http://TARGET
```

**Explanation:**

- `wpscan`: Executes WPScan.
- `--url`: Specifies the target WordPress website.
- `http://TARGET`: Address of the target.

By default, WPScan performs several checks, including WordPress version detection, theme detection, and identification of interesting findings.

### Verbose Mode

```bash
wpscan --url http://TARGET -v
```

- `-v`: Enables verbose output, providing additional details during execution.

---

## 4. Enumeration Options

WPScan provides the `-e` or `--enumerate` option to specify what information we want to enumerate.

### Common Enumeration Options

| Option | Description |
|--------|-------------|
| `vp` | Vulnerable plugins |
| `ap` | All plugins |
| `p` | Popular plugins |
| `vt` | Vulnerable themes |
| `at` | All themes |
| `t` | Popular themes |
| `u` | WordPress users |
| `cb` | Configuration backups |
| `dbe` | Database exports |
| `m` | Media IDs |
| `tt` | Timthumb files |

### Enumerate Vulnerable Plugins

```bash
wpscan --url http://TARGET -e vp
```

Attempts to identify plugins associated with known vulnerabilities.

Vulnerability information requires access to the WPScan vulnerability database API.

### Enumerate All Plugins

```bash
wpscan --url http://TARGET -e ap
```

Attempts to enumerate installed plugins.

### Enumerate Users

```bash
wpscan --url http://TARGET -e u
```

Attempts to identify WordPress users.

This automates techniques similar to the manual user enumeration methods we previously studied.

### Enumerate Users by ID Range

```bash
wpscan --url http://TARGET -e u1-20
```

Attempts to enumerate users associated with IDs from 1 to 20.

### Enumerate Themes

```bash
wpscan --url http://TARGET -e at
```

Attempts to identify installed WordPress themes.

---

## 5. WPScan Vulnerability Database

WPScan can retrieve vulnerability information from the WordPress Vulnerability Database.

The database contains information about known security vulnerabilities affecting:

- WordPress Core
- WordPress plugins
- WordPress themes

### API Token

To retrieve vulnerability information, we can supply an API token:

```bash
wpscan --url http://TARGET --api-token YOUR_TOKEN
```

**Explanation:**

- `--api-token`: Supplies an authentication token for the vulnerability database API.
- `YOUR_TOKEN`: Our personal API token.

The API token is used to access vulnerability information, not to authenticate to the target WordPress website.

### Example: Vulnerable Plugin Enumeration

```bash
wpscan --url http://TARGET -e vp --api-token YOUR_TOKEN
```

This command:

1. Scans the target WordPress website.
2. Attempts to identify vulnerable plugins.
3. Queries the vulnerability database.
4. Reports applicable known vulnerabilities when data is available.

### API Limitations

The free WPScan API plan currently provides 25 requests per day.

WPScan can still perform enumeration without an API token, but vulnerability information from the API will not be included.

---

## 6. Scan Output

WPScan supports saving scan results to files.

### Save Results

```bash
wpscan --url http://TARGET -o scan.txt
```

- `-o`: Specifies an output file.
- `scan.txt`: File where results will be saved.

### JSON Output

```bash
wpscan --url http://TARGET -f json -o scan.json
```

**Explanation:**

- `-f json`: Selects JSON as the output format.
- `-o scan.json`: Saves the results in a JSON file.

JSON output is useful for automated reporting and further analysis.

---

## 7. Manual vs. Automated Enumeration

### Manual Enumeration

Tools: `curl`, `grep`, `jq`

Examples:

```bash
curl -s http://TARGET/wp-json/wp/v2/users | jq
```

```bash
curl -s -I "http://TARGET/?author=1"
```

Manual enumeration helps us understand how the application exposes information.

### Automated Enumeration

Tool: `wpscan`

Example:

```bash
wpscan --url http://TARGET -e u
```

WPScan automates multiple enumeration techniques and organizes the discovered information.

### Why Learn Manual Enumeration First?

- Understand the underlying HTTP requests.
- Interpret server responses.
- Troubleshoot automated scanning issues.
- Validate findings.
- Understand the impact of scanning activities.

---

## 8. Useful Commands Summary

| Command | Purpose |
|---------|---------|
| `gem install wpscan` | Install WPScan |
| `wpscan --version` | Display version |
| `wpscan --help` | Basic help |
| `wpscan --hh` | Complete help |
| `wpscan --url URL` | Basic WordPress scan |
| `wpscan --url URL -v` | Verbose scan |
| `wpscan --url URL -e vp` | Enumerate vulnerable plugins |
| `wpscan --url URL -e ap` | Enumerate all plugins |
| `wpscan --url URL -e u` | Enumerate users |
| `wpscan --url URL -e at` | Enumerate themes |
| `wpscan --url URL -o FILE` | Save scan output |
| `wpscan --url URL -f json` | JSON output |
| `wpscan --url URL --api-token TOKEN` | Include vulnerability API data |

---

## Key Takeaways

1. WPScan automates WordPress enumeration and security assessment.
2. The `--url` parameter specifies the target.
3. The `-e` parameter specifies what to enumerate.
4. WPScan can identify users, plugins, themes, and exposed files.
5. An API token enables retrieval of known vulnerability information.
6. WPScan can perform enumeration without an API token.
7. Manual enumeration remains important for understanding and validating automated results.
8. Scanning should only be performed against authorized targets.