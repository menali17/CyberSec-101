# JavaScript

## Overview

**JavaScript** is one of the main programming languages used in web applications.

It is commonly executed on the **client side**, inside the web browser.

However, JavaScript can also be used on the **back end** through technologies such as:

```text
Node.js
```

A simple way to remember the main front-end technologies is:

```text
HTML       → Structure
CSS        → Appearance
JavaScript → Behavior / Functionality
```

Without JavaScript, many web pages would be mostly static and would have much less interaction.

---

# Loading JavaScript

JavaScript can be included directly inside an HTML document using the `<script>` tag.

```html
<script type="text/javascript">
    // JavaScript code
</script>
```

It can also be loaded from an external file:

```html
<script src="./script.js"></script>
```

External JavaScript files are especially important during web application analysis because they may contain:

* Application logic
* API endpoints
* Hidden parameters
* Internal paths
* Client-side validation
* Interesting functions

---

# JavaScript and the DOM

JavaScript can interact directly with the **Document Object Model (DOM)**.

Example:

```javascript
document.getElementById("button1").innerHTML = "Changed Text!";
```

This code:

1. Searches for an HTML element with the ID `button1`
2. Accesses that element
3. Changes its content to `Changed Text!`

Conceptually:

```text
JavaScript
    ↓
DOM
    ↓
HTML Element
    ↓
Content Modified
```

This ability to manipulate the DOM is one of the foundations of dynamic web applications.

---

# Dynamic Web Applications

Modern web applications heavily rely on JavaScript to dynamically control the page.

JavaScript can be used to:

* Update page content
* Process user input
* React to button clicks
* Validate forms
* Modify DOM elements
* Send and receive data
* Communicate with the back end
* Automate client-side tasks

Example:

```text
User Action
    ↓
JavaScript
    ↓
Process Input
    ↓
Update Page
```

This can happen without reloading the entire page.

---

# JavaScript and HTTP Requests

One of the most important uses of JavaScript is communicating with back-end services.

JavaScript can send **HTTP requests** to:

* Retrieve data
* Submit data
* Update content
* Interact with APIs

Conceptually:

```text
Browser
   ↓
JavaScript
   ↓
HTTP Request
   ↓
Back-End API
   ↓
HTTP Response
   ↓
JavaScript
   ↓
DOM Updated
```

This allows web applications to dynamically retrieve information without refreshing the entire page.

---

# AJAX

**AJAX (Asynchronous JavaScript and XML)** allows JavaScript to communicate with the server asynchronously.

This means the browser can:

```text
Send Request
     ↓
Continue Running
     ↓
Receive Response
     ↓
Update Page
```

without performing a full page reload.

Despite the name, AJAX does not necessarily use XML.

Modern applications often exchange data in formats such as:

```text
JSON
```

---

# Client-Side Execution

Modern browsers contain JavaScript engines capable of executing JavaScript locally.

This means JavaScript code can run directly inside our browser without requiring the server to process every operation.

Conceptually:

```text
Server
  ↓
Sends HTML + CSS + JavaScript
  ↓
Browser
  ↓
JavaScript Engine
  ↓
Code Executed Locally
```

This makes applications faster and more interactive.

---

# JavaScript Frameworks

Modern web applications can become very complex.

For this reason, developers often use JavaScript frameworks and libraries instead of building everything from scratch.

Common examples include:

* Angular
* React
* Vue
* jQuery

These technologies help developers create:

* Dynamic interfaces
* Reusable components
* User authentication interfaces
* Forms
* Client-side routing
* Interactive functionality

---

# Pentesting Perspective

JavaScript is very important during web application penetration testing because the browser receives much of the client-side JavaScript code.

This means we can inspect it.

JavaScript files may reveal information such as:

```text
API endpoints
Hidden routes
Parameters
Internal URLs
Application logic
Authentication logic
Client-side validation
Hardcoded secrets
Interesting functions
```

For example, we may find code such as:

```javascript
fetch("/api/users")
```

This may reveal an API endpoint:

```text
/api/users
```

Even if that endpoint is not visible anywhere in the interface.

---

# Client-Side Validation

JavaScript is often used to validate user input.

Example:

```text
User Input
    ↓
JavaScript Validation
    ↓
Request Sent
```

However:

```text
Client-side validation is not a security boundary.
```

Because JavaScript runs on our machine, we can potentially:

* Modify it
* Disable it
* Bypass it
* Send requests manually

Therefore, important validation must also happen on the **server side**.

---

# JavaScript and XSS

JavaScript is especially important when studying:

```text
Cross-Site Scripting (XSS)
```

XSS vulnerabilities may allow attacker-controlled JavaScript to execute inside another user's browser.

Conceptually:

```text
Malicious Input
     ↓
Web Application
     ↓
JavaScript Executed
     ↓
Victim's Browser
```

Once JavaScript executes in the page context, it may interact with the DOM and application functionality.

This is why understanding basic JavaScript is important before studying XSS.

---

# Key Takeaways

* JavaScript provides functionality and interactivity to web applications.
* It normally runs inside the browser.
* JavaScript can also run on the server through technologies such as Node.js.
* JavaScript is loaded using the `<script>` HTML tag.
* External JavaScript files can be loaded using the `src` attribute.
* JavaScript can manipulate the DOM.
* JavaScript can dynamically update web page content.
* JavaScript can send HTTP requests to back-end services.
* AJAX enables asynchronous communication with the server.
* Modern web applications often exchange JSON data.
* JavaScript frameworks include Angular, React, Vue, and jQuery.
* Client-side validation can be bypassed and must not be trusted as a security control.
* JavaScript source code can reveal valuable information during reconnaissance.
* JavaScript knowledge is fundamental for understanding vulnerabilities such as XSS.

---

# Pentesting Mindset

When analyzing JavaScript files, we should look for:

```text
What endpoints are being called?

What parameters are being sent?

What data is received?

Is any sensitive information hardcoded?

Is security validation only performed client-side?

Are there hidden functions or routes?

How does JavaScript interact with the DOM?

Can our input reach a JavaScript execution context?
```

For web pentesting:

```text
HTML       → Understand the structure
CSS        → Recognize presentation
JavaScript → Understand the application's client-side logic
```

JavaScript is significantly more relevant than CSS because it directly interacts with the application logic, user input, HTTP requests, APIs, and the DOM.
