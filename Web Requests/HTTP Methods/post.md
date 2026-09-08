# POST Requests

---

HTTP `POST` requests are commonly used when a web application needs to send data in the **request body** rather than placing it directly in the URL. This is especially useful for login forms, file uploads, API requests, and other operations involving larger or structured data.

## GET vs POST Data

The main difference in this context is where parameters are sent.

| GET                                         | POST                                               |
| ------------------------------------------- | -------------------------------------------------- |
| Parameters commonly appear in the URL       | Parameters are placed in the request body          |
| Limited by practical URL-length constraints | Can send considerably more data                    |
| Parameters are visible in the URL           | Parameters are not placed in the URL               |
| Common for retrieving/searching data        | Common for forms, uploads, and structured requests |

For example, a GET request may contain:

```http
GET /search.php?search=london HTTP/1.1
```

A POST request can instead send the parameter in its body:

```http
POST /search.php HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded

search=london
```

Moving data into the body avoids URL-length limitations and makes POST suitable for binary or larger payloads.

> POST does **not** inherently provide confidentiality. If HTTP is used instead of HTTPS, POST body data can still be intercepted.

---

# Login Forms

A common use of POST is submitting authentication credentials.

Suppose a login form receives:

```text
username=admin&password=admin
```

Browser DevTools can reveal this by opening the **Network** tab, selecting the login request, and inspecting its request body.

We can reproduce the same request manually with cURL:

```bash
curl -X POST -d 'username=admin&password=admin' http://<SERVER_IP>:<PORT>/
```

Here:

| Option    | Purpose                            |
| --------- | ---------------------------------- |
| `-X POST` | Explicitly selects the POST method |
| `-d`      | Adds data to the request body      |

The server may then return the authenticated version of the page:

```bash
...SNIP...
<em>Type a city name and hit <strong>Enter</strong></em>
...SNIP...
```

If authentication causes an HTTP redirect, cURL can follow it with:

```bash
curl -L -X POST -d 'username=admin&password=admin' http://<SERVER_IP>:<PORT>/
```

The `-L` option follows redirects such as a login redirect to `/dashboard.php`.

---

# Authenticated Cookies

After successful authentication, a web application may return a session cookie so that credentials do not need to be submitted with every request.

We can inspect response headers with:

```bash
curl -X POST -d 'username=admin&password=admin' http://<SERVER_IP>:<PORT>/ -i
```

Example response:

```bash
HTTP/1.1 200 OK
Server: Apache/2.4.41 (Ubuntu)
Set-Cookie: PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1; path=/

...SNIP...
<em>Type a city name and hit <strong>Enter</strong></em>
...SNIP...
```

The important header is:

```http
Set-Cookie: PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1; path=/
```

The server is assigning a session identifier named `PHPSESSID`.

## Sending the Cookie

Once we have a valid session cookie, cURL can send it using `-b`:

```bash
curl -b 'PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1' http://<SERVER_IP>:<PORT>/
```

The authenticated session can therefore be reused without submitting the username and password again.

We can also manually specify the `Cookie` header:

```bash
curl -H 'Cookie: PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1' http://<SERVER_IP>:<PORT>/
```

These approaches produce the same essential HTTP behavior:

```http
Cookie: PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1
```

## Cookies in Browser DevTools

Browser DevTools can also inspect and modify cookies.

For the example application, the workflow is:

1. Open **Storage**.
2. Select **Cookies**.
3. Select the target website.
4. Locate `PHPSESSID`.
5. Inspect or replace its value.
6. Refresh the page.

If the session identifier corresponds to an authenticated session, the application may recognize the browser as authenticated without requiring another login.

This demonstrates an important security concept:

> A valid authenticated session cookie may effectively represent the authenticated session itself.

Protecting session cookies is therefore critical.

---

# JSON Data

POST bodies are not limited to traditional form parameters.

Modern web applications frequently exchange structured data using **JSON**.

For example, the City Search functionality sends:

```json
{"search":"london"}
```

to:

```text
/search.php
```

The request uses the header:

```http
Content-Type: application/json
```

which tells the server how to interpret the request body.

A simplified version of the HTTP request is:

```http
POST /search.php HTTP/1.1
Host: server_ip
Content-Type: application/json
Cookie: PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1

{"search":"london"}
```

The captured request also contains normal browser headers such as `User-Agent`, `Accept`, `Referer`, `Origin`, and `Content-Length`.

---

# Sending JSON with cURL

We can reproduce the browser request directly:

```bash
curl -X POST \
  -d '{"search":"london"}' \
  -b 'PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1' \
  -H 'Content-Type: application/json' \
  http://<SERVER_IP>:<PORT>/search.php
```

Response:

```bash
["London (UK)"]
```

The options perform different parts of the request:

| Option    | Purpose                        |
| --------- | ------------------------------ |
| `-X POST` | Sends a POST request           |
| `-d`      | Supplies the request body      |
| `-b`      | Sends the authenticated cookie |
| `-H`      | Adds a custom HTTP header      |

In this case:

```bash
-H 'Content-Type: application/json'
```

informs the application that:

```bash
-d '{"search":"london"}'
```

contains JSON.

The request can interact directly with the backend endpoint without using the application's graphical interface.

---

# Why Headers Matter

A POST request is more than just its body.

Consider:

```bash
curl -X POST -d '{"search":"london"}' http://<SERVER_IP>:<PORT>/search.php
```

Compared with:

```bash
curl -X POST \
  -d '{"search":"london"}' \
  -b 'PHPSESSID=<SESSION_ID>' \
  -H 'Content-Type: application/json' \
  http://<SERVER_IP>:<PORT>/search.php
```

The second request provides additional context expected by the application:

```text
Cookie
    → Which authenticated session is making the request?

Content-Type
    → How should the server interpret the request body?
```

Removing either can change the application's response. The HTB exercise specifically recommends testing the request without the cookie or `Content-Type` header and observing the difference.

---

# Copying Requests from DevTools

Browser DevTools can help reproduce existing requests.

From the **Network** tab, we can right-click a request and use:

```text
Copy → Copy as cURL
```

This generates a cURL command representing the browser request.

For JavaScript, DevTools can also provide:

```text
Copy → Copy as Fetch
```

The resulting `fetch()` code can be executed from the browser console and modified to test different request data.

Manually understanding and constructing requests is still important because it allows us to determine exactly which components are required.

---

# Quick Reference

| Task                    | Command                                                                     |
| ----------------------- | --------------------------------------------------------------------------- |
| Send POST data          | `curl -X POST -d 'data' URL`                                                |
| Send form parameters    | `curl -X POST -d 'username=admin&password=admin' URL`                       |
| Follow redirects        | `curl -L URL`                                                               |
| Show response headers   | `curl -i URL`                                                               |
| Send cookie             | `curl -b 'name=value' URL`                                                  |
| Send cookie manually    | `curl -H 'Cookie: name=value' URL`                                          |
| Add header              | `curl -H 'Header: Value' URL`                                               |
| Send JSON               | `curl -X POST -d '{"key":"value"}' -H 'Content-Type: application/json' URL` |
| Copy browser request    | DevTools → Network → **Copy as cURL**                                       |
| Copy JavaScript request | DevTools → Network → **Copy as Fetch**                                      |

---

# Key Takeaway

A POST request allows data to be placed in the **HTTP request body**, making it suitable for login forms, larger payloads, and structured formats such as JSON.

The most important concepts from this section are:

```text
POST body      → Data being sent
Content-Type   → Format of that data
Cookie         → Session/authentication state
```

Using browser DevTools, we can observe exactly what a web application sends. Using cURL or `fetch()`, we can then reproduce those requests directly, modify their parameters, and interact with backend endpoints without relying on the application's graphical interface.
