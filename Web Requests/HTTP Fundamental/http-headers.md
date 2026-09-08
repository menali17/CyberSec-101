# HTTP Headers

**HTTP headers** pass additional information between the client and server.

A header follows the general format:

```http
Header-Name: Value
```

Headers may contain one or multiple values and can be grouped into:

* General Headers
* Entity Headers
* Request Headers
* Response Headers
* Security Headers

---

# General Headers

**General headers** can appear in both requests and responses. They describe the HTTP message itself rather than its content.

| Header       | Example                               | Description                                                             |
| ------------ | ------------------------------------- | ----------------------------------------------------------------------- |
| `Date`       | `Date: Wed, 16 Feb 2022 10:38:44 GMT` | Date and time when the message originated, typically represented in UTC |
| `Connection` | `Connection: close`                   | Controls whether the network connection remains open                    |

Two common `Connection` values are:

```http
Connection: close
Connection: keep-alive
```

* `close` — terminate the connection after the request.
* `keep-alive` — keep the connection open for additional communication.

---

# Entity Headers

**Entity headers** describe the content transferred in an HTTP message.

They are commonly found in responses and requests containing data, such as `POST` and `PUT`.

| Header             | Example                       | Description                                          |
| ------------------ | ----------------------------- | ---------------------------------------------------- |
| `Content-Type`     | `Content-Type: text/html`     | Type of content being transferred                    |
| `Media-Type`       | `Media-Type: application/pdf` | Describes the transferred data                       |
| `Boundary`         | `boundary="b4e4fbd93540"`     | Separates multiple pieces of content                 |
| `Content-Length`   | `Content-Length: 385`         | Size of the transferred entity                       |
| `Content-Encoding` | `Content-Encoding: gzip`      | Transformation or compression applied to the content |

---

## Content-Type

`Content-Type` tells the recipient how to interpret the body.

Examples:

```http
Content-Type: text/html
Content-Type: application/json
Content-Type: text/html; charset=UTF-8
```

The optional `charset` specifies the character encoding.

---

## Boundary

A boundary separates multiple pieces of data contained in the same HTTP message.

For example:

```http
boundary="b4e4fbd93540"
```

The body can then use:

```text
--b4e4fbd93540
```

to separate different sections of form data.

---

## Content-Length

`Content-Length` specifies the size of the message body:

```http
Content-Length: 385
```

Browsers and tools such as cURL usually generate it automatically when necessary.

---

## Content-Encoding

`Content-Encoding` indicates transformations applied to the content before transmission.

For example:

```http
Content-Encoding: gzip
```

means the content was compressed using gzip.

---

# Request Headers

**Request headers** are sent by the client and provide information about the request and client.

Important request headers include:

| Header          | Example                                  | Purpose                                |
| --------------- | ---------------------------------------- | -------------------------------------- |
| `Host`          | `Host: www.inlanefreight.com`            | Target host                            |
| `User-Agent`    | `User-Agent: curl/7.77.0`                | Information about the client           |
| `Referer`       | `Referer: http://www.inlanefreight.com/` | Page from which the request originated |
| `Accept`        | `Accept: */*`                            | Content types the client accepts       |
| `Cookie`        | `Cookie: PHPSESSID=b4e4fbd93540`         | Sends cookies to the server            |
| `Authorization` | `Authorization: BASIC cGFzc3dvcmQK`      | Sends authentication information       |

---

## Host

The `Host` header specifies which host the client wants to access:

```http
Host: www.inlanefreight.com
```

This is particularly important when a single HTTP server hosts multiple websites.

From a penetration-testing perspective, the `Host` header can also be useful when enumerating additional virtual hosts.

---

## User-Agent

The `User-Agent` describes the client making the request:

```http
User-Agent: curl/7.77.0
```

It may reveal information such as:

* Browser or application
* Version
* Operating system

---

## Referer

The `Referer` header identifies where the current request originated.

Example:

```http
Referer: https://google.com/
```

This header should not be blindly trusted because the client can manipulate it.

---

## Accept

The `Accept` header tells the server which media types the client understands.

Example:

```http
Accept: */*
```

`*/*` means that the client accepts any media type.

Multiple accepted types can also be provided.

---

## Cookie

The `Cookie` header sends stored cookies to the server:

```http
Cookie: PHPSESSID=b4e4fbd93540
```

Cookies can be used for purposes such as:

* Session identification
* Session tracking
* User preferences

Multiple cookies can appear in the same header:

```http
Cookie: cookie1=value1; cookie2=value2
```

---

## Authorization

The `Authorization` header sends authentication information:

```http
Authorization: BASIC cGFzc3dvcmQK
```

Different authentication mechanisms can use this header depending on the server and application.

---

# Response Headers

**Response headers** are sent by the server and provide additional information about the response.

Important examples include:

| Header             | Example                                     | Purpose                           |
| ------------------ | ------------------------------------------- | --------------------------------- |
| `Server`           | `Server: Apache/2.2.14 (Win32)`             | Information about the web server  |
| `Set-Cookie`       | `Set-Cookie: PHPSESSID=b4e4fbd93540`        | Sends cookies to the client       |
| `WWW-Authenticate` | `WWW-Authenticate: BASIC realm="localhost"` | Specifies required authentication |

---

## Server

The `Server` header can disclose information about the web server:

```http
Server: Apache/2.2.14 (Win32)
```

This may reveal:

* Server software
* Version
* Operating system information

Such information can be useful during enumeration.

---

## Set-Cookie

The server uses `Set-Cookie` to instruct the client to store a cookie:

```http
Set-Cookie: PHPSESSID=b4e4fbd93540
```

The browser can later send it back using:

```http
Cookie: PHPSESSID=b4e4fbd93540
```

The distinction is:

```text
Set-Cookie → Server sends cookie to client
Cookie     → Client sends cookie to server
```

---

## WWW-Authenticate

This header tells the client which authentication mechanism is required:

```http
WWW-Authenticate: BASIC realm="localhost"
```

It is commonly seen with authentication-related responses such as `401 Unauthorized`.

---

# Security Headers

**Security headers** are response headers that instruct the browser to apply specific security policies.

Important examples from this section are:

| Header                      | Example             | Purpose                                     |
| --------------------------- | ------------------- | ------------------------------------------- |
| `Content-Security-Policy`   | `script-src 'self'` | Restricts permitted content sources         |
| `Strict-Transport-Security` | `max-age=31536000`  | Forces HTTPS usage                          |
| `Referrer-Policy`           | `origin`            | Controls information sent through `Referer` |

---

## Content-Security-Policy (CSP)

Example:

```http
Content-Security-Policy: script-src 'self'
```

CSP controls which sources the browser is allowed to load content from.

In this example:

```text
script-src 'self'
```

means scripts should only be loaded from the same origin.

CSP can help mitigate attacks such as **Cross-Site Scripting (XSS)**.

---

## Strict-Transport-Security (HSTS)

Example:

```http
Strict-Transport-Security: max-age=31536000
```

**HTTP Strict Transport Security (HSTS)** tells the browser to use HTTPS rather than plaintext HTTP for the specified period.

This helps protect against attempts to downgrade communication to HTTP.

---

## Referrer-Policy

Example:

```http
Referrer-Policy: origin
```

This controls how much referrer information the browser sends in the `Referer` header.

It can reduce the risk of exposing sensitive URL information to other websites.

---

# Header Categories Summary

| Category     | Main Purpose                   | Examples                                             |
| ------------ | ------------------------------ | ---------------------------------------------------- |
| **General**  | Describe the HTTP message      | `Date`, `Connection`                                 |
| **Entity**   | Describe transferred content   | `Content-Type`, `Content-Length`, `Content-Encoding` |
| **Request**  | Information sent by the client | `Host`, `User-Agent`, `Cookie`, `Authorization`      |
| **Response** | Information sent by the server | `Server`, `Set-Cookie`, `WWW-Authenticate`           |
| **Security** | Browser security policies      | `CSP`, `HSTS`, `Referrer-Policy`                     |

Applications may also define **custom HTTP headers** beyond the standard headers.

---

# Working with Headers Using cURL

We already used `-v` to inspect the complete HTTP communication:

```bash
curl -v https://www.inlanefreight.com
```

This section introduces additional options specifically useful for headers.

---

## `-I` — Response Headers Only

The `-I` option sends a `HEAD` request and displays the response headers:

```bash
menali@htb[/htb]$ curl -I https://www.inlanefreight.com
```

`HEAD` will be covered in the next section.

---

## `-i` — Include Response Headers

Lowercase `-i` includes the response headers together with the response body:

```bash
curl -i https://www.inlanefreight.com
```

The important distinction is:

| Option | Behavior                                                     |
| ------ | ------------------------------------------------------------ |
| `-I`   | Sends a `HEAD` request and displays headers                  |
| `-i`   | Includes response headers with the normal response body      |
| `-v`   | Shows detailed request, response, and connection information |

---

# Setting Request Headers

cURL allows us to manually set request headers using:

```bash
-H
```

General syntax:

```bash
curl -H 'Header-Name: Value' https://example.com
```

This will be explored further later in the module.

---

## Changing the User-Agent

cURL provides the `-A` option specifically for setting the `User-Agent`.

Example:

```bash
menali@htb[/htb]$ curl https://www.inlanefreight.com -A 'Mozilla/5.0'

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
...SNIP...
```

This changes the request header to:

```http
User-Agent: Mozilla/5.0
```

We can verify it using verbose mode:

```bash
curl -v https://www.inlanefreight.com -A 'Mozilla/5.0'
```

---

# Headers in Browser DevTools

HTTP headers can also be inspected through:

```text
DevTools → Network → Select Request → Headers
```

The browser separates the information into request and response headers.

The **Raw** view displays them closer to their original HTTP representation.

The **Cookies** tab can also be used to inspect cookies associated with a request.

---

# Quick Reference

### Important Request Headers

```text
Host
User-Agent
Referer
Accept
Cookie
Authorization
```

### Important Response Headers

```text
Server
Set-Cookie
WWW-Authenticate
```

### Important Security Headers

```text
Content-Security-Policy
Strict-Transport-Security
Referrer-Policy
```

### cURL

```bash
curl -I <URL>                    # Response headers using HEAD
curl -i <URL>                    # Headers + response body
curl -v <URL>                    # Verbose request/response information
curl -H 'Header: Value' <URL>    # Set a request header
curl -A 'Mozilla/5.0' <URL>      # Set User-Agent
```

---

## Key Takeaway

**HTTP headers carry metadata and instructions between clients and servers. Request headers describe the client and request, response headers describe the server's response, entity headers describe transferred content, and security headers define browser security policies. For web testing, recognizing headers such as `Host`, `Cookie`, `Authorization`, `Server`, `Set-Cookie`, CSP, and HSTS is particularly important.**
