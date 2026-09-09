# Web Servers

## Overview

A **web server** is an application running on a back-end server that handles HTTP traffic from client browsers.

It is responsible for:

* Receiving HTTP requests
* Routing requests to the correct resource
* Processing or forwarding requests
* Returning HTTP responses to the client

Web servers commonly listen on:

```text
HTTP  → TCP/80
HTTPS → TCP/443
```

A simplified flow is:

```text
Browser
   ↓
HTTP Request
   ↓
Web Server
   ↓
Requested Resource / Application
   ↓
HTTP Response
   ↓
Browser
```

---

# HTTP Responses

Web servers respond to requests using HTTP status codes.

Some common codes are:

| Code                        | Meaning                                  |
| --------------------------- | ---------------------------------------- |
| `200 OK`                    | Request succeeded                        |
| `301 Moved Permanently`     | Resource permanently moved               |
| `302 Found`                 | Resource temporarily moved               |
| `400 Bad Request`           | Invalid request syntax                   |
| `401 Unauthorized`          | Authentication is required               |
| `403 Forbidden`             | Access is denied                         |
| `404 Not Found`             | Resource does not exist                  |
| `405 Method Not Allowed`    | HTTP method is not allowed               |
| `408 Request Timeout`       | Request timed out                        |
| `500 Internal Server Error` | Server encountered an internal error     |
| `502 Bad Gateway`           | Invalid response from an upstream server |
| `504 Gateway Timeout`       | Upstream server did not respond in time  |

A useful way to group them is:

```text
2xx → Success
3xx → Redirection
4xx → Client-side request errors
5xx → Server-side errors
```

---

# Web Server Workflow

A web server can receive different types of data through HTTP requests, such as:

* Text
* JSON
* Form data
* Binary data
* Uploaded files

Conceptually:

```text
Client
   ↓
HTTP Request
   ↓
Web Server
   ↓
Route Request
   ↓
Application / File / API
   ↓
HTTP Response
```

The web server connects the user to the appropriate resource inside the web application.

---

# Using cURL

`cURL` is a command-line utility used to send requests to web servers.

To retrieve only HTTP headers:

```bash
curl -I https://academy.hackthebox.com
```

Example response:

```text
HTTP/2 200
content-type: text/html; charset=UTF-8
```

The `-I` flag displays the response headers.

---

## Retrieving Page Content

Without `-I`:

```bash
curl https://academy.hackthebox.com
```

the server returns the page content.

Example:

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="utf-8" />
    <title>Cyber Security Training : HTB Academy</title>
</head>
```

This allows us to interact with a web server directly without using a browser.

---

# Common Web Servers

Three common web servers are:

```text
Apache
NGINX
IIS
```

Other technologies mentioned include:

* Apache Tomcat
* Node.js

---

# Apache

**Apache**, also known as `httpd`, is a popular open-source web server.

It is commonly associated with Linux systems but can also run on Windows and macOS.

Apache is frequently used with PHP.

Example stack:

```text
Linux
  ↓
Apache
  ↓
PHP
  ↓
MySQL
```

Apache can also support technologies such as:

* .NET
* Python
* Perl
* Bash through CGI

Its functionality can be extended through modules.

For example:

```text
mod_php
```

can be used to support PHP.

### Important Characteristics

* Open source
* Modular
* Well documented
* Widely used
* Supports multiple programming languages

---

# NGINX

**NGINX** is another popular open-source web server.

It is designed to efficiently handle many concurrent connections while using relatively low CPU and memory resources.

Its architecture is well suited for high-traffic web applications.

### Important Characteristics

```text
High concurrency
Low resource usage
Asynchronous architecture
Open source
High performance
```

NGINX is commonly used by large and high-traffic web applications.

---

# IIS

**IIS (Internet Information Services)** is Microsoft's web server.

It mainly runs on:

```text
Windows Server
```

It is commonly used with applications built using:

```text
.NET
```

but can also host other technologies such as PHP.

IIS can also provide other services, including FTP.

---

## IIS and Active Directory

An important feature of IIS is its integration with Microsoft environments.

It can use:

```text
Windows Authentication
```

to authenticate users through **Active Directory**.

Conceptually:

```text
User
 ↓
Windows Authentication
 ↓
Active Directory
 ↓
IIS
 ↓
Web Application
```

This makes IIS particularly common in organizations that heavily rely on Windows Server and Active Directory.

---

# Apache vs NGINX vs IIS

| Web Server | Common Environment | Main Characteristics                                                |
| ---------- | ------------------ | ------------------------------------------------------------------- |
| Apache     | Linux              | Modular, widely used, commonly paired with PHP                      |
| NGINX      | Linux / Unix-like  | High performance, asynchronous, handles many concurrent connections |
| IIS        | Windows Server     | Microsoft ecosystem, .NET and Active Directory integration          |

---

# Other Web Servers

## Apache Tomcat

**Apache Tomcat** is commonly associated with Java web applications.

```text
Java Application
      ↓
Apache Tomcat
```

---

## Node.js

Node.js allows JavaScript to run on the server side.

It can therefore be used to build back-end web applications and HTTP services.

```text
JavaScript
    ↓
Node.js
    ↓
Back-End Application
```

---

# Back-End Server vs Web Server

It is important not to confuse these concepts.

```text
Back-End Server
│
├── Operating System
├── Web Server
├── Web Application
└── Database
```

The **back-end server** is the system or environment hosting the application.

The **web server** is one of the applications running inside that environment.

For example:

```text
Ubuntu Server
   ↓
NGINX
   ↓
Web Application
```

Here:

```text
Ubuntu Server → Back-End Server / OS environment
NGINX         → Web Server
```

---

# Pentesting Perspective

Identifying the web server can give us useful information about the application's technology stack.

We may try to determine:

```text
Which web server is running?

Which version is running?

Is it Apache, NGINX, IIS, or another server?

Which operating system may be behind it?

Which technologies are commonly associated with it?

Are there unusual HTTP responses?

Which HTTP methods are accepted?
```

HTTP status codes can also provide useful information during enumeration.

For example:

```text
200 → Resource exists and is accessible

403 → Resource may exist, but access is forbidden

404 → Resource was not found

500 → Something failed on the server
```

A `403` can therefore be especially interesting because it may indicate that the resource exists even though we cannot currently access it.

---

# Key Takeaways

* A web server handles HTTP traffic between clients and web applications.
* HTTP commonly uses TCP port `80`.
* HTTPS commonly uses TCP port `443`.
* Web servers respond using HTTP status codes.
* `2xx` means success.
* `3xx` means redirection.
* `4xx` means client/request-related errors.
* `5xx` means server-related errors.
* `curl -I` retrieves HTTP response headers.
* `curl <URL>` can retrieve page content.
* Apache, NGINX, and IIS are common web servers.
* Apache is modular and commonly associated with PHP.
* NGINX focuses on efficient handling of concurrent requests.
* IIS integrates strongly with Windows Server and Active Directory.
* The web server is only one component of the larger back-end environment.

---

# Pentesting Mindset

When we connect to a web application, we should pay attention to:

```text
Ports
HTTP headers
Status codes
Server type
Server version
Allowed methods
Redirects
Error responses
```

A useful mental model is:

```text
Client
  ↓
HTTP Request
  ↓
Web Server
  ↓
Application
  ↓
Response
  ↓
Status Code + Headers + Content
```
