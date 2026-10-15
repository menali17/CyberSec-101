# Plugins and Themes Enumeration

Plugins and themes are important WordPress components to enumerate because they may reveal additional functionality, versions, misconfigurations, or vulnerabilities.

Enumeration can be performed using:

```text
Passive Enumeration
Active Enumeration
```

---

# Passive Enumeration

Passive enumeration involves analyzing information that the website already exposes.

The HTML source may reference files located inside:

```text
/wp-content/plugins/
/wp-content/themes/
```

These references can reveal installed plugins and themes.

---

## Plugin Enumeration

Example:

```bash
curl -s -X GET http://blog.inlanefreight.com \
| sed 's/href=/\n/g' \
| sed 's/src=/\n/g' \
| grep 'wp-content/plugins/*' \
| cut -d"'" -f2
```

Possible output:

```text
/wp-content/plugins/wp-google-places-review-slider/...
/wp-content/plugins/mail-masta/...
```

This reveals plugin names such as:

```text
wp-google-places-review-slider
mail-masta
```

---

## Command Breakdown

### curl

```bash
curl -s -X GET <URL>
```

Retrieves the HTML source.

```text
-s → silent mode
-X GET → sends a GET request
```

---

### sed

```bash
sed 's/href=/\n/g'
sed 's/src=/\n/g'
```

Separates HTML references into different lines.

This makes links from:

```text
href=
src=
```

easier to process.

---

### grep

```bash
grep 'wp-content/plugins/*'
```

Filters the output to show plugin-related paths.

---

### cut

```bash
cut -d"'" -f2
```

Extracts the URL from the matching HTML line.

---

# Theme Enumeration

Themes can be discovered using the same technique.

Example:

```bash
curl -s -X GET http://blog.inlanefreight.com \
| sed 's/href=/\n/g' \
| sed 's/src=/\n/g' \
| grep 'themes' \
| cut -d"'" -f2
```

Possible output:

```text
/wp-content/themes/ben_theme/style.css
/wp-content/themes/ben_theme/js/navigation.js
```

This reveals the installed theme:

```text
ben_theme
```

---

# Version Information

Plugin or theme references may contain version parameters.

Example:

```text
subscriber.js?ver=5.3.3
```

or:

```text
wprev-public_combine.css?ver=6.1
```

Response headers may also contain version information for specific plugins.

---

# Limitations of Passive Enumeration

Not every installed plugin or theme will necessarily appear in the page source.

Example:

```text
Plugin installed
      ↓
Not used on current page
      ↓
No CSS/JS reference
      ↓
Not discovered passively
```

Because of this, active enumeration may be required.

---

# Active Enumeration

Active enumeration involves sending requests to possible plugin or theme paths.

Example:

```bash
curl -I -X GET http://blog.inlanefreight.com/wp-content/plugins/mail-masta
```

The option:

```text
-I
```

retrieves the HTTP response headers.

---

## Existing Plugin

Example response:

```text
HTTP/1.1 301 Moved Permanently
```

with:

```text
Location: http://blog.inlanefreight.com/wp-content/plugins/mail-masta/
```

This indicates that the directory exists and the server redirects to the path containing the trailing `/`.

```text
301 → content likely exists
```

---

## Non-Existing Plugin

Example:

```bash
curl -I -X GET http://blog.inlanefreight.com/wp-content/plugins/someplugin
```

Response:

```text
HTTP/1.1 404 Not Found
```

This indicates that the requested plugin path does not exist.

```text
404 → content does not exist
```

---

# Themes Active Enumeration

The same technique can be used for themes.

Example:

```text
/wp-content/themes/<theme-name>
```

Responses can help determine whether the theme exists.

---

# Passive vs Active Enumeration

```text
Passive Enumeration
→ Analyze information already exposed by the website
```

Examples:

```text
HTML source
CSS references
JavaScript references
```

```text
Active Enumeration
→ Send requests to suspected paths
```

Examples:

```text
/wp-content/plugins/<plugin>
/wp-content/themes/<theme>
```

---

# Automation

Manually testing many plugin and theme names can be slow.

The process can be automated using:

```text
Bash scripts
wfuzz
WPScan
```

These tools can send multiple requests and identify which plugin or theme paths exist.

---

# Main Takeaways

- Plugins are commonly stored in:

```text
/wp-content/plugins/
```

- Themes are commonly stored in:

```text
/wp-content/themes/
```

- HTML source can reveal installed plugins and themes.
- CSS and JavaScript references may expose component names and versions.
- Passive enumeration does not necessarily discover every installed component.
- Active enumeration tests whether specific paths exist.
- `301` may indicate that a directory exists.
- `404` indicates that the requested resource does not exist.
- Bash scripts, `wfuzz`, and `WPScan` can automate enumeration.

```text
Passive → Observe what is already exposed
Active  → Send requests to test what exists
```