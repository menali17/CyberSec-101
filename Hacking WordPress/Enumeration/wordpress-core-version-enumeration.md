# WordPress Core Version Enumeration

Identifying the WordPress version is an important part of enumeration.

Knowing the version can help us search for:

- Known vulnerabilities
- Common misconfigurations
- Version-specific default credentials
- Outdated components

---

## Page Source

One of the easiest ways to manually identify the WordPress version is by inspecting the page source.

In the browser:

```text
CTRL + U
```

opens the page source.

Then:

```text
CTRL + F
```

can be used to search for useful information.

---

## Meta Generator

WordPress may expose its version through the HTML `meta generator` tag.

Example:

```html
<meta name="generator" content="WordPress 5.3.3" />
```

This reveals:

```text
WordPress Version → 5.3.3
```

---

## Using cURL

We can retrieve the page source from the command line:

```bash
curl -s -X GET http://blog.inlanefreight.com
```

To filter for the WordPress generator tag:

```bash
curl -s -X GET http://blog.inlanefreight.com | grep '<meta name="generator"'
```

Example output:

```html
<meta name="generator" content="WordPress 5.3.3" />
```

---

## CSS Version Information

CSS files may also expose version information through URL parameters.

Example:

```html
style.css?ver=5.3.3
```

The value:

```text
ver=5.3.3
```

may provide a hint about the WordPress version.

Example:

```html
<link rel='stylesheet'
href='http://blog.inlanefreight.com/wp-content/themes/ben_theme/style.css?ver=5.3.3'>
```

---

## JavaScript Version Information

JavaScript files may also expose version information.

Example:

```html
subscriber.js?ver=5.3.3
```

Other JavaScript files may reveal versions of libraries:

```html
jquery.js?ver=1.12.4-wp
```

These values may reveal information about:

- WordPress
- Plugins
- JavaScript libraries

---

## Comments in Source Code

The HTML source may also contain useful comments.

These comments may reveal:

- Software information
- Developer notes
- Paths
- Components
- Other useful enumeration information

---

## readme.html

Older WordPress versions may expose version information through:

```text
/readme.html
```

This file may exist in the WordPress root directory.

Example:

```text
http://target/readme.html
```

---

# Enumeration Sources

Useful places to search for WordPress version information include:

```text
HTML source
├── meta generator
├── comments
├── CSS links
└── JavaScript links

WordPress root
└── readme.html
```

---

# Main Takeaways

- Identifying the WordPress version is an important enumeration step.
- The HTML source may contain a `meta generator` tag.
- CSS files may expose versions using `?ver=`.
- JavaScript files may expose WordPress, plugin, or library versions.
- `readme.html` may reveal version information in older installations.
- Version information can later be used to search for known vulnerabilities and misconfigurations.

```text
<meta name="generator" content="WordPress X.X.X" />
```

is one of the easiest ways to identify the WordPress core version.