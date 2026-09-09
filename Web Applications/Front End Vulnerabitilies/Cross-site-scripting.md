# Cross-Site Scripting (XSS)

## Overview

**Cross-Site Scripting (XSS)** occurs when attacker-controlled JavaScript is injected into a web application and executed inside another user's browser.

XSS is similar to **HTML Injection**, but the main difference is that XSS involves **JavaScript execution**.

```text
HTML Injection
→ Inject HTML
→ Modify page structure/content

XSS
→ Inject JavaScript
→ Execute code in the browser
```

Because JavaScript runs on the client side, XSS can affect users interacting with the vulnerable application.

---

# XSS Execution Flow

A simplified XSS attack flow looks like this:

```text
User-Controlled Input
        ↓
Application Fails to Sanitize It
        ↓
Input is Inserted into the Page
        ↓
Browser Interprets JavaScript
        ↓
JavaScript Executes
```

The key idea is:

```text
Our input becomes executable code inside the browser.
```

---

# Main Types of XSS

There are three main types of XSS:

1. Reflected XSS
2. Stored XSS
3. DOM XSS

---

## Reflected XSS

**Reflected XSS** occurs when our input is sent to the application and immediately returned inside the response.

Common examples include:

* Search results
* Error messages
* URL parameters

Conceptually:

```text
Input
  ↓
Server Processes Request
  ↓
Input Returned in Response
  ↓
Browser Executes It
```

Example scenario:

```text
Search Parameter
      ↓
Search Results Page
      ↓
Input Reflected Back
```

The payload is not permanently stored.

---

## Stored XSS

**Stored XSS** occurs when malicious input is stored by the application and later displayed to users.

Common locations include:

* Comments
* Posts
* User profiles
* Messages
* Database records

Conceptually:

```text
Attacker Input
     ↓
Stored in Database
     ↓
Victim Opens Page
     ↓
Stored Input is Loaded
     ↓
JavaScript Executes
```

The important difference is:

```text
Stored XSS persists.
```

This may affect multiple users because the malicious input remains stored in the application.

---

## DOM XSS

**DOM XSS** occurs when JavaScript on the client side takes user-controlled input and inserts it into the DOM in an unsafe way.

The server may not need to process the malicious input.

Conceptually:

```text
User Input
    ↓
Client-Side JavaScript
    ↓
Unsafe DOM Operation
    ↓
DOM Modified
    ↓
JavaScript Executes
```

This connects directly with what we saw earlier:

```javascript
element.innerHTML = userInput;
```

If user-controlled input reaches an unsafe DOM sink such as `innerHTML`, it may create a DOM XSS vulnerability.

---

# Comparing the Three Types

| Type          | Where Input Is Handled | Persistence       |
| ------------- | ---------------------- | ----------------- |
| Reflected XSS | Server response        | Temporary         |
| Stored XSS    | Stored by back end     | Persistent        |
| DOM XSS       | Client-side DOM        | Usually temporary |

A simple way to remember them:

```text
Reflected → Input comes back

Stored → Input stays

DOM → Browser-side JavaScript handles it
```

---

# Example Payload

The module demonstrates a DOM XSS payload:

```html
#"><img src=/ onerror=alert(document.cookie)>
```

The important part is:

```javascript
alert(document.cookie)
```

This accesses:

```text
document.cookie
```

which represents cookies accessible to JavaScript for the current page.

The payload uses an HTML element with an event handler:

```html
<img ... onerror=...>
```

When the image fails to load, the browser triggers:

```text
onerror
```

which causes the JavaScript to execute.

---

# Understanding the Payload

The payload:

```html
#"><img src=/ onerror=alert(document.cookie)>
```

can be understood conceptually as:

```text
Break out of existing context
        ↓
Inject new HTML element
        ↓
Trigger an event
        ↓
Execute JavaScript
```

The `<img>` element attempts to load an invalid resource:

```html
src=/
```

If an error occurs, the browser executes the `onerror` handler:

```javascript
alert(document.cookie)
```

---

# `document.cookie`

JavaScript can access cookies through:

```javascript
document.cookie
```

In the HTB example, the value is displayed using:

```javascript
alert(document.cookie)
```

This is useful as a simple demonstration that JavaScript execution is possible.

Conceptually:

```text
XSS
 ↓
JavaScript Execution
 ↓
document.cookie
 ↓
Cookie Value Accessed
```

---

# Why Cookies Matter

Cookies are commonly used to maintain application sessions.

Conceptually:

```text
Login
  ↓
Server Creates Session
  ↓
Browser Receives Cookie
  ↓
Cookie Identifies the Session
```

If sensitive session information is accessible to injected JavaScript, an XSS vulnerability may potentially be used to compromise a user's authenticated session.

---

# XSS and the DOM

XSS becomes easier to understand when we connect it with the DOM.

```text
HTML
 ↓
DOM
 ↓
JavaScript
 ↓
DOM Manipulation
```

If our input reaches the DOM without proper sanitization:

```text
User Input
    ↓
DOM
    ↓
Browser Interprets Input
    ↓
JavaScript Execution
```

This is especially relevant for DOM XSS.

---

# HTML Injection vs XSS

The two vulnerabilities are closely related.

## HTML Injection

```html
<h1>Injected Content</h1>
```

The browser renders new HTML.

## XSS

```html
<img src=x onerror=alert(1)>
```

The browser executes JavaScript.

The main distinction is:

```text
HTML Injection → Content Injection

XSS → Code Execution in Browser
```

A page vulnerable to HTML Injection may also be worth testing for XSS if the application does not properly sanitize user-controlled input.

---

# Possible Impact

XSS may potentially be used to:

* Access information available to JavaScript
* Modify page content
* Interact with the DOM
* Perform actions in the context of the victim
* Target authenticated users
* Target administrators
* Compromise application sessions

The actual impact depends on the application's security controls and what information is accessible to JavaScript.

---

# Key Takeaways

* XSS involves injecting JavaScript into a web application.
* The injected code executes inside the browser.
* XSS mainly affects the client side.
* The three main types are:

  * Reflected XSS
  * Stored XSS
  * DOM XSS
* Reflected XSS immediately returns user input in a response.
* Stored XSS stores malicious input before displaying it later.
* DOM XSS happens through unsafe client-side DOM manipulation.
* `innerHTML` can be dangerous when used with untrusted input.
* `document.cookie` can access cookies available to JavaScript.
* HTML Injection and XSS are related, but XSS involves JavaScript execution.

---

# Pentesting Mindset

When testing user-controlled input, we should ask:

```text
Where does our input go?

Is it reflected in the response?

Is it stored?

Does JavaScript process it?

Does it reach the DOM?

Is innerHTML being used?

Can HTML be injected?

Can JavaScript execute?

Does the behavior affect other users?
```

A useful mental model is:

```text
Input
 ↓
Source
 ↓
Application Processing
 ↓
Sink
 ↓
Browser Interpretation
 ↓
Possible JavaScript Execution
```

For DOM XSS specifically:

```text
User-Controlled Source
        ↓
JavaScript
        ↓
Unsafe DOM Sink
        ↓
XSS
```
