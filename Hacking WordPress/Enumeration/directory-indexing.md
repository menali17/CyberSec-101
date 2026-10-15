# Directory Indexing

Inactive plugins should still be considered during a WordPress assessment.

Deactivating a plugin does **not** remove its files from the server.

Example:

```text
Plugin deactivated in WordPress
        ↓
Files still exist on disk
        ↓
Files may still be accessible directly
```

---

## Disabled Plugins

A disabled plugin may still exist inside:

```text
/wp-content/plugins/
```

Example:

```text
/wp-content/plugins/mail-masta/
```

Even if the plugin is deactivated, its files may still be accessible.

Possible files and directories include:

```text
amazon_api/
inc/
lib/
plugin-interface.php
readme.txt
```

---

# Directory Indexing

Directory indexing occurs when a web server allows users to browse the contents of a directory.

Example:

```text
http://target/wp-content/plugins/mail-masta/
```

If directory indexing is enabled, the server may display:

```text
Index of /wp-content/plugins/mail-masta

amazon_api/
inc/
lib/
plugin-interface.php
readme.txt
```

---

## Why Directory Indexing Matters

Directory indexing may expose:

- Source code
- Plugin files
- Readme files
- Version information
- Sensitive information
- Vulnerable scripts

This can provide useful information during enumeration.

---

# Using cURL

We can retrieve the directory listing using:

```bash
curl -s -X GET http://blog.inlanefreight.com/wp-content/plugins/mail-masta/
```

The output will normally be HTML.

---

## html2text

The HTML output can be converted into a more readable format using:

```bash
curl -s -X GET http://blog.inlanefreight.com/wp-content/plugins/mail-masta/ | html2text
```

This may produce output such as:

```text
Index of /wp-content/plugins/mail-masta

amazon_api/
inc/
lib/
plugin-interface.php
readme.txt
```

---

# Deactivated vs Removed

A plugin that is deactivated is not necessarily safe.

```text
Deactivate ≠ Remove
```

Deactivation means WordPress is not actively using the plugin.

However:

```text
Plugin files may still exist
        ↓
Files may still be accessible
        ↓
Vulnerable code may still be exposed
```

---

# Security Best Practices

Unused plugins should be:

```text
Removed
```

or:

```text
Kept up-to-date
```

Directory indexing should also be disabled on the web server.

This prevents users from directly browsing unnecessary files and directories.

---

# Main Takeaways

- Deactivated plugins may still be accessible.
- Plugin files remain on the server unless the plugin is removed.
- Directory indexing allows users to browse directory contents.
- Directory indexing may expose sensitive or vulnerable files.
- `curl` can retrieve directory listings.
- `html2text` makes HTML directory listings easier to read.
- Unused plugins should be removed or kept updated.
- Directory indexing should be disabled.

```text
Disabled Plugin
      ↓
Files still exist
      ↓
Directory Indexing enabled
      ↓
Files may be accessible
```