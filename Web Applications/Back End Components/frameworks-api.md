# Development Frameworks & APIs

## Development Frameworks

Modern web applications are often built using **development frameworks**.

Frameworks provide ready-made structures and functionality that make application development faster and easier.

Common features include:

* User registration
* Authentication
* Routing
* Database interaction
* API creation
* Form handling

Some common frameworks are:

| Framework | Language             |
| --------- | -------------------- |
| Laravel   | PHP                  |
| Express   | Node.js / JavaScript |
| Django    | Python               |
| Rails     | Ruby                 |

Large web applications may use multiple frameworks and technologies at the same time.

---

# APIs

An **API (Application Programming Interface)** defines how applications or components communicate with each other.

In web applications, APIs commonly connect:

```text
Front End
    ↓
HTTP Request
    ↓
API
    ↓
Back End
    ↓
Processing
    ↓
API Response
    ↓
Front End
```

The front end sends a request asking the back end to perform a specific action.

The back end:

1. Receives the request
2. Processes it
3. Performs the required action
4. Returns a response

---

# Query Parameters

Web applications commonly receive input through HTTP parameters.

Two common methods are:

```text
GET
POST
```

---

## GET Parameters

GET parameters are commonly included directly in the URL.

Example:

```text
/search.php?item=apples
```

Here:

```text
/search.php → Resource

item        → Parameter

apples      → Value
```

The general structure is:

```text
page?parameter=value
```

For example:

```text
/search.php?item=apples
```

means:

```text
item = apples
```

---

## POST Parameters

POST parameters are normally sent inside the HTTP request body.

Example:

```http
POST /search.php HTTP/1.1

item=apples
```

The parameter is:

```text
item
```

and its value is:

```text
apples
```

Unlike GET parameters, POST data is not usually placed directly in the URL.

---

# GET vs POST Parameters

A simple comparison:

```text
GET

/search.php?item=apples
             ↑
       Parameter in URL
```

```text
POST

POST /search.php

item=apples
↑
Parameter in request body
```

Both methods allow the front end to send data to the back end.

---

# Web APIs

A **Web API** exposes application functionality over protocols such as HTTP.

For example, a weather application may provide an API such as:

```text
/weather/brasilia
```

The back end may return:

```json
{
    "city": "Brasilia",
    "temperature": 25
}
```

The front end receives this information and displays it to us.

Conceptually:

```text
Browser
   ↓
GET /weather/brasilia
   ↓
API
   ↓
Weather Data
   ↓
JSON Response
   ↓
Browser
```

---

# API Responses

Web APIs commonly return structured data.

Common formats include:

```text
JSON
XML
```

JSON is especially common in modern web applications.

Example:

```json
{
    "username": "enzo",
    "role": "user"
}
```

This makes it easy for applications and JavaScript to process the response.

---

# API Standards

Two important API standards are:

```text
SOAP
REST
```

---

# SOAP

**SOAP (Simple Object Access Protocol)** commonly exchanges structured data using **XML**.

A SOAP request and response are usually represented in XML.

Example:

```xml
<?xml version="1.0"?>

<soap:Envelope>
    <soap:Header>
    </soap:Header>

    <soap:Body>
    </soap:Body>
</soap:Envelope>
```

SOAP can be useful when transferring:

* Complex structured data
* Serialized objects
* Binary data
* Stateful information

However, SOAP requests can be relatively complex and verbose.

---

# REST

**REST (Representational State Transfer)** is commonly used in modern web APIs.

REST APIs often use:

* URL paths to identify resources
* HTTP methods to define actions
* JSON for responses

Example:

```text
GET /users/1
```

This may represent:

```text
Retrieve user with ID 1
```

The server may respond with:

```json
{
    "id": 1,
    "username": "enzo"
}
```

---

# REST Paths

REST APIs commonly identify resources through URL paths.

Example:

```text
/users/1
```

Here:

```text
users → Resource

1 → Specific user
```

Other examples:

```text
/posts/10
/categories/2
/products/50
```

---

# Query Parameters vs REST Paths

Query parameter example:

```text
/search.php?user=1
```

REST-style example:

```text
/users/1
```

Conceptually:

```text
Query Parameter
/users?user=1

REST Path
/users/1
```

Both can send information to the application, but they organize input differently.

---

# HTTP Methods in REST

REST APIs commonly use HTTP methods to represent different actions.

## GET

Used to retrieve data.

```http
GET /users/1
```

Conceptually:

```text
GET → Read
```

---

## POST

Used to create new data.

```http
POST /users
```

Conceptually:

```text
POST → Create
```

POST is normally **non-idempotent**.

This means repeating the same request may produce additional changes.

Example:

```text
POST /users
```

executed twice may create:

```text
User 1
User 2
```

---

## PUT

Used to create or replace data.

```http
PUT /users/1
```

Conceptually:

```text
PUT → Create / Replace
```

PUT is usually **idempotent**.

This means repeating the same request should produce the same final state.

Example:

```text
PUT /users/1
name=Enzo
```

Sending it multiple times should still leave:

```text
name = Enzo
```

---

## DELETE

Used to remove data.

```http
DELETE /users/1
```

Conceptually:

```text
DELETE → Remove
```

---

# CRUD and HTTP Methods

REST operations closely relate to **CRUD**:

```text
CRUD        HTTP

Create  →   POST
Read    →   GET
Update  →   PUT
Delete  →   DELETE
```

This mapping is very important when working with APIs.

---

# Example REST API

Suppose we have:

```text
/users
```

We may interact with it using:

```http
GET /users
```

Retrieve users.

```http
GET /users/1
```

Retrieve user `1`.

```http
POST /users
```

Create a user.

```http
PUT /users/1
```

Replace or update user `1`.

```http
DELETE /users/1
```

Delete user `1`.

---

# REST Response Example

An API request such as:

```http
GET /category/posts/
```

may return:

```json
{
    "100001": {
        "date": "01-01-2021",
        "content": "Welcome to this web application."
    },
    "100002": {
        "date": "02-01-2021",
        "content": "This is the first post on this web app."
    }
}
```

The front end can parse this JSON and display the content.

---

# API Authentication

Some API functionality may only be available to authenticated users.

For example:

```text
GET /posts
```

may be public, while:

```text
POST /posts
```

may require authentication.

Conceptually:

```text
Request
   ↓
API
   ↓
Authentication Check
   ↓
Authorization Check
   ↓
Action Performed
```

---

# Pentesting Perspective

APIs are extremely important in web penetration testing because they expose back-end functionality.

When analyzing an application, we should look for:

```text
API endpoints

GET parameters

POST parameters

URL paths

HTTP methods

JSON data

Authentication requirements

Authorization checks
```

For example, if we find:

```http
GET /users/1
```

we may wonder:

```text
What happens with /users/2?

Can we access another user's data?

Does the API verify authorization?
```

This can lead to vulnerabilities such as:

```text
IDOR
Broken Access Control
```

---

# Parameters as Attack Surface

Every parameter controlled by us may become an attack surface.

Example:

```text
/search.php?item=apples
```

The interesting value is:

```text
item=apples
```

During testing, we want to understand:

```text
Where does item go?

How does the server process it?

Does it reach a database?

Is it reflected in the response?

Does it affect application logic?
```

The general flow is:

```text
User-Controlled Parameter
        ↓
API / Application
        ↓
Back-End Processing
        ↓
Database / Service / Function
```

---

# Key Takeaways

* Development frameworks simplify building modern web applications.
* Laravel uses PHP.
* Express uses Node.js.
* Django uses Python.
* Rails uses Ruby.
* APIs allow application components to communicate.
* Web APIs commonly use HTTP.
* GET parameters are commonly passed through the URL.
* POST parameters are commonly passed through the request body.
* APIs often return JSON or XML.
* SOAP mainly uses XML.
* REST commonly uses URLs, HTTP methods, and JSON.
* REST APIs divide application functionality into resources.
* `GET` retrieves data.
* `POST` creates data.
* `PUT` creates or replaces data.
* `DELETE` removes data.
* APIs are a major attack surface during web application penetration testing.

---

# Pentesting Mindset

When we discover an API endpoint, we should ask:

```text
What does this endpoint do?

Which parameters does it accept?

Which HTTP methods are allowed?

Does it require authentication?

Does it properly verify authorization?

Can we change resource IDs?

Can we modify parameters?

Where does our input go?

What information does the response reveal?
```

A useful mental model is:

```text
Front End
   ↓
HTTP Request
   ↓
API Endpoint
   ↓
Parameters
   ↓
Back-End Logic
   ↓
Database / Service
   ↓
HTTP Response
```
