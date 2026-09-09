# Web Applications — Introduction

## Web Applications

A **web application** is an interactive application that runs inside a web browser.

Most web applications follow a **client-server architecture**, which is divided into two main sides:

* **Client-side (Front End):** What we see and interact with in the browser.
* **Server-side (Back End):** The application's logic, web server, and interaction with databases or other services.

```text
Browser / Client
       |
       | HTTP Requests
       v
   Web Server
       |
       v
Application Logic
       |
       v
    Database
```

Examples of web applications include:

* Gmail
* Amazon
* Google Docs

---

## Web Applications vs. Websites

Traditional websites are usually **static**.

Their content does not change based on our interaction, and developers normally need to manually modify the page to update its content.

This type of website is associated with **Web 1.0**.

Web applications, on the other hand, provide **dynamic and interactive content** based on user actions.

They are commonly associated with **Web 2.0**.

### Main Differences

**Traditional Website**

```text
Static content
Same information for most users
Limited functionality
```

**Web Application**

```text
Dynamic content
Interactive
Can behave differently for each user
Provides application functionality
```

Web applications are also commonly:

* Modular
* Compatible with different display sizes
* Platform-independent

---

## Web Applications vs. Native Applications

Web applications are generally **platform-independent** because they run inside browsers.

We usually do not need to install them locally.

Another important advantage is **version unity**.

Since the application is hosted on a central web server, developers can update it once and all users immediately access the updated version.

### Web Application Advantages

* Platform-independent
* No local installation required
* Centralized updates
* Lower maintenance requirements
* Same application version for all users

### Native Application Advantages

Native applications usually provide:

* Better performance
* Better integration with the operating system
* Access to native OS libraries
* Greater access to local hardware

Modern **hybrid applications** and **Progressive Web Applications (PWAs)** attempt to combine characteristics of both approaches.

---

## Web Application Distribution

Web applications may be either **open source** or **closed source**.

### Open Source

Examples:

* WordPress
* OpenCart
* Joomla

Their source code is publicly available and can usually be modified.

### Closed Source

Examples:

* Wix
* Shopify
* DotNetNuke

These applications are proprietary and are commonly sold or provided through subscription models.

---

# Security Risks of Web Applications

Web applications provide a large **attack surface** because they are often publicly accessible through the Internet.

As applications become more complex, the possibility of introducing security vulnerabilities also increases.

A successful web application attack may compromise:

* Sensitive user information
* Corporate data
* Databases
* Web servers
* Other systems connected to the application

For this reason, organizations should regularly test their web applications and apply secure coding practices throughout the development lifecycle.

---

## Web Application Penetration Testing

To properly test a web application, we need to understand:

* How the application works
* How it was developed
* Which technologies it uses
* How its components communicate
* Which risks exist at each layer

A common reference for web application testing is the **OWASP Web Security Testing Guide (WSTG)**.

---

## Front-End Analysis

The main front-end technologies are:

```text
HTML
CSS
JavaScript
```

These are sometimes called the **front-end trinity**.

During an assessment, we should inspect front-end components and look for vulnerabilities such as:

* Sensitive Data Exposure
* Cross-Site Scripting (XSS)

We should also analyze the communication between:

```text
Browser <----> Web Server
```

This allows us to identify technologies, parameters, requests, application behavior, and potential attack vectors.

---

## Authentication Perspectives

When possible, we should test the application from both perspectives:

```text
Unauthenticated User
```

and

```text
Authenticated User
```

Some functionality and vulnerabilities may only be accessible after authentication.

Testing both perspectives improves our coverage of the application's attack surface.

---

# Common Web Application Vulnerabilities

## SQL Injection

**SQL Injection (SQLi)** occurs when user-controlled input is handled unsafely inside database queries.

Possible consequences include:

* Accessing sensitive information
* Reading database contents
* Writing or reading files
* Authentication bypass
* Remote Code Execution in some scenarios

SQL Injection may also expose information that can later be used in other attacks.

---

## File Inclusion

A **File Inclusion** vulnerability may allow us to make the application load unintended files.

Possible consequences include:

* Reading application source code
* Reading configuration files
* Discovering hidden functionality
* Exposing credentials
* Potential Remote Code Execution

---

## Unrestricted File Upload

An **Unrestricted File Upload** vulnerability occurs when an application does not properly restrict which files can be uploaded.

For example, a profile picture functionality should normally allow image files:

```text
.jpg
.png
.webp
```

If arbitrary files are accepted, malicious code may potentially be uploaded to the server.

Depending on how the server handles the uploaded file, this may lead to:

```text
Remote Code Execution (RCE)
```

---

## Insecure Direct Object Reference (IDOR)

**IDOR** occurs when an application exposes references to objects without properly verifying authorization.

Example:

```text
/user/701/edit-profile
```

If we change the ID:

```text
/user/702/edit-profile
```

and gain access to another user's profile, the application has failed to properly verify authorization.

### Important Concept

```text
Knowing an object's identifier
does not mean
we are authorized to access it.
```

---

## Broken Access Control

**Broken Access Control** occurs when an application fails to correctly enforce what each user is allowed to do.

For example, suppose account registration sends:

```text
username=bjones
password=Welcome1
email=bjones@inlanefreight.local
roleid=3
```

If we can modify:

```text
roleid=3
```

to:

```text
roleid=1
```

and register an administrator account, the application has trusted a client-controlled value that should have been validated on the server.

### Important Security Principle

```text
Client-controlled data cannot be trusted.
```

Requests sent by a browser can be intercepted and modified.

Authorization decisions must therefore be enforced by the **server**.

---

# Attack Chaining

A single vulnerability may be combined with other techniques to produce a much larger impact.

This is known as **attack chaining**.

Example:

```text
SQL Injection
      ↓
Extract Active Directory usernames
      ↓
Password Spraying
      ↓
Compromise a corporate account
      ↓
Access VPN / Email
      ↓
Potential internal network access
```

This demonstrates how a web application vulnerability may become the initial entry point into a larger corporate infrastructure.

---

# Key Takeaways

* Web applications usually follow a **client-server architecture**.
* The **front end** runs in the browser.
* The **back end** contains application logic and interacts with databases and other services.
* Web applications are dynamic and interactive.
* Their complexity creates a large **attack surface**.
* We should analyze both front-end and back-end interactions.
* We should test both authenticated and unauthenticated functionality.
* User-controlled input should never automatically be trusted.
* Authorization must always be validated on the server.
* Individual vulnerabilities can often be combined through **attack chaining**.

## Pentesting Mindset

When analyzing a web application, we should think beyond what is visually displayed in the browser.

```text
Client
   ↓
Web Application
   ↓
Web Server
   ↓
Application Logic
   ↓
Database / Internal Services
```

Our goal is to understand how data flows through these components and identify where user-controlled input may affect the application's behavior.

A strong understanding of web applications is essential because web vulnerabilities frequently appear during penetration tests, HTB machines, and real-world security assessments.
