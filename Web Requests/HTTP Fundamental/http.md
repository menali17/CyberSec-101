# HyperText Transfer Protocol (HTTP)

**HyperText Transfer Protocol (HTTP)** is an application-layer protocol used to access resources on the World Wide Web.

Most web and mobile applications constantly communicate with remote servers using HTTP requests.

The term **hypertext** refers to text that can contain links to other resources.

---

# Client and Server

HTTP communication follows a **client-server model**.

The client requests a resource, and the server processes the request and returns a response.

For example:

```text
Client
  ↓
HTTP Request
  ↓
Server
  ↓
HTTP Response
  ↓
Client
```

The default HTTP port is:

```text
80
```

although a web server can be configured to listen on another port.

When we visit a website, we usually access it through a **Fully Qualified Domain Name (FQDN)** inside a **Uniform Resource Locator (URL)**.

Example:

```text
http://www.hackthebox.com/
```

---

# URL

A **URL** identifies the location of a resource and how it should be accessed.

Example:

```text
http://admin:password@inlanefreight.com:80/dashboard.php?login=true#status
```

A URL can contain several components.

| Component        | Example             | Description                                             |
| ---------------- | ------------------- | ------------------------------------------------------- |
| **Scheme**       | `http://`           | Protocol used to access the resource                    |
| **User Info**    | `admin:password@`   | Optional credentials for authentication                 |
| **Host**         | `inlanefreight.com` | Hostname or IP address of the server                    |
| **Port**         | `:80`               | Network port used for the connection                    |
| **Path**         | `/dashboard.php`    | Specific resource being requested                       |
| **Query String** | `?login=true`       | Parameters sent with the request                        |
| **Fragment**     | `#status`           | Identifies a section of the resource on the client side |

---

## Scheme

The **scheme** identifies the protocol.

Examples:

```text
http://
https://
```

The scheme ends with:

```text
://
```

---

## User Information

A URL can optionally contain credentials.

Example:

```text
admin:password@
```

The username and password are separated by:

```text
:
```

and the credentials are separated from the host with:

```text
@
```

---

## Host

The **host** identifies where the requested resource is located.

It can be:

```text
Hostname
```

or:

```text
IP address
```

Example:

```text
inlanefreight.com
```

---

## Port

The port is separated from the host using a colon.

Example:

```text
:80
```

If no port is specified, the default depends on the scheme:

| Protocol | Default Port |
| -------- | -----------: |
| HTTP     |         `80` |
| HTTPS    |        `443` |

---

## Path

The **path** identifies the specific resource we want to access.

Example:

```text
/dashboard.php
```

The resource can be a:

* File
* Directory
* Application endpoint

If no path is specified, the server normally returns its default index resource.

For example:

```text
index.html
```

---

## Query String

A **query string** starts with:

```text
?
```

Example:

```text
?login=true
```

This contains a parameter and its value:

```text
login=true
```

Multiple parameters can be separated using:

```text
&
```

For example:

```text
?user=bob&admin=false
```

---

## Fragment

A **fragment** starts with:

```text
#
```

Example:

```text
#status
```

Fragments are processed by the browser on the client side and are commonly used to navigate to a particular section of a page.

---

# Required URL Components

Not every URL component is required.

The two main required parts introduced in this section are:

```text
Scheme
Host
```

For example:

```text
http://inlanefreight.com
```

is enough to identify the protocol and destination.

---

# HTTP Flow

When we enter a domain such as:

```text
inlanefreight.com
```

into a browser, several steps occur before the webpage appears.

---

## 1. Domain Resolution

The browser first needs the IP address associated with the domain.

It typically checks whether the hostname can be resolved locally.

For example, on Linux:

```bash
/etc/hosts
```

can contain manual hostname mappings.

If the hostname is not found locally, the system queries a **DNS server**.

The DNS server returns the IP address associated with the domain.

Example:

```text
inlanefreight.com
        ↓
DNS
        ↓
152.153.81.14
```

The server must ultimately be reached using an IP address.

---

## 2. HTTP Request

Once the browser knows the destination IP address, it can connect to the web server.

For normal HTTP, this usually means connecting to:

```text
TCP/80
```

The browser can then send an HTTP request.

For example, it may request the root path:

```text
/
```

using a `GET` request.

---

## 3. Server Processing

The web server receives the request and determines which resource should be returned.

When the requested path is:

```text
/
```

the server is often configured to return a default index file such as:

```text
index.html
```

---

## 4. HTTP Response

The server returns an HTTP response containing the requested resource.

The response also contains a status code.

For example:

```text
HTTP/1.1 200 OK
```

`200 OK` indicates that the request was successfully processed.

---

## 5. Browser Rendering

The browser receives the response and renders the returned webpage for us.

In the example:

```text
index.html
```

is received and displayed as the final webpage.

---

# cURL

**cURL**, or **client URL**, is a command-line tool used to communicate with servers through HTTP and many other protocols.

It is especially useful for:

* Sending web requests
* Viewing raw responses
* Automation
* Scripting
* Web penetration testing

Unlike a web browser, cURL does not normally render HTML, CSS, or JavaScript visually.

Instead, it displays the raw response content.

---

# Basic HTTP Request with cURL

A basic request can be sent by giving cURL a URL:

```bash
menali@htb[/htb]$ curl http://info.cern.ch/
```

Example output:

```bash
<html><head></head><body><header>
<title>http://info.cern.ch</title>
</header>

<h1>http://info.cern.ch - home of the first website</h1>
...SNIP...
```

The HTML is printed directly into the terminal.

This is useful during penetration testing because we can inspect the raw server response without the browser rendering it.

---

# Downloading Files with cURL

cURL can also save the response to a file.

There are two commonly used options:

```text
-o
-O
```

---

## `-o`

The lowercase `-o` allows us to specify the output filename.

General syntax:

```bash
curl -o <filename> <URL>
```

---

## `-O`

The uppercase `-O` uses the remote filename automatically.

Example:

```bash
menali@htb[/htb]$ curl -O http://info.cern.ch/index.html

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   646  100   646    0     0   9773      0 --:--:-- --:--:-- --:--:--  9787
```

The file is then stored locally:

```bash
menali@htb[/htb]$ ls
index.html
```

The webpage contents are no longer printed to the terminal because they were written to the file.

---

# Silent Mode

Even when cURL writes output to a file, it normally displays transfer information.

We can suppress this with:

```text
-s
```

Example:

```bash
menali@htb[/htb]$ curl -s -O http://info.cern.ch/index.html
```

This produces no terminal output while saving the file.

---

# cURL Help

We can view common cURL options with:

```bash
menali@htb[/htb]$ curl -h
```

Important options introduced in this section include:

| Option                | Purpose                               |
| --------------------- | ------------------------------------- |
| `-d`, `--data`        | Send HTTP POST data                   |
| `-h`, `--help`        | Display help                          |
| `-i`, `--include`     | Include response headers              |
| `-o`, `--output`      | Write output to a specified file      |
| `-O`, `--remote-name` | Save using the remote filename        |
| `-s`, `--silent`      | Silent mode                           |
| `-u`, `--user`        | Provide username and password         |
| `-A`, `--user-agent`  | Specify a User-Agent                  |
| `-v`, `--verbose`     | Display more request/response details |

Example:

```bash
curl -h
```

For all available help:

```bash
curl --help all
```

For help related to a specific category:

```bash
curl -h http
```

For the full manual:

```bash
man curl
```

---

# Browser vs. cURL

| Browser                        | cURL                                  |
| ------------------------------ | ------------------------------------- |
| Renders HTML visually          | Displays raw HTML                     |
| Executes frontend content      | Focuses on request/response data      |
| Designed for user interaction  | Designed for command-line interaction |
| Convenient for normal browsing | Convenient for testing and automation |

For web penetration testing, both are useful, but cURL is particularly valuable for quickly inspecting HTTP behavior from the command line.

---

# Quick Reference

### Basic Request

```bash
curl http://example.com/
```

### Download and Choose Filename

```bash
curl -o page.html http://example.com/
```

### Download Using Remote Filename

```bash
curl -O http://example.com/index.html
```

### Silent Download

```bash
curl -s -O http://example.com/index.html
```

### Help

```bash
curl -h
curl --help all
curl -h http
man curl
```

### Default Ports

```text
HTTP  → 80
HTTPS → 443
```

### URL Components

```text
Scheme
User Info
Host
Port
Path
Query String
Fragment
```

---

## Key Takeaway

**HTTP is an application-layer protocol based on communication between a client and a server. A client identifies resources through URLs, resolves domain names through DNS, sends HTTP requests to the server, and receives HTTP responses containing status information and resources. cURL provides a command-line way to send these requests, inspect raw responses, and download resources, making it an essential tool for web testing and automation.**
