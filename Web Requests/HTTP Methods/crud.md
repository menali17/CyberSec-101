# CRUD API

---

A web application may expose an **API endpoint** that allows data to be accessed and modified directly through HTTP requests.

Instead of interacting with a graphical interface, we can communicate with the API by combining:

* An API endpoint
* A resource or entity
* An identifier or search term
* An HTTP method
* Request data when required

For example:

```bash
curl -X PUT http://<SERVER_IP>:<PORT>/api.php/city/london
```

Here:

```text
/api.php        → API endpoint
/city           → Resource
/london         → Specific entity/search term
PUT             → Operation
```

The exact URL structure and behavior depend on how the API was designed.

---

# CRUD

`CRUD` represents four fundamental operations performed on data:

| Operation  | HTTP Method | Purpose                |
| ---------- | ----------- | ---------------------- |
| **Create** | `POST`      | Add new data           |
| **Read**   | `GET`       | Retrieve existing data |
| **Update** | `PUT`       | Modify existing data   |
| **Delete** | `DELETE`    | Remove existing data   |

A useful mapping is:

```text
Create → POST
Read   → GET
Update → PUT
Delete → DELETE
```

Although this pattern is common in CRUD and REST-style APIs, APIs do not necessarily implement these operations or URLs in exactly the same way. Authentication and authorization may also restrict which operations are available.

---

# Read — GET

The first operation is retrieving information from the API.

For the example API, we specify the resource and search term directly in the URL:

```bash
curl http://<SERVER_IP>:<PORT>/api.php/city/london
```

Output:

```bash
[{"city_name":"London","country_name":"(UK)"}]
```

The API returns the result as **JSON**.

## Formatting JSON with jq

The `jq` utility can parse and pretty-print JSON output.

```bash
curl -s http://<SERVER_IP>:<PORT>/api.php/city/london | jq
```

Output:

```bash
[
  {
    "city_name": "London",
    "country_name": "(UK)"
  }
]
```

Here:

| Component | Purpose                              |
| --------- | ------------------------------------ |
| `-s`      | Silences cURL progress information   |
| `\|`      | Sends cURL output to another command |
| `jq`      | Parses and formats JSON              |

## Searching for Multiple Entries

A less specific search term may return multiple matches:

```bash
curl -s http://<SERVER_IP>:<PORT>/api.php/city/le | jq
```

Output:

```bash
[
  {
    "city_name": "Leeds",
    "country_name": "(UK)"
  },
  {
    "city_name": "Dudley",
    "country_name": "(UK)"
  },
  {
    "city_name": "Leicester",
    "country_name": "(UK)"
  },
  ...SNIP...
]
```

## Retrieving All Entries

In this API, leaving the search term empty returns all entries:

```bash
curl -s http://<SERVER_IP>:<PORT>/api.php/city/ | jq
```

Output:

```bash
[
  {
    "city_name": "London",
    "country_name": "(UK)"
  },
  {
    "city_name": "Birmingham",
    "country_name": "(UK)"
  },
  {
    "city_name": "Leeds",
    "country_name": "(UK)"
  },
  ...SNIP...
]
```

Therefore, in this API:

```text
/city/london → Search for London
/city/le     → Search for entries matching "le"
/city/       → Retrieve all entries
```

---

# Create — POST

To create a new entry, we send a `POST` request containing the new data.

The API expects JSON, so we also specify:

```http
Content-Type: application/json
```

Example:

```bash
curl -X POST http://<SERVER_IP>:<PORT>/api.php/city/ \
  -d '{"city_name":"HTB_City", "country_name":"HTB"}' \
  -H 'Content-Type: application/json'
```

The request contains:

```json
{
  "city_name": "HTB_City",
  "country_name": "HTB"
}
```

We can verify that the entry was created with a GET request:

```bash
curl -s http://<SERVER_IP>:<PORT>/api.php/city/HTB_City | jq
```

Output:

```bash
[
  {
    "city_name": "HTB_City",
    "country_name": "HTB"
  }
]
```

The basic process is:

```text
POST new data
      ↓
API creates entry
      ↓
GET entry
      ↓
Confirm creation
```

---

# Update — PUT

The `PUT` method is commonly used to update an existing API resource.

In this example, we must identify the existing entity in the URL and provide its new data in the request body.

```bash
curl -X PUT http://<SERVER_IP>:<PORT>/api.php/city/london \
  -d '{"city_name":"New_HTB_City", "country_name":"HTB"}' \
  -H 'Content-Type: application/json'
```

The target is:

```text
/city/london
```

while the replacement data is:

```json
{
  "city_name": "New_HTB_City",
  "country_name": "HTB"
}
```

We can check whether the original entry still exists:

```bash
curl -s http://<SERVER_IP>:<PORT>/api.php/city/london | jq
```

Then retrieve the updated entry:

```bash
curl -s http://<SERVER_IP>:<PORT>/api.php/city/New_HTB_City | jq
```

Output:

```bash
[
  {
    "city_name": "New_HTB_City",
    "country_name": "HTB"
  }
]
```

In this example, the original `london` entry has been replaced with the updated information.

---

# PUT vs PATCH

Both `PUT` and `PATCH` may be used for updates, but they generally represent different operations.

| Method  | Typical Purpose                         |
| ------- | --------------------------------------- |
| `PUT`   | Replace or update the complete resource |
| `PATCH` | Partially modify a resource             |

For example, suppose an entry contains:

```json
{
  "city_name": "London",
  "country_name": "(UK)"
}
```

A `PATCH` request may modify only:

```json
{
  "city_name": "New_London"
}
```

while leaving the other fields unchanged.

`PUT` generally represents sending the complete updated representation.

The exact behavior still depends on the API implementation.

## OPTIONS

The `OPTIONS` HTTP method may help determine which HTTP methods an endpoint accepts.

For example:

```bash
curl -X OPTIONS http://<SERVER_IP>:<PORT>/api.php/city/london
```

The response may indicate whether methods such as `GET`, `POST`, `PUT`, `PATCH`, or `DELETE` are supported.

---

# PUT and Resource Creation

Some APIs implement `PUT` so that it can also create a resource when the requested resource does not already exist.

Conceptually:

```text
PUT /resource/existing
        ↓
Update resource

PUT /resource/non-existing
        ↓
May create resource
```

This behavior is API-dependent. The example API in this section does **not** behave this way.

---

# Delete — DELETE

The final CRUD operation removes an existing resource.

We specify the target resource and use the `DELETE` method:

```bash
curl -X DELETE http://<SERVER_IP>:<PORT>/api.php/city/New_HTB_City
```

We can then attempt to retrieve it:

```bash
curl -s http://<SERVER_IP>:<PORT>/api.php/city/New_HTB_City | jq
```

Output:

```bash
[]
```

The empty JSON array indicates that the entry is no longer returned by the API.

---

# Complete CRUD Example

Using the same resource, the four operations look like this:

### Create

```bash
curl -X POST http://<SERVER_IP>:<PORT>/api.php/city/ \
  -d '{"city_name":"HTB_City","country_name":"HTB"}' \
  -H 'Content-Type: application/json'
```

### Read

```bash
curl -s http://<SERVER_IP>:<PORT>/api.php/city/HTB_City | jq
```

### Update

```bash
curl -X PUT http://<SERVER_IP>:<PORT>/api.php/city/HTB_City \
  -d '{"city_name":"New_HTB_City","country_name":"HTB"}' \
  -H 'Content-Type: application/json'
```

### Delete

```bash
curl -X DELETE http://<SERVER_IP>:<PORT>/api.php/city/New_HTB_City
```

This gives us the complete lifecycle:

```text
POST   → Create
GET    → Read
PUT    → Update
DELETE → Delete
```

---

# Authentication and Authorization

Real APIs generally restrict which users can perform particular operations.

For example, an application may allow:

```text
Regular user
    GET    ✓
    POST   ✗
    PUT    ✗
    DELETE ✗

Administrator
    GET    ✓
    POST   ✓
    PUT    ✓
    DELETE ✓
```

The exact permissions depend on the application.

Authentication information may be supplied through mechanisms already covered, such as:

```http
Cookie: PHPSESSID=<SESSION_ID>
```

or an authorization header, for example:

```http
Authorization: Bearer <TOKEN>
```

Having access to an API endpoint does **not** automatically mean every CRUD operation should be available. If unauthorized users can create, modify, or delete protected resources, this may represent an access-control vulnerability.

---

# Why Direct API Interaction Matters

A web application's frontend is often only an interface for requests sent to backend endpoints.

Instead of:

```text
Browser interface → JavaScript → API → Data
```

we can communicate directly with the API:

```text
cURL → API → Data
```

This allows us to inspect and test the underlying requests without depending on the graphical interface.

During web application assessments, this is useful for understanding:

* Available API endpoints
* Accepted HTTP methods
* Request parameters
* JSON structures
* Authentication requirements
* Authorization restrictions
* API responses

---

# Quick Reference

| Operation               | Method    | Example               |
| ----------------------- | --------- | --------------------- |
| Create                  | `POST`    | `curl -X POST ...`    |
| Read                    | `GET`     | `curl URL`            |
| Update                  | `PUT`     | `curl -X PUT ...`     |
| Partial Update          | `PATCH`   | `curl -X PATCH ...`   |
| Delete                  | `DELETE`  | `curl -X DELETE ...`  |
| Check supported methods | `OPTIONS` | `curl -X OPTIONS ...` |

Useful cURL options:

| Option      | Purpose                |
| ----------- | ---------------------- |
| `-X METHOD` | Specify HTTP method    |
| `-d`        | Send request body data |
| `-H`        | Add a request header   |
| `-s`        | Silent mode            |
| `-b`        | Send cookies           |

JSON formatting:

```bash
curl -s <URL> | jq
```

---

# Key Takeaway

CRUD represents the four fundamental data operations commonly exposed by APIs:

```text
Create → POST
Read   → GET
Update → PUT
Delete → DELETE
```

The HTTP method tells the API **what operation we want to perform**, while the URL identifies **which resource we want to operate on**. When data must be supplied, it can be placed in the request body, commonly as JSON.

Understanding this relationship makes it possible to interact with API endpoints directly using tools such as cURL, inspect their behavior, and determine how authentication and authorization control access to their resources.
