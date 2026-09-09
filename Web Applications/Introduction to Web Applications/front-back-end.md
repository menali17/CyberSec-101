# Front End vs. Back End

## Overview

Web application development is usually divided into two main areas:

* **Front End**
* **Back End**

A developer who works with both areas is commonly called a **Full Stack Developer**.

Although both parts are essential to a web application, they perform very different roles.

---

# Front End

The **front end** is the part of the web application that runs on the client side, usually inside our web browser.

It contains everything we can see and interact with.

The three main front-end technologies are:

```text
HTML
CSS
JavaScript
```

---

## HTML

**HTML — HyperText Markup Language**

HTML defines the structure and content of the page.

Examples include:

* Titles
* Paragraphs
* Buttons
* Forms
* Links
* Images

Example:

```html
<h1>Hack The Box</h1>
<p>Welcome to HTB Academy</p>
```

We can think of HTML as the **structure or skeleton** of the page.

---

## CSS

**CSS — Cascading Style Sheets**

CSS controls the appearance and design of the page.

It can define:

* Colors
* Fonts
* Sizes
* Layout
* Animations
* Positioning

We can think of CSS as the **visual design** of the application.

---

## JavaScript

JavaScript controls much of the interactive behavior of a web page.

It may handle things such as:

* Button actions
* Dynamic content
* Form validation
* Animations
* Requests to the server
* Changes to page elements

We can think of JavaScript as the **behavior and interaction layer**.

---

## Front-End Trinity

A simple way to remember the three technologies is:

```text
HTML       → Structure
CSS        → Appearance
JavaScript → Behavior
```

---

## Front-End Optimization

Modern front-end applications should work across:

* Different browsers
* Different operating systems
* Desktop devices
* Mobile devices
* Different screen sizes

A badly optimized front end may make an application appear slow even when the web server and network are working correctly.

Other areas associated with front-end development include:

* Visual Web Design
* User Interface — UI
* User Experience — UX

---

# Back End

The **back end** contains the core functionality of a web application.

It runs on the server side and is usually not directly visible to us.

Without a back end, many web applications would be little more than static pages.

The back end is responsible for processing requests, executing application logic, interacting with databases, and returning results to the client.

---

# Main Back-End Components

There are four main back-end components:

1. Back-End Servers
2. Web Servers
3. Databases
4. Development Frameworks

---

## Back-End Servers

A back-end server provides the hardware and operating system environment where the application's server-side components run.

Common environments include:

```text
Linux
Windows
Containers
```

The server may host:

* Web servers
* Application code
* Databases
* APIs
* Services

---

## Web Servers

A **web server** receives and handles HTTP connections and requests.

Common examples include:

```text
Apache
NGINX
IIS
```

Conceptually:

```text
Browser
   |
   | HTTP Request
   v
Web Server
   |
   | Processes / forwards request
   v
Web Application
```

---

## Databases

Databases store and retrieve application data.

### Relational Databases

Examples:

```text
MySQL
MSSQL
Oracle
PostgreSQL
```

Relational databases usually organize information into:

```text
Tables
Rows
Columns
Relationships
```

### Non-Relational Databases

Examples include:

```text
MongoDB
NoSQL Databases
```

The application communicates with the database whenever it needs to store, retrieve, update, or delete information.

---

## Development Frameworks

Development frameworks provide tools and structures that help developers build the application's back-end logic.

Examples:

| Framework | Language            |
| --------- | ------------------- |
| Laravel   | PHP                 |
| ASP.NET   | C#                  |
| Spring    | Java                |
| Django    | Python              |
| Express   | NodeJS / JavaScript |

---

# Back-End Separation and Containers

Back-end components do not necessarily have to run on the same server.

They may be separated across:

* Different physical servers
* Virtual machines
* Containers

Docker can be used to isolate individual components.

For example:

```text
Container 1
└── Web Application

Container 2
└── Database
```

This provides **logical separation**.

If one component is compromised, isolation may reduce the impact on other components.

---

# Back-End Responsibilities

Some common back-end responsibilities include:

* Developing application logic
* Implementing application functionality
* Managing databases
* Developing libraries
* Implementing business requirements
* Creating APIs
* Integrating external systems
* Connecting cloud services
* Communicating with front-end components

---

# Front End and Back End Communication

The front end and back end commonly communicate using **HTTP requests** and **APIs**.

A simplified interaction may look like this:

```text
Front End
   |
   | HTTP Request
   v
Web Server
   |
   v
Back-End Logic
   |
   v
Database
   |
   | Data
   v
Back End
   |
   | HTTP Response
   v
Front End
```

Example:

```text
We click "Login"
      ↓
Front end sends credentials
      ↓
Back end receives request
      ↓
Database checks account
      ↓
Back end creates response
      ↓
Front end displays result
```

---

# Securing the Front End and Back End

Not having access to back-end source code does not mean an application cannot be attacked.

We can still interact with the application through inputs and HTTP requests.

Improper processing of user-controlled input may lead to vulnerabilities such as:

* SQL Injection
* Command Injection

---

## SQL Injection

SQL Injection occurs when input supplied by us is improperly handled before being used in a database query.

Conceptually:

```text
User Input
    ↓
Web Application
    ↓
SQL Query
    ↓
Database
```

If the application does not properly validate or safely handle the input, we may be able to manipulate the database query.

Possible consequences include:

* Reading unauthorized data
* Modifying data
* Authentication bypass
* Database compromise

---

## Command Injection

Command Injection occurs when user-controlled input reaches an operating system command in an unsafe way.

Conceptually:

```text
User Input
    ↓
Application
    ↓
Operating System Command
```

This can potentially allow unauthorized command execution on the server.

---

# Whitebox Pentesting

In **Whitebox Pentesting**, we have access to internal information about the application.

This may include:

* Source code
* Architecture
* Configuration
* Credentials
* Documentation

Since front-end source code is normally sent directly to the browser, we can usually inspect it.

For example, we can review:

```text
HTML
CSS
JavaScript
```

This allows us to perform source-code analysis and search for vulnerabilities.

---

# Blackbox Pentesting

In **Blackbox Pentesting**, we do not have access to the application's internal source code.

This is commonly the situation when testing back-end components.

We interact with the application externally and attempt to understand its behavior.

```text
Blackbox
────────────────────
We see:
Requests
Responses
Errors
Application behavior

We usually do not see:
Source code
Internal logic
Database queries
Server configuration
```

---

## Obtaining Back-End Source Code

Although back-end source code is normally hidden, some situations may expose it.

Examples include:

* Open-source applications
* Misconfigured servers
* Local File Inclusion — LFI
* Exposed backups or files

If we obtain back-end source code, we may discover:

* Credentials
* Passwords
* API keys
* Hidden functionality
* Vulnerable functions
* Database information
* Internal paths

This can turn part of a blackbox assessment into something closer to a whitebox analysis.

---

# Common Developer Security Mistakes

Several development mistakes can create security vulnerabilities.

Some important examples include:

* Allowing invalid data into the database
* Treating security as the last development step
* Storing passwords in plain text
* Using weak passwords
* Storing sensitive data without encryption
* Trusting client-side controls
* Trusting third-party code
* Hard-coding accounts or credentials
* Improper SQL handling
* Remote File Inclusion
* Insecure data handling
* Incorrect cryptography
* WAF misconfigurations

---

## Do Not Trust the Client Side

One of the most important principles is:

```text
Never trust the client.
```

Anything running on our side can potentially be modified.

This includes:

```text
HTML
JavaScript
Cookies
HTTP Headers
Request Parameters
Form Fields
URLs
```

For example, if JavaScript prevents us from submitting a certain value:

```text
Browser Validation
```

we may bypass the front end and send the request manually.

Therefore, important security validation must also happen on the **server side**.

---

# OWASP Top 10

The section introduces the OWASP Top 10 categories:

1. **Broken Access Control**
2. **Cryptographic Failures**
3. **Injection**
4. **Insecure Design**
5. **Security Misconfiguration**
6. **Vulnerable and Outdated Components**
7. **Identification and Authentication Failures**
8. **Software and Data Integrity Failures**
9. **Security Logging and Monitoring Failures**
10. **Server-Side Request Forgery — SSRF**

These categories represent important classes of security problems that we will encounter throughout web application penetration testing.

---

# Key Takeaways

* The **front end** runs primarily inside the browser.
* The main front-end technologies are **HTML, CSS, and JavaScript**.
* The **back end** processes application logic on the server.
* Back-end components include web servers, databases, frameworks, and operating systems.
* Apache, NGINX, and IIS are common web servers.
* MySQL, PostgreSQL, MSSQL, and Oracle are common relational databases.
* Laravel, Django, Spring, ASP.NET, and Express are examples of back-end frameworks.
* Back-end components may be separated into different servers or containers.
* APIs commonly connect the front end to back-end functionality.
* We can inspect front-end code directly because it is sent to the browser.
* Back-end source code is normally hidden.
* **Whitebox testing** involves access to internal implementation details.
* **Blackbox testing** involves testing the application externally.
* User-controlled input must never be blindly trusted.
* Client-side security controls alone are not sufficient.
* Many web security issues fall under the **OWASP Top 10**.

---

# Pentesting Perspective

When interacting with a web application, what appears in the browser is only one part of the system.

```text
             FRONT END
                 |
        HTML / CSS / JavaScript
                 |
                 | HTTP / APIs
                 v
             BACK END
                 |
        Web Server / Logic
                 |
                 v
              Database
```

As pentesters, we should constantly ask:

```text
What information is controlled by us?

Where is that information sent?

How does the server process it?

Does the server trust something it should not?

Can we modify the request?

Can we reach another component through the application?
```

Understanding the boundary between the **front end** and the **back end** is fundamental because many web vulnerabilities exist precisely where data crosses this boundary.
