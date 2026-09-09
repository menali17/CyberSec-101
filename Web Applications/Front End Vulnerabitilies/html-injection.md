# HTML Injection

## Overview

A major part of front-end security is properly **validating and sanitizing user input**.

Validation and sanitization may happen on the back end, but some input is processed entirely on the front end.

For this reason, user input should be properly handled on both:

```text
Front End
Back End
```

---

# What is HTML Injection?

**HTML Injection** occurs when unfiltered user input is inserted into a web page and interpreted as HTML.

This may happen when input is:

* Retrieved from a database
* Displayed from a previous user submission
* Directly processed by JavaScript
* Inserted into the DOM without sanitization

Conceptually:

```text
User Input
    ↓
No Sanitization
    ↓
Inserted into Page
    ↓
Browser Interprets Input as HTML
```

---

# Why It Happens

The vulnerability exists when the user has control over content that is rendered as part of the page.

Instead of treating input only as text:

```text
Enzo
```

the application may also interpret:

```html
<h1>Enzo</h1>
```

as actual HTML.

This allows us to inject HTML elements into the page.

---

# Possible Impact

HTML Injection can be used to:

* Modify page content
* Change the appearance of the page
* Insert malicious advertisements
* Add fake forms
* Perform web page defacement
* Trick users into submitting sensitive information

For example, an attacker could inject a fake login form.

```text
Injected Login Form
        ↓
Victim enters credentials
        ↓
Credentials sent to malicious server
```

---

# Web Page Defacement

**Defacement** consists of modifying the visible appearance of a web page through injected HTML.

Possible changes include:

```text
Changing text
Changing images
Changing page layout
Adding advertisements
Replacing page content
```

Although this may not directly compromise the back-end server, it can cause significant reputational damage.

---

# Vulnerable Example

Consider the following page:

```html
<!DOCTYPE html>
<html>

<body>
    <button onclick="inputFunction()">Click to enter your name</button>
    <p id="output"></p>

    <script>
        function inputFunction() {
            var input = prompt("Please enter your name", "");

            if (input != null) {
                document.getElementById("output").innerHTML =
                    "Your name is " + input;
            }
        }
    </script>
</body>

</html>
```

The important line is:

```javascript
document.getElementById("output").innerHTML = "Your name is " + input;
```

The application takes our input and places it directly inside the page.

There is no sanitization before the input is passed to:

```text
innerHTML
```

---

# Understanding `innerHTML`

`innerHTML` allows JavaScript to modify the HTML content inside an element.

Example:

```javascript
document.getElementById("output").innerHTML = "Hello";
```

This changes the content of the selected element.

However, if user-controlled input is inserted directly:

```javascript
document.getElementById("output").innerHTML = input;
```

the browser may interpret our input as HTML.

Conceptually:

```text
Our Input
   ↓
innerHTML
   ↓
HTML Parser
   ↓
New DOM Elements
```

---

# Testing for HTML Injection

A simple way to test for HTML Injection is to submit a small HTML element and check whether the browser renders it.

For example:

```html
<h1>Test</h1>
```

If the page displays a large heading instead of showing the literal characters:

```text
<h1>Test</h1>
```

then the application is interpreting our input as HTML.

---

# HTB Example

The module uses the following payload:

```html
<style>
    body {
        background-image: url('https://academy.hackthebox.com/images/logo.svg');
    }
</style>
```

If the application is vulnerable, the injected `<style>` element becomes part of the page.

The result is:

```text
User Input
    ↓
<style> injected
    ↓
Browser processes CSS
    ↓
Page background changes
```

This confirms that our input is being interpreted as HTML rather than normal text.

---

# HTML Injection vs Normal Input

Safe behavior:

```text
Input:
<h1>Hello</h1>

Output:
<h1>Hello</h1>
```

The input is displayed as text.

Vulnerable behavior:

```text
Input:
<h1>Hello</h1>

Output:
Hello
```

where `Hello` is rendered as an actual HTML heading.

---

# HTML Injection and XSS

A page vulnerable to HTML Injection may also be interesting when testing for:

```text
Cross-Site Scripting (XSS)
```

Both problems are related to unsafe handling of user-controlled input.

The important concept is:

```text
Untrusted Input
      ↓
Inserted into DOM
      ↓
Browser Interprets It
```

HTML Injection focuses on injecting **HTML content**, while XSS involves the execution of JavaScript.

---

# Key Takeaways

* HTML Injection occurs when user input is rendered as HTML without proper sanitization.
* Input may be processed on either the front end or the back end.
* User-controlled input should not be trusted.
* JavaScript can introduce HTML Injection by placing unsafe input into the DOM.
* `innerHTML` is important when analyzing this type of vulnerability.
* HTML Injection may allow us to modify the appearance or structure of a page.
* It may be used to create fake forms or perform page defacement.
* Simple HTML payloads can be used to determine whether input is interpreted as HTML.
* HTML Injection may indicate that further testing for XSS is worthwhile.

---

# Pentesting Mindset

When we find user input being displayed on a page, we should ask:

```text
Where does our input appear?

Is it displayed as text or interpreted as HTML?

Is the input sanitized?

Is JavaScript inserting it into the DOM?

Is innerHTML being used?

Can we inject new HTML elements?

Could this behavior also lead to XSS?
```

A particularly important pattern to recognize is:

```javascript
element.innerHTML = userInput;
```

because user-controlled input is being directly inserted into the HTML structure of the page.
