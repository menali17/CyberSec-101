# WordPress - Login

## Overview

After identifying valid WordPress users through **User Enumeration**, we can assess the authentication mechanisms to identify weak or compromised credentials.

WordPress authentication can be tested through two main interfaces:

1. **Login Page:** `/wp-login.php`
2. **XML-RPC Endpoint:** `/xmlrpc.php`

In an authorized penetration test, these interfaces can be assessed for weaknesses such as weak passwords, missing rate limiting, and inadequate brute-force protection.

---

## 1. WordPress Login Page

WordPress provides a default authentication interface at:

```text
http://TARGET/wp-login.php
```

This interface allows users to authenticate using their username and password.

Successful authentication provides access to the WordPress dashboard according to the user's assigned permissions.

---

## 2. XML-RPC Authentication

XML-RPC (XML Remote Procedure Call) is a protocol that allows applications to execute remote procedures using HTTP requests containing XML data.

WordPress supports XML-RPC through:

```text
http://TARGET/xmlrpc.php
```

Unlike the standard login page, XML-RPC allows us to interact with WordPress programmatically.

### Authentication Method

We can use the `wp.getUsersBlogs` method to test authentication with a known username and password.

### Command

```bash
curl -X POST \
-d "<methodCall><methodName>wp.getUsersBlogs</methodName><params><param><value>admin</value></param><param><value>CORRECT-PASSWORD</value></param></params></methodCall>" \
http://TARGET/xmlrpc.php
```

**Explanation:**

- `curl`: Sends the HTTP request.
- `-X POST`: Specifies the HTTP POST method.
- `-d`: Sends data in the request body.
- `methodCall`: Defines an XML-RPC method invocation.
- `methodName`: Specifies the remote procedure to execute.
- `wp.getUsersBlogs`: Retrieves information about blogs accessible to the authenticated user.
- `admin`: Username supplied for authentication.
- `CORRECT-PASSWORD`: Password supplied for authentication.

---

## 3. Successful Authentication

If the credentials are valid, WordPress may return an XML response containing information about the user's accessible blogs.

### Example Response

```xml
<?xml version="1.0" encoding="UTF-8"?>
<methodResponse>
  <params>
    <param>
      <value>
        <array>
          <data>
            <value>
              <struct>
                <member>
                  <name>isAdmin</name>
                  <value><boolean>1</boolean></value>
                </member>
                <member>
                  <name>blogName</name>
                  <value><string>Inlanefreight</string></value>
                </member>
              </struct>
            </value>
          </data>
        </array>
      </value>
    </param>
  </params>
</methodResponse>
```

### Important Fields

- `methodResponse`: Contains the XML-RPC response.
- `isAdmin`: Indicates whether the user has administrator privileges for that blog.
- `blogName`: Identifies the WordPress blog.
- `blogid`: Identifies the blog.
- `url`: Contains the blog URL.

**Important:**

```xml
<name>isAdmin</name>
<value><boolean>1</boolean></value>
```

The value `1` indicates that the authenticated user has administrator privileges for the corresponding blog.

A successful response confirms that the supplied credentials were accepted.

---

## 4. Invalid Credentials

If the username or password is incorrect, WordPress returns an XML-RPC fault.

### Command

```bash
curl -X POST \
-d "<methodCall><methodName>wp.getUsersBlogs</methodName><params><param><value>admin</value></param><param><value>WRONG-PASSWORD</value></param></params></methodCall>" \
http://TARGET/xmlrpc.php
```

### Example Response

```xml
<?xml version="1.0" encoding="UTF-8"?>
<methodResponse>
  <fault>
    <value>
      <struct>
        <member>
          <name>faultCode</name>
          <value><int>403</int></value>
        </member>
        <member>
          <name>faultString</name>
          <value>
            <string>Incorrect username or password.</string>
          </value>
        </member>
      </struct>
    </value>
  </fault>
</methodResponse>
```

### Understanding the Response

- `fault`: Indicates that the XML-RPC operation failed.
- `faultCode`: Contains an error code.
- `403`: Indicates an authentication failure in this example.
- `faultString`: Describes the error.

**Important distinction:** An XML-RPC `faultCode` of `403` is not necessarily an HTTP `403 Forbidden` response.

The server may return HTTP `200 OK` while including an XML-RPC error in the response body.

We can inspect both HTTP headers and the XML response with:

```bash
curl -i -X POST \
-H "Content-Type: text/xml" \
-d '<methodCall><methodName>wp.getUsersBlogs</methodName><params><param><value>admin</value></param><param><value>WRONG-PASSWORD</value></param></params></methodCall>' \
http://TARGET/xmlrpc.php
```

---

## 5. Security Considerations

The XML-RPC interface can become an attack surface when authentication protections are insufficient.

Potential security concerns include:

- Password brute-force attempts.
- Credential stuffing using previously compromised credentials.
- Insufficient authentication rate limiting.
- Exposure of unnecessary remote functionality.

However, the existence of `xmlrpc.php` does not automatically indicate a vulnerability.

Modern WordPress installations may restrict XML-RPC access through plugins, server configurations, or other security controls.

### Defensive Measures

- Enforce strong passwords.
- Enable multi-factor authentication where supported.
- Implement rate limiting.
- Monitor authentication failures.
- Restrict XML-RPC functionality when unnecessary.

---

## 6. Manual vs. Automated Testing

### Manual Testing

We manually construct HTTP requests and analyze responses.

Advantages:

- Better understanding of the authentication process.
- Easier identification of unusual server behavior.
- Greater control over individual requests.

### Automated Testing

Security assessment tools can automate enumeration and authentication testing.

Advantages:

- Faster execution.
- Ability to process larger sets of test cases.
- Consistent collection of results.

However, automation can generate significant traffic and potentially disrupt services.

We should understand the underlying manual techniques before relying on automated tools.

---

## 7. Useful Commands

### Access the Login Page

```bash
curl -I http://TARGET/wp-login.php
```

### Check the XML-RPC Endpoint

```bash
curl -i http://TARGET/xmlrpc.php
```

### Send an XML-RPC Authentication Request

```bash
curl -X POST \
-H "Content-Type: text/xml" \
-d '<methodCall><methodName>wp.getUsersBlogs</methodName><params><param><value>admin</value></param><param><value>PASSWORD</value></param></params></methodCall>' \
http://TARGET/xmlrpc.php
```

### Display HTTP Headers and Response Body

```bash
curl -i -X POST \
-H "Content-Type: text/xml" \
-d '<methodCall><methodName>wp.getUsersBlogs</methodName><params><param><value>admin</value></param><param><value>PASSWORD</value></param></params></methodCall>' \
http://TARGET/xmlrpc.php
```

---

## Key Takeaways

1. WordPress authentication is commonly accessible through `wp-login.php`.
2. XML-RPC provides an alternative programmatic interface through `xmlrpc.php`.
3. The `wp.getUsersBlogs` method can validate supplied credentials when XML-RPC authentication is available.
4. Successful authentication may reveal blog information and user privileges.
5. Invalid credentials may produce an XML-RPC `faultCode` of `403`.
6. XML-RPC error codes must not be confused with HTTP status codes.
7. Understanding manual authentication requests helps us interpret automated security testing results.
8. Authentication testing should be performed only against authorized targets.