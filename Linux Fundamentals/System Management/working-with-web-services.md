# Working with Web Services

Web services allow systems to communicate over protocols such as HTTP and HTTPS.

In this section, we focus on three important concepts:

```text
Apache
→ Run a web server

cURL / Wget
→ Communicate with web servers and retrieve content

Python HTTP Server
→ Quickly create a simple web server
```

Apache is one of the most widely used web servers and can be extended through modules to provide additional functionality.

---

# Apache

Apache is a **web server**.

Its job is to receive requests from clients and return web content.

Conceptually:

```text
Browser
   │
   │ HTTP Request
   ▼
Apache
   │
   │ HTTP Response
   ▼
Browser
```

For example, when we visit:

```text
http://localhost
```

our browser sends an HTTP request to the Apache server running on our own machine.

---

# Installing Apache

We can install Apache using APT:

```bash
menali@htb[/htb]$ sudo apt install apache2 -y

Reading package lists... Done
Building dependency tree
Reading state information... Done
The following NEW packages will be installed:
  apache2
<SNIP>
```

Breaking this down:

```text
sudo
→ elevated privileges

apt
→ package manager

install
→ install software

apache2
→ package

-y
→ automatically confirm
```

---

# Starting Apache

After installation, we can start Apache using:

```bash
sudo systemctl start apache2
```

This combines concepts from previous sections:

```text
Package Management
        │
        ▼
apt install apache2
        │
        ▼
Service Management
        │
        ▼
systemctl start apache2
```

---

# Default HTTP Port

Apache serves HTTP on:

```text
TCP/80
```

by default.

Therefore:

```text
http://localhost
```

implicitly means:

```text
http://localhost:80
```

The browser automatically uses port `80` when HTTP is specified without another port.

---

# `localhost`

The hostname:

```text
localhost
```

refers to our own machine.

It normally resolves to:

```text
127.0.0.1
```

So:

```text
http://localhost
```

means:

> Connect to the web server running on this machine.

---

# Changing the Apache Port

Sometimes port `80` is already being used by another service.

Apache's listening ports can be configured in:

```text
/etc/apache2/ports.conf
```

For example:

```text
Listen 8080
```

changes the HTTP listening port to:

```text
8080
```

We would then access the server using:

```text
http://localhost:8080
```

---

# Ports in URLs

Compare:

```text
http://localhost
```

with:

```text
http://localhost:8080
```

The first implicitly uses:

```text
TCP/80
```

The second explicitly uses:

```text
TCP/8080
```

Conceptually:

```text
IP / Hostname
→ Which machine?

Port
→ Which service on that machine?
```

For example:

```text
10.10.10.5:22
→ SSH

10.10.10.5:80
→ HTTP

10.10.10.5:443
→ commonly HTTPS

10.10.10.5:8080
→ application/service listening on 8080
```

---

# Testing a Web Server with cURL

After changing Apache to port `8080`, the material tests it using:

```bash
curl -I http://localhost:8080
```

Output:

```bash
HTTP/1.1 200 OK
Date: Mon, 04 Nov 2024 21:18:50 GMT
Server: Apache/2.4.62 (Debian)
Last-Modified: Mon, 07 Oct 2024 06:39:39 GMT
Content-Length: 10701
Content-Type: text/html
```

The important result is:

```text
HTTP/1.1 200 OK
```

which indicates that the HTTP request succeeded.

---

# cURL

`cURL` is a command-line tool used to transfer data and communicate with network services.

It supports protocols including:

```text
HTTP
HTTPS
FTP
SFTP
FTPS
SCP
```

For web testing, we can think of `curl` as a **browser for the terminal**.

The material emphasizes that it can retrieve content and inspect communication with web servers.

---

# Basic cURL Request

We can request a webpage using:

```bash
curl http://localhost
```

Instead of rendering the website visually like a browser, `curl` prints the response body to:

```text
STDOUT
```

Example:

```bash
menali@htb[/htb]$ curl http://localhost

<!DOCTYPE html>
<html>
<head>
    <title>Apache2 Ubuntu Default Page: It works</title>
...
```

This is an important distinction.

### Browser

```text
HTML + CSS + JavaScript
          │
          ▼
       Browser
          │
          ▼
Rendered website
```

### cURL

```text
HTTP response
      │
      ▼
    cURL
      │
      ▼
Raw content in terminal
```

The material explicitly points out that `curl` returns the page source through STDOUT rather than rendering it.

---

# `curl -I`

The option:

```text
-I
```

allows us to request HTTP headers.

For example:

```bash
curl -I http://localhost:8080
```

may return:

```bash
HTTP/1.1 200 OK
Server: Apache/2.4.62 (Debian)
Content-Length: 10701
Content-Type: text/html
```

This can quickly give us information about the server and response without displaying the entire page body.

---

# Wget

`wget` is another command-line tool used to retrieve resources from network servers.

The material focuses on downloading from:

```text
HTTP
FTP
```

Example:

```bash
wget http://localhost
```

Output:

```bash
--2020-05-15 17:43:52--  http://localhost/
Resolving localhost (localhost)... 127.0.0.1
Connecting to localhost (localhost)|127.0.0.1|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 10918 (11K) [text/html]
Saving to: 'index.html'

index.html  100%[=======================================>]  10.66K

'index.html' saved [10918/10918]
```

Notice:

```text
Saving to: 'index.html'
```

Unlike the basic `curl` example, `wget` stores the retrieved content locally.

---

# cURL vs Wget

This is the main distinction shown in this section.

### cURL

```bash
curl http://localhost
```

returns the content to:

```text
STDOUT
```

So we see it directly in the terminal.

### Wget

```bash
wget http://localhost
```

downloads the content and stores it locally.

For example:

```text
index.html
```

A useful mental model is:

```text
curl
→ "Show / interact with the response"

wget
→ "Download the resource"
```

---

# Example

Suppose a server contains:

```text
http://10.10.10.5/file.txt
```

Using:

```bash
curl http://10.10.10.5/file.txt
```

might display:

```bash
secret information
```

directly in our terminal.

Using:

```bash
wget http://10.10.10.5/file.txt
```

would instead download:

```text
file.txt
```

to our machine.

---

# Python HTTP Server

Python provides a very simple way to create a temporary HTTP server.

We use:

```bash
python3 -m http.server
```

Output:

```bash
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

By default, the server listens on:

```text
TCP/8000
```

and serves files from the directory where we executed the command.

---

# Understanding the Current Directory

Suppose we have:

```bash
pwd
```

```bash
/home/htb/files
```

And:

```bash
ls
```

```bash
notes.txt
script.sh
image.png
```

If we execute:

```bash
python3 -m http.server
```

Python exposes that directory through HTTP.

Conceptually:

```text
/home/htb/files/
├── notes.txt
├── script.sh
└── image.png
       │
       ▼
python3 -m http.server
       │
       ▼
TCP/8000
       │
       ▼
Other machine
```

A remote machine could then request:

```text
http://OUR_IP:8000/script.sh
```

---

# Python HTTP Server and Wget

These tools become particularly useful together.

Machine A:

```bash
python3 -m http.server
```

Suppose Machine A has:

```text
script.sh
```

Machine B can retrieve it with:

```bash
wget http://MACHINE_A_IP:8000/script.sh
```

Conceptually:

```text
MACHINE A                     MACHINE B

script.sh
   │
   ▼
Python HTTP Server
   │
   │ HTTP
   └─────────────────────────► wget
                                │
                                ▼
                            script.sh
```

This is why Python's HTTP server is so useful for quick file transfers.

---

# Python HTTP Server Requests

The server also displays incoming requests.

For example:

```bash
python3 -m http.server
```

may show:

```bash
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...

127.0.0.1 - - [15/May/2020 17:56:29] "GET /readme.html HTTP/1.1" 200 -
127.0.0.1 - - [15/May/2020 17:56:29] "GET /wp-admin/css/install.css HTTP/1.1" 200 -
```

The line:

```text
GET /readme.html HTTP/1.1
```

means a client requested:

```text
/readme.html
```

And:

```text
200
```

indicates a successful HTTP response in this example.

---

# How Everything Connects

This section is easier if we imagine two computers.

```text
┌─────────────────────┐
│      MACHINE A      │
│                     │
│ python3 -m           │
│ http.server          │
│                     │
│ file.txt             │
└──────────┬──────────┘
           │
           │ HTTP :8000
           │
           ▼
┌─────────────────────┐
│      MACHINE B      │
│                     │
│ wget http://A:8000/ │
│ file.txt             │
└─────────────────────┘
```

Machine A is acting as the:

```text
SERVER
```

Machine B is acting as the:

```text
CLIENT
```

---

# Server vs Client

This distinction is fundamental.

### Server

Provides a resource or service.

Examples:

```text
Apache
Python HTTP Server
```

### Client

Connects to the server and requests something.

Examples:

```text
Browser
curl
wget
```

Therefore:

```text
        HTTP
Client ───────► Server
       request

Client ◄─────── Server
       response
```

---

# Apache vs Python HTTP Server

Both can serve HTTP content, but their purposes in this material are different.

### Apache

```text
Full web server
Configurable
Modular
Suitable for web applications
Runs as a service
```

### Python HTTP Server

```text
Simple
Temporary
One command
Useful for quick file transfers/testing
```

So:

```text
Apache
→ "We want a real configurable web server."

Python HTTP Server
→ "We need to serve this folder quickly."
```

---

# Cybersecurity Relevance

These tools are extremely useful during security work.

For example, we may need to:

```text
Inspect HTTP responses
Download files
Transfer tools between machines
Test whether a web server responds
Inspect HTTP headers
Temporarily expose files
```

This is why `curl`, `wget`, and Python's HTTP server appear frequently in penetration-testing environments.

---

# Essential Commands

Install Apache:

```bash
sudo apt install apache2 -y
```

Start Apache:

```bash
sudo systemctl start apache2
```

Retrieve webpage content:

```bash
curl http://localhost
```

Retrieve HTTP headers:

```bash
curl -I http://localhost
```

Download content:

```bash
wget http://localhost
```

Start a temporary HTTP server:

```bash
python3 -m http.server
```

---

# Quick Reference

| Command                   | Main Purpose                   |
| ------------------------- | ------------------------------ |
| `systemctl start apache2` | Start Apache                   |
| `curl URL`                | Retrieve/interact with content |
| `curl -I URL`             | Display HTTP headers           |
| `wget URL`                | Download content               |
| `python3 -m http.server`  | Start simple HTTP server       |

---

# Mental Model

The easiest way to remember this section is:

```text
Apache
→ SERVE a website

Python HTTP Server
→ SERVE files quickly

curl
→ REQUEST / inspect content

wget
→ DOWNLOAD content
```

And:

```text
SERVER                         CLIENT

Apache       ◄─────────────── Browser
Python HTTP  ◄─────────────── curl
Server       ◄─────────────── wget
                  HTTP
```

---

## Key Takeaway

**Web services follow a client-server model. Apache provides a configurable web server, while Python can quickly expose a directory through HTTP. On the client side, cURL allows us to retrieve and inspect web responses directly from the terminal, while Wget is particularly useful for downloading resources. These tools become especially important in cybersecurity for web analysis and transferring files between systems.**
