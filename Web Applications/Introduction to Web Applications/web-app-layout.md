# Web Application Layout

## Overview

No two web applications are exactly the same.

They may differ in:

* Programming languages
* Back-end infrastructure
* Database design
* Hosting models
* Application components
* Security controls

To properly understand and test a web application, we need to understand how its components are organized and how they communicate.

Web application layouts can be divided into three main categories:

1. **Web Application Infrastructure**
2. **Web Application Components**
3. **Web Application Architecture**

---

# Web Application Infrastructure

Web applications can use several infrastructure models.

The most common ones are:

* Client-Server
* One Server
* Many Servers - One Database
* Many Servers - Many Databases

---

## Client-Server

The **client-server model** is one of the most common web application models.

The server hosts the web application and provides its functionality to clients.

There are two main sides:

### Client Side

Usually includes:

* Browser
* Front-end components
* User Interface

### Server Side

Usually includes:

* Web server
* Application logic
* Database interaction

The basic communication flow is:

```text
Client
   |
   | HTTP Request
   v
Server
   |
   | Processes request
   v
Application / Database
   |
   | Result
   v
Server
   |
   | HTTP Response
   v
Client
```

For example, when we click a login button:

```text
Browser
   |
   | Sends login request
   v
Web Server
   |
   | Processes credentials
   v
Application Logic
   |
   | Checks data
   v
Database
   |
   | Returns result
   v
Browser
```

---

## One Server

In the **One Server** architecture, all components are hosted on a single server.

This may include:

```text
Web Server
Application Logic
Database
Other Web Applications
```

### Advantages

* Simple architecture
* Easy to implement
* Lower infrastructure complexity

### Disadvantages

From a security and availability perspective, this model can be risky.

If the server is compromised:

```text
One compromised server
        ↓
Web applications compromised
        ↓
Application data exposed
        ↓
Database may also be compromised
```

This is sometimes described as:

```text
"All eggs in one basket"
```

Another problem is availability.

If the server goes offline:

```text
Server Down
    ↓
All hosted applications become unavailable
```

---

## Many Servers - One Database

In this architecture, the database is hosted separately from the web servers.

Example:

```text
Web Server 1 ──┐
               |
Web Server 2 ──┼──> Database Server
               |
Web Server 3 ──┘
```

Several web applications may access the same database.

These applications may be:

* Replicas of the same application
* Primary and backup instances
* Different applications sharing common data

### Security Advantage

The main benefit is **segmentation**.

Different components are separated into different systems.

For example:

```text
Compromised Web Server
        ↓
Other Web Servers
may remain unaffected
```

However, segmentation alone is not enough.

We still need proper **access controls** to ensure each application can only access the data it actually needs.

---

## Many Servers - Many Databases

This model extends the previous architecture.

Instead of several web applications sharing a single database, each application may have its own database.

Example:

```text
Web App 1 ──> Database 1

Web App 2 ──> Database 2

Web App 3 ──> Database 3
```

Applications may still access common shared data when required.

This architecture improves:

* Segmentation
* Access control
* Security
* Availability
* Redundancy

Backup servers or databases can also be used.

```text
Primary Server
      |
      X Failure
      |
Backup Server
      ↓
Service continues
```

This architecture may require components such as **load balancers**.

Although it is more complex, it provides better security through proper segmentation and access control.

Other possible infrastructure models include:

* Serverless
* Microservices

---

# Web Application Components

A web application may contain several different components.

The main components can be grouped into:

## Client

The client is usually:

```text
Web Browser
```

It interacts with the application and sends requests to the server.

---

## Server

The server side may contain:

### Web Server

Receives and handles web requests.

### Web Application Logic

Processes application functionality and business logic.

### Database

Stores and retrieves application data.

---

## Services

Applications may also interact with services such as:

* Microservices
* Third-party integrations
* Internal application integrations

---

## Functions

Modern applications may also use:

```text
Serverless Functions
```

These functions execute specific application tasks without requiring developers to directly manage the underlying server infrastructure.

---

# Three-Tier Architecture

Web application architecture can commonly be divided into three layers.

```text
Presentation Layer
        ↓
Application Layer
        ↓
Data Layer
```

---

## Presentation Layer

The **Presentation Layer** represents what users interact with.

Typical technologies include:

```text
HTML
CSS
JavaScript
```

It is responsible for presenting information and allowing communication between the user and the application.

---

## Application Layer

The **Application Layer** handles application logic.

It processes requests and may verify:

* Authorization
* User privileges
* Input data
* Application rules

Example:

```text
HTTP Request
     ↓
Application Layer
     ↓
Check Authentication
Check Authorization
Validate Input
Process Request
```

---

## Data Layer

The **Data Layer** is responsible for accessing and managing stored data.

The application layer communicates with it whenever information needs to be stored or retrieved.

Example:

```text
Client
   ↓
Presentation Layer
   ↓
Application Layer
   ↓
Data Layer
   ↓
Database
```

---

# Microservices

**Microservices** divide a large application into smaller, independent components.

Each microservice usually performs one specific function.

For an online store, we may have separate services for:

```text
Registration
Search
Payments
Ratings
Reviews
```

These services can communicate with:

* Clients
* Other microservices
* Databases
* External services

Microservice communication is commonly **stateless**.

This means each request and response is handled independently.

```text
Request 1
   ↓
Service
   ↓
Response 1

Request 2
   ↓
Service
   ↓
Response 2
```

Persistent data is stored separately from the microservice itself.

Another important characteristic is that different microservices can be written in different programming languages while still communicating with each other.

### Benefits

* Agility
* Flexible scaling
* Easy deployment
* Reusable code
* Resilience

---

# Serverless Architecture

In a **serverless architecture**, cloud providers manage the underlying infrastructure.

Examples of cloud providers include:

* AWS
* Azure
* Google Cloud Platform

Developers focus mainly on the application code instead of managing servers.

Conceptually:

```text
Application Code
      ↓
Cloud Provider
      ↓
Infrastructure Management
Scaling
Execution
Maintenance
```

Serverless applications may run inside stateless computing environments or containers.

The cloud provider is responsible for tasks such as:

* Provisioning infrastructure
* Scaling
* Maintaining servers
* Managing execution environments

---

# Architecture Security

Understanding application architecture is extremely important during penetration testing.

A vulnerability may exist not because of a programming mistake, but because of a **design or architecture flaw**.

For example, an application may correctly implement its functionality but fail to implement proper access control.

A user may then gain access to:

```text
Administrative Functions
Other Users' Data
Restricted Features
```

A common access control model is:

```text
RBAC
Role-Based Access Control
```

RBAC defines what users are allowed to access depending on their assigned roles.

Example:

```text
User
 └── Regular Features

Moderator
 ├── Regular Features
 └── Moderation Features

Administrator
 ├── Regular Features
 ├── Moderation Features
 └── Administrative Features
```

If these permissions are incorrectly designed, users may access functionality they should not have access to.

---

## Architecture During Pentesting

Application architecture can also explain what we observe after compromising a system.

For example:

```text
Web Server Compromised
        ↓
Database not found locally
        ↓
Database may be hosted
on another server
```

Or:

```text
Database Access
      ↓
Only some application data found
      ↓
Multiple databases may exist
```

Understanding the architecture helps us determine where other components may be located and how they interact.

---

# Key Takeaways

* Web applications can use many different infrastructure models.
* The **client-server model** separates client-side and server-side responsibilities.
* Hosting everything on one server creates a large single point of failure.
* **Segmentation** separates application components and reduces the impact of compromise.
* Multiple servers and databases can provide better security and redundancy.
* Web applications are commonly divided into **Presentation**, **Application**, and **Data** layers.
* **Microservices** divide applications into smaller specialized services.
* **Serverless** architectures allow cloud providers to manage infrastructure.
* Security problems may come from both **implementation flaws** and **architecture flaws**.
* Understanding architecture helps us identify where systems, databases, and security boundaries may exist.

## Pentesting Perspective

When analyzing a web application, we should not think only about the visible website.

We should think about the entire infrastructure behind it:

```text
Client
   ↓
Front End
   ↓
Web Server
   ↓
Application Logic
   ↓
Services / Microservices
   ↓
Database
   ↓
Other Internal Systems
```

Understanding these relationships helps us identify possible attack paths and understand the real impact of a vulnerability.
