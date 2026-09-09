# Back End Servers

## Overview

A **back-end server** is the physical or virtual system that hosts the components required for a web application to operate.

It includes:

* Hardware
* Operating System
* Web Server
* Database
* Development Framework

The back-end server is responsible for running the processes and services that support the web application.

---

# Main Components

A back-end server commonly contains three important software components:

```text
Web Server
Database
Development Framework
```

Conceptually:

```text
Back-End Server
│
├── Operating System
│
├── Web Server
│
├── Web Application
│
└── Database
```

Other components may also exist, such as:

* Containers
* Hypervisors
* Web Application Firewalls (WAFs)

---

# Web Server

The **web server** handles HTTP requests from clients.

Examples include:

```text
Apache
NGINX
IIS
```

Basic flow:

```text
Browser
   ↓
HTTP Request
   ↓
Web Server
   ↓
Web Application
```

---

# Database

The database stores and retrieves application data.

Examples include:

```text
MySQL
SQL Server
Oracle
PostgreSQL
```

The web application communicates with the database whenever data needs to be accessed or modified.

---

# Development Framework

The development framework is used to implement the application's back-end logic.

Examples may include:

```text
PHP
.NET
Java
Python
Node.js
```

A simplified structure may look like:

```text
Client
  ↓
Web Server
  ↓
Application / Framework
  ↓
Database
```

---

# Solution Stacks

A **solution stack** is a combination of technologies commonly used together to build and host web applications.

## LAMP

```text
L → Linux
A → Apache
M → MySQL
P → PHP
```

```text
Linux
  ↓
Apache
  ↓
PHP Application
  ↓
MySQL
```

---

## WAMP

```text
W → Windows
A → Apache
M → MySQL
P → PHP
```

---

## WINS

```text
Windows
IIS
.NET
SQL Server
```

---

## MAMP

```text
M → macOS
A → Apache
M → MySQL
P → PHP
```

---

## XAMPP

XAMPP is a cross-platform stack that commonly includes:

```text
Apache
MySQL
PHP
PERL
```

---

# Why Stacks Matter in Pentesting

Recognizing the technology stack helps us understand what technologies may exist behind a web application.

For example:

```text
Linux + Apache + PHP + MySQL
            ↓
           LAMP
```

Knowing the stack can help us determine:

* Which web server is being used
* Which operating system may be present
* Which programming language or framework is involved
* Which database technology may be used
* Which vulnerabilities or misconfigurations may be relevant

---

# Hardware and Infrastructure

The back-end server also includes the underlying hardware resources required to run the application.

These may include:

* CPU
* Memory
* Storage
* Network resources

The available resources affect the application's:

```text
Performance
Stability
Responsiveness
```

---

# Multiple Back-End Servers

Large web applications may distribute their workload across several back-end servers.

Instead of:

```text
Users
  ↓
One Server
```

we may have:

```text
             ┌── Server 1
Users → Load ├── Server 2
             └── Server 3
```

This can improve:

* Performance
* Availability
* Scalability
* Redundancy

---

# Virtualization and Cloud Hosting

Web applications do not need to run directly on a single physical server.

They may run on:

* Virtual Machines
* Containers
* Data Centers
* Cloud Infrastructure

Conceptually:

```text
Physical Infrastructure
        ↓
Virtualization / Cloud
        ↓
Virtual Servers
        ↓
Web Application
```

---

# Key Takeaways

* A back-end server hosts the systems required for a web application.
* It may contain the web server, database, and application framework.
* Additional components may include containers, hypervisors, and WAFs.
* A **solution stack** is a combination of technologies used together.
* **LAMP** stands for Linux, Apache, MySQL, and PHP.
* Large applications may use multiple back-end servers.
* Back-end servers may be physical, virtualized, containerized, or cloud-hosted.
* Identifying the back-end stack is useful during reconnaissance and web penetration testing.

---

# Pentesting Perspective

When analyzing a web application, we should try to identify the technologies behind it.

Useful questions include:

```text
What operating system may be running?

Which web server is being used?

Which framework or language powers the application?

Which database may be present?

Is the application running inside containers?

Is the infrastructure distributed across multiple servers?
```

A useful mental model is:

```text
Back-End Server
      ↓
Operating System
      ↓
Web Server
      ↓
Application / Framework
      ↓
Database
```
