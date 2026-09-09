# Sensitive Data Exposure

## Overview

Front-end components are executed on the **client side**.

Because of this, vulnerabilities in front-end components do not usually directly compromise the application's back end.

However, they may still expose users or administrators to attacks.

If a privileged user such as an administrator is affected, the impact may include:

* Unauthorized access
* Exposure of sensitive information
* Access to restricted functionality
* Service disruption
* Potential compromise of back-end systems

---

# Sensitive Data Exposure

**Sensitive Data Exposure** occurs when sensitive information is available in clear text to the end user.

This information may be found in:

* HTML source code
* JavaScript files
* Developer comments
* Hidden links
* Hidden directories
* Debugging information
* User information

The important distinction is:

```text
Front-End Source Code
→ Accessible from the browser

Back-End Source Code
→ Normally stored on the server
```

---

# Viewing Page Source

The HTML source code of a page can be inspected directly from the browser.

Common methods include:

```text
Right Click → View Page Source
```

or:

```text
Ctrl + U
```

The source can also be inspected through tools such as:

```text
Burp Suite
```

A page source may contain:

* HTML
* JavaScript
* External JavaScript references
* Links
* Comments
* Forms
* Hidden functionality

---

# Why Source Code Review Matters

Developers may accidentally leave sensitive information inside the front-end source code.

Examples include:

```text
Credentials
Password hashes
Test accounts
Internal links
Hidden directories
Debug parameters
Hidden functionality
User information
```

For this reason, one of the first steps when assessing a web application should be:

```text
Review the Page Source
```

We should look for easy-to-find information that may help us gain additional access.

---

# Example

Consider the following login form:

```html
<form action="action_page.php" method="post">

    <div class="container">
        <label for="uname"><b>Username</b></label>
        <input type="text" required>

        <label for="psw"><b>Password</b></label>
        <input type="password" required>

        <!-- TODO: remove test credentials test:test -->

        <button type="submit">Login</button>
    </div>
</form>
```

The interesting part is the HTML comment:

```html
<!-- TODO: remove test credentials test:test -->
```

This reveals potential test credentials:

```text
Username: test
Password: test
```

The credentials may still be valid if the developer forgot to remove them.

---

# HTML Comments

HTML comments are written using:

```html
<!-- Comment -->
```

Comments are not displayed normally on the page, but they remain visible in the source code.

Developers may unintentionally leave useful information inside them.

Examples:

```html
<!-- TODO: remove admin page -->

<!-- test account: dev:password123 -->

<!-- debug endpoint: /debug -->

<!-- temporary backup page -->
```

During a penetration test, comments are therefore worth inspecting.

---

# External JavaScript Files

Sensitive information may also exist inside JavaScript files loaded by the application.

Example:

```html
<script src="./script.js"></script>
```

We should inspect referenced JavaScript files because they may contain:

```text
Endpoints
Hidden paths
Test functionality
Credentials
Debug information
Internal links
```

---

# Low-Hanging Fruit

**Low-hanging fruit** refers to information or vulnerabilities that are easy to identify and may immediately provide useful access.

Examples include:

```text
Exposed credentials
Hidden admin pages
Test directories
Debug parameters
Interesting comments
Exposed JavaScript files
```

Reviewing the source code is a simple step that may reveal valuable information before more complex testing is required.

---

# Possible Attack Flow

Sensitive information found on the front end may help us reach more important parts of the application.

```text
Page Source
    ↓
Sensitive Information
    ↓
Credentials / Hidden Paths
    ↓
Additional Access
    ↓
Restricted Functionality
    ↓
Potential Back-End Attack
```

A front-end exposure may therefore become the starting point for a larger attack.

---

# Prevention

Front-end source code should only contain information necessary for the application to function.

Developers should avoid exposing:

* Credentials
* Sensitive comments
* Hidden internal links
* Debug information
* Unnecessary functionality

Client-side code should also be reviewed before deployment.

Sensitive information should be classified so developers know which data can safely be exposed to the browser.

---

## JavaScript Obfuscation

Developers may use techniques such as:

```text
JavaScript Packing
JavaScript Obfuscation
```

These techniques make JavaScript code harder to read and may reduce the effectiveness of automated tools looking for sensitive information.

However, the main goal should still be to avoid placing sensitive information in client-side code.

---

# Key Takeaways

* Front-end code is accessible to the user.
* Sensitive information should never be exposed unnecessarily in client-side code.
* We should inspect the page source during web application assessments.
* `Ctrl + U` can be used to view source code in many browsers.
* Burp Suite can also be used to inspect the application's source and requests.
* HTML comments may contain valuable information.
* External JavaScript files should also be reviewed.
* Useful findings may include credentials, directories, hidden pages, and debug parameters.
* Front-end information leaks may help us attack restricted or back-end functionality.

---

# Pentesting Mindset

One of the first things we should do when assessing a web application is:

```text
View Source
    ↓
Read HTML
    ↓
Check Comments
    ↓
Inspect JavaScript Files
    ↓
Look for Hidden Links
    ↓
Look for Credentials
    ↓
Look for Debug Information
```

Useful questions include:

```text
Are there developer comments?

Are there test credentials?

Are there hidden routes or directories?

Are external JavaScript files loaded?

Do those files reveal internal functionality?

Is sensitive information exposed in clear text?
```

Even simple source-code inspection can reveal information that helps us move deeper into the application.
