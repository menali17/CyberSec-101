# GET

`GET` is the default HTTP method browsers use when requesting resources from a URL.

A page may initially be retrieved with `GET` and then generate additional requests as the application loads or as we interact with it.

These requests can be inspected through:

```text
DevTools → Network
```

This is particularly useful for understanding how a web application communicates with its backend.

---

# HTTP Basic Authentication

**HTTP Basic Authentication** is handled directly through HTTP rather than through a typical application login form.

When accessing a protected resource without credentials:

```bash
menali@htb[/htb]$ curl -i http://<SERVER_IP>:<PORT>/
HTTP/1.1 401 Authorization Required
Date: Mon, 21 Feb 2022 13:11:46 GMT
Server: Apache/2.4.41 (Ubuntu)
Cache-Control: no-cache, must-revalidate, max-age=0
WWW-Authenticate: Basic realm="Access denied"
Content-Length: 13
Content-Type: text/html; charset=UTF-8

Access denied
```

Two important parts indicate that authentication is required:

```http
HTTP/1.1 401 Authorization Required
WWW-Authenticate: Basic realm="Access denied"
```

The `WWW-Authenticate` header identifies **Basic authentication** as the authentication mechanism.

---

# Basic Authentication with cURL

cURL provides the `-u` option for supplying credentials:

```bash
menali@htb[/htb]$ curl -u admin:admin http://<SERVER_IP>:<PORT>/

<!DOCTYPE html>
<html lang="en">

<head>
...SNIP...
```

The syntax is:

```bash
curl -u username:password <URL>
```

Credentials can also be included directly in the URL:

```bash
menali@htb[/htb]$ curl http://admin:admin@<SERVER_IP>:<PORT>/

<!DOCTYPE html>
<html lang="en">

<head>
...SNIP...
```

---

# Authorization Header

If we inspect an authenticated request using `-v`:

```bash
menali@htb[/htb]$ curl -v http://admin:admin@<SERVER_IP>:<PORT>/

*   Trying <SERVER_IP>:<PORT>...
* Connected to <SERVER_IP> (<SERVER_IP>) port PORT (#0)
* Server auth using Basic with user 'admin'
> GET / HTTP/1.1
> Host: <SERVER_IP>
> Authorization: Basic YWRtaW46YWRtaW4=
> User-Agent: curl/7.77.0
> Accept: */*
>
< HTTP/1.1 200 OK
< Date: Mon, 21 Feb 2022 13:19:57 GMT
< Server: Apache/2.4.41 (Ubuntu)
< Content-Type: text/html; charset=UTF-8
<
...SNIP...
```

we can see:

```http
Authorization: Basic YWRtaW46YWRtaW4=
```

With Basic authentication, the credentials are encoded in Base64:

```text
admin:admin
     ↓
YWRtaW46YWRtaW4=
```

Therefore, **Base64 is not encryption**. It is only an encoding format.

This is one reason Basic authentication should be used over HTTPS so the HTTP headers are protected in transit.

---

# Manually Setting Authorization

Instead of using `-u`, we can manually provide the `Authorization` header with cURL's `-H` option:

```bash
menali@htb[/htb]$ curl -H 'Authorization: Basic YWRtaW46YWRtaW4=' http://<SERVER_IP>:<PORT>/

<!DOCTYPE html>
<html lang="en">

<head>
...SNIP...
```

General syntax:

```bash
curl -H 'Header: Value' <URL>
```

Multiple `-H` options can be used to specify multiple headers.

For Basic authentication:

```http
Authorization: Basic <Base64(username:password)>
```

Modern authentication mechanisms may use other authorization schemes. For example:

```http
Authorization: Bearer <token>
```

---

# GET Parameters

`GET` requests commonly send parameters through the URL.

The general format is:

```text
/path?parameter=value
```

For example, the City Search application sends:

```text
/search.php?search=le
```

Here:

| Part          | Meaning                 |
| ------------- | ----------------------- |
| `/search.php` | Requested resource      |
| `?`           | Begins the query string |
| `search`      | Parameter               |
| `le`          | Parameter value         |

The resulting request is effectively:

```http
GET /search.php?search=le HTTP/1.1
```

---

# Reproducing a GET Request with cURL

After observing the request in DevTools, we can reproduce it directly:

```bash
menali@htb[/htb]$ curl 'http://<SERVER_IP>:<PORT>/search.php?search=le' -H 'Authorization: Basic YWRtaW46YWRtaW4='

Leeds (UK)
Leicester (UK)
```

The browser displayed the search results through the application's interface, while requesting `search.php` directly returns the backend response without that interface.

This is useful during web testing because we can interact directly with backend endpoints.

---

# Copy as cURL

Browser DevTools can automatically generate the cURL command corresponding to a request.

In the Network tab:

```text
Right-click request → Copy → Copy as cURL
```

The generated command reproduces the browser's request, including its headers.

It may contain many unnecessary headers, so we can usually remove them and retain only those required by the endpoint.

For example, authentication may require keeping:

```http
Authorization: Basic YWRtaW46YWRtaW4=
```

---

# Copy as Fetch

DevTools can also reproduce a request using JavaScript's Fetch API:

```text
Right-click request → Copy → Copy as Fetch
```

The copied request can be executed from the browser's JavaScript console.

In Firefox, the console can be opened with:

```text
Ctrl + Shift + K
```

After executing the Fetch request, we can inspect the resulting request and response through DevTools.

---

# Quick Reference

### Basic GET

```bash
curl http://<SERVER_IP>:<PORT>/
```

### Basic Authentication

```bash
curl -u admin:admin http://<SERVER_IP>:<PORT>/
```

### Credentials in URL

```bash
curl http://admin:admin@<SERVER_IP>:<PORT>/
```

### Authorization Header

```bash
curl -H 'Authorization: Basic YWRtaW46YWRtaW4=' http://<SERVER_IP>:<PORT>/
```

### GET Parameter

```text
/search.php?search=le
```

### Authenticated GET with Parameter

```bash
curl 'http://<SERVER_IP>:<PORT>/search.php?search=le' \
-H 'Authorization: Basic YWRtaW46YWRtaW4='
```

### DevTools

```text
Network → Copy → Copy as cURL
Network → Copy → Copy as Fetch
```

---

## Key Takeaway

**GET requests retrieve resources and commonly pass parameters through the URL query string. HTTP Basic Authentication sends Base64-encoded credentials through the `Authorization` header, which cURL can generate with `-u` or set manually with `-H`. Browser DevTools can reveal backend requests and reproduce them through cURL or JavaScript Fetch, which is particularly useful when analyzing web applications.**
