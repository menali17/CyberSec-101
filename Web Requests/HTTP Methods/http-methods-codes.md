# HTTP Methods and Status Codes

HTTP **methods** tell the server what action we want to perform on a resource, while **status codes** tell the client the result of that request.

In a request, the method appears at the beginning of the request line:

```http
GET / HTTP/1.1
```

In the response, the status code appears in the status line:

```http
HTTP/1.1 200 OK
```

---

# HTTP Request Methods

Common HTTP methods include:

| Method    | Purpose                                     |
| --------- | ------------------------------------------- |
| `GET`     | Retrieve a resource                         |
| `POST`    | Send data to the server                     |
| `HEAD`    | Retrieve response headers without the body  |
| `PUT`     | Create a resource                           |
| `DELETE`  | Delete a resource                           |
| `OPTIONS` | Check information such as supported methods |
| `PATCH`   | Partially modify a resource                 |

---

## GET

`GET` requests a resource from the server.

```http
GET /index.html HTTP/1.1
```

Additional information can be passed through the URL using query parameters:

```text
/search?query=HTB
```

For example:

```text
?param=value
```

---

## POST

`POST` sends data to the server through the **request body**.

It is commonly used for:

* Forms
* Login requests
* File uploads
* Sending application data

Example structure:

```http
POST /login HTTP/1.1
Content-Type: application/x-www-form-urlencoded

username=admin&password=password
```

Unlike typical `GET` parameters, the data is placed in the request body.

---

## HEAD

`HEAD` requests the same headers that would normally be returned by a `GET` request, but without the response body.

```http
HEAD /index.html HTTP/1.1
```

This can be useful for checking information about a resource without downloading the resource itself.

This is also the method used by the previously introduced cURL option:

```bash
curl -I https://example.com
```

---

## PUT

`PUT` can be used to create resources on the server.

For example, an application may allow a resource to be placed at a specified location.

If `PUT` is enabled without proper access controls, it may allow unauthorized files or malicious resources to be uploaded.

---

## DELETE

`DELETE` removes an existing resource:

```http
DELETE /resource HTTP/1.1
```

Improperly secured `DELETE` functionality can allow unauthorized deletion of server resources and potentially cause service disruption.

---

## OPTIONS

`OPTIONS` requests information about the communication options available for a resource.

It can reveal which HTTP methods the server accepts.

For example, a server may indicate support for:

```text
GET
POST
HEAD
OPTIONS
```

This can be useful during web application enumeration.

---

## PATCH

`PATCH` applies **partial modifications** to an existing resource.

Unlike replacing or creating an entire resource, it is intended to modify only part of it.

---

# Methods in Web Applications

Most traditional web applications rely heavily on:

```text
GET
POST
```

Applications using REST APIs may also commonly use methods such as:

```text
PUT
DELETE
PATCH
```

The methods actually available depend on the web server and application configuration.

---

# HTTP Status Codes

HTTP **status codes** tell the client what happened when the server processed a request.

They are divided into five classes:

| Class | Meaning                   |
| ----- | ------------------------- |
| `1xx` | Informational             |
| `2xx` | Success                   |
| `3xx` | Redirection               |
| `4xx` | Client-side request error |
| `5xx` | Server-side error         |

A useful way to remember them is:

```text
1xx → Information
2xx → Success
3xx → Redirect
4xx → Client problem
5xx → Server problem
```

---

# Common Status Codes

## `200 OK`

```http
HTTP/1.1 200 OK
```

The request succeeded.

The response body usually contains the requested resource.

---

## `302 Found`

```http
HTTP/1.1 302 Found
```

Redirects the client to another URL.

For example, after a successful login, an application may redirect us to a dashboard.

---

## `400 Bad Request`

```http
HTTP/1.1 400 Bad Request
```

The server considers the request malformed or invalid.

An example would be an incorrectly formatted HTTP request.

---

## `403 Forbidden`

```http
HTTP/1.1 403 Forbidden
```

The server understood the request but does not allow the client to access the resource.

It may also appear when a server detects input that it considers malicious.

---

## `404 Not Found`

```http
HTTP/1.1 404 Not Found
```

The requested resource does not exist on the server.

For example:

```http
GET /does-not-exist.txt HTTP/1.1
```

may result in:

```http
HTTP/1.1 404 Not Found
```

---

## `500 Internal Server Error`

```http
HTTP/1.1 500 Internal Server Error
```

The server encountered a problem while processing the request.

Unlike `4xx`, which indicates a problem associated with the client's request, `5xx` indicates a problem on the server side.

---

# Common Codes Summary

| Code                        | Meaning                              |
| --------------------------- | ------------------------------------ |
| `200 OK`                    | Request succeeded                    |
| `302 Found`                 | Redirect to another location         |
| `400 Bad Request`           | Malformed request                    |
| `403 Forbidden`             | Access denied                        |
| `404 Not Found`             | Resource does not exist              |
| `500 Internal Server Error` | Server failed to process the request |

Servers and providers may also implement additional status codes beyond the standard HTTP codes.

---

# Quick Reference

### Methods

```text
GET     → Retrieve
POST    → Send data
HEAD    → Headers only
PUT     → Create resource
DELETE  → Delete resource
OPTIONS → Check available options/methods
PATCH   → Partially modify
```

### Status Code Classes

```text
1xx → Informational
2xx → Success
3xx → Redirection
4xx → Client error
5xx → Server error
```

### Common Status Codes

```text
200 → OK
302 → Found / Redirect
400 → Bad Request
403 → Forbidden
404 → Not Found
500 → Internal Server Error
```

---

## Key Takeaway

**HTTP methods specify what action the client wants the server to perform, while HTTP status codes indicate the result. The most important methods to recognize are `GET`, `POST`, `HEAD`, `PUT`, `DELETE`, `OPTIONS`, and `PATCH`, while the status-code classes `2xx`, `3xx`, `4xx`, and `5xx` quickly indicate success, redirection, client errors, and server errors.**
