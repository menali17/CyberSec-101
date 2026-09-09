# HTML

## Overview

**HTML (HyperText Markup Language)** is the main language used to define the structure and basic elements of a web page.

HTML can contain elements such as:

* Titles
* Headings
* Paragraphs
* Forms
* Images
* Links
* Scripts
* Styles

The browser interprets the HTML document and displays its contents to us.

---

# Basic HTML Structure

A simple HTML document may look like this:

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Page Title</title>
    </head>
    <body>
        <h1>A Heading</h1>
        <p>A Paragraph</p>
    </body>
</html>
```

The elements are organized in a **tree structure**:

```text
document
 └── html
     ├── head
     │   └── title
     └── body
         ├── h1
         └── p
```

The `<html>` element contains the other HTML elements of the page.

---

# HTML Tags and Elements

HTML elements are generally defined using **opening and closing tags**.

Example:

```html
<p>A Paragraph</p>
```

Here:

```text
<p>            → Opening tag
A Paragraph    → Content
</p>           → Closing tag
```

Together, these parts form an HTML element.

Another example:

```html
<h1>A Heading</h1>
```

---

## Nested Elements

HTML elements can contain other HTML elements.

Example:

```html
<html>
    <body>
        <p>Hello</p>
    </body>
</html>
```

The relationship can be represented as:

```text
html
 └── body
     └── p
```

Understanding these relationships is important because HTML documents are organized hierarchically.

---

# IDs and Classes

HTML tags may contain attributes such as **id** and **class**.

Example:

```html
<p id="para1">Paragraph</p>
```

An ID identifies a specific element.

Another example:

```html
<p class="red-paragraphs">Paragraph</p>
```

Classes can be used to group elements.

These attributes are useful for locating and formatting HTML elements, especially when working with CSS and JavaScript.

---

# Main HTML Sections

## `<head>`

The `<head>` element usually contains information that is not directly displayed as the main page content.

Example:

```html
<head>
    <title>Page Title</title>
</head>
```

Common elements inside `<head>` include:

* Page title
* Style information
* Other document information

---

## `<body>`

The `<body>` contains the main elements that are displayed on the page.

Example:

```html
<body>
    <h1>A Heading</h1>
    <p>A Paragraph</p>
</body>
```

Most of the content we interact with is located inside the body.

---

## `<style>`

The `<style>` element contains CSS code used to control the appearance of the page.

Example:

```html
<style>
    p {
        color: blue;
    }
</style>
```

---

## `<script>`

The `<script>` element contains or loads JavaScript code.

Example:

```html
<script>
    // JavaScript code
</script>
```

JavaScript can be used to interact with and modify page elements.

---

# URL Encoding

**URL Encoding**, also called **Percent-Encoding**, is used to represent characters that cannot safely appear directly inside a URL.

URLs mainly use the ASCII character set.

Characters that need encoding are represented using:

```text
%
+
two hexadecimal digits
```

For example:

```text
'  → %27
```

A space may be represented as:

```text
Space → %20
```

or, in some cases:

```text
Space → +
```

---

## Common URL Encodings

| Character | Encoding |
| --------- | -------- |
| Space     | `%20`    |
| `!`       | `%21`    |
| `"`       | `%22`    |
| `#`       | `%23`    |
| `$`       | `%24`    |
| `%`       | `%25`    |
| `&`       | `%26`    |
| `'`       | `%27`    |
| `(`       | `%28`    |
| `)`       | `%29`    |

Example:

```text
Hello World
```

may become:

```text
Hello%20World
```

---

# Why URL Encoding Matters

When interacting with web applications, some characters may need to be encoded before they can be sent inside URLs.

For example:

```text
'
```

becomes:

```text
%27
```

Knowing how encoding works helps us understand what data is actually being transmitted in requests.

Tools such as **Burp Suite** can encode and decode different types of data.

---

# Document Object Model — DOM

The **DOM (Document Object Model)** represents the structure of a document in a way that programs and scripts can access and modify.

The DOM allows scripts to dynamically interact with:

* Content
* Structure
* Style

The DOM can be viewed as a tree representing the HTML document.

Example:

```text
document
 └── html
     ├── head
     │   └── title
     └── body
         ├── h1
         └── p
```

---

# DOM Types

The DOM standard is divided into three main parts:

* **Core DOM** — Standard model for all document types
* **XML DOM** — Standard model for XML documents
* **HTML DOM** — Standard model for HTML documents

For web applications, we mainly work with the **HTML DOM**.

---

# Accessing DOM Elements

DOM elements can be referenced and manipulated through scripts.

For example, parts of the document may be accessed through the document structure.

Conceptually:

```text
document
document.head
document.body
```

HTML elements can also be located using:

* ID
* Tag name
* Class name

For example:

```html
<p id="para1">Hello</p>
```

The element can be identified through its ID:

```text
para1
```

---

# DOM and Web Pentesting

Understanding the DOM is important during web application testing because it allows us to understand where page elements are located and how they interact.

When inspecting a web application, we may examine elements such as:

```text
Inputs
Forms
Buttons
Links
Scripts
Hidden elements
```

Knowing the HTML structure makes it easier to inspect specific elements in the browser.

---

# DOM and XSS

The DOM is also important when dealing with front-end vulnerabilities such as:

```text
Cross-Site Scripting (XSS)
```

If a vulnerability allows us to execute JavaScript in the browser, the script may interact with the DOM.

This can include:

```text
Reading elements
Modifying elements
Creating new elements
Changing page content
```

Conceptually:

```text
XSS
 ↓
JavaScript Execution
 ↓
DOM Manipulation
 ↓
Page Content / Elements Changed
```

---

# Key Takeaways

* HTML defines the basic structure of web pages.
* HTML documents are organized as a tree.
* Elements normally use opening and closing tags.
* HTML elements can contain other elements.
* IDs and classes help identify and organize elements.
* The `<head>` contains document-related information.
* The `<body>` contains the main visible page content.
* `<style>` contains CSS.
* `<script>` contains or loads JavaScript.
* URLs may require characters to be encoded using **Percent-Encoding**.
* `%20` commonly represents a space.
* `%27` represents a single quote.
* The **DOM** represents the structure of an HTML document.
* DOM elements can be accessed by ID, class, or tag name.
* Understanding the DOM is important when analyzing front-end vulnerabilities such as XSS.

---

# Pentesting Perspective

When we inspect a web page, we should not only look at what is visually displayed.

We should also inspect the HTML structure behind it.

```text
Web Page
   ↓
HTML
   ↓
DOM
   ↓
Elements
   ↓
Inputs / Forms / Scripts / Links
```

A useful mindset is:

```text
What elements exist?

Which elements accept our input?

Are there hidden elements?

Which scripts interact with these elements?

Can we modify the DOM?

How is our input represented or encoded?
```

Understanding HTML and the DOM gives us the foundation needed to analyze how front-end web applications work and how vulnerabilities such as XSS can interact with page elements.
