# WordPress - User Enumeration

## Overview

User Enumeration is the process of identifying valid users registered on a WordPress website.

During a security assessment, discovering valid usernames can help us evaluate authentication security, including weak passwords and default credentials.

There are two common methods for manual WordPress user enumeration:

1. Author ID Enumeration
2. WordPress REST API Enumeration

---

## 1. Author ID Enumeration

WordPress assigns a numeric ID to each registered user.

For example:

- ID 1 → admin
- ID 2 → ch4p
- ID 3 → john

We can enumerate authors by manipulating the `author` parameter in the URL.

### Command

```bash
curl -s -I http://TARGET/?author=1
```

**Options:**

- `-s`: Silent mode.
- `-I`: Request only HTTP response headers.
- `?author=1`: Query the author associated with ID 1.

### Existing User

```http
HTTP/1.1 301 Moved Permanently
X-Redirect-By: WordPress
Location: http://TARGET/index.php/author/admin/
```

**Explanation:**

- `301 Moved Permanently`: The server redirects the request.
- `Location`: Indicates the destination URL.
- `/author/admin/`: Reveals the author's public username/slug.

We can conclude that author ID `1` is associated with `admin`.

### Non-Existing User

```bash
curl -s -I http://TARGET/?author=100
```

Example response:

```http
HTTP/1.1 404 Not Found
```

A `404 Not Found` indicates that the requested resource could not be found.

In this HTB example, different HTTP responses allow us to distinguish valid and invalid author IDs.

**Note:** HTTP behavior depends on the WordPress configuration. A redirect does not always guarantee that the author exists.

---

## 2. WordPress REST API Enumeration

WordPress provides a REST API that allows applications to retrieve information using HTTP requests.

We can query the users endpoint to discover publicly accessible author information.

### Endpoint

```text
/wp-json/wp/v2/users
```

### Command

```bash
curl -s http://TARGET/wp-json/wp/v2/users | jq
```

**Explanation:**

- `curl -s`: Sends an HTTP request in silent mode.
- `|`: Pipes the output into another command.
- `jq`: Formats and processes JSON data.

### Example Response

```json
[
  {
    "id": 1,
    "name": "admin",
    "slug": "admin",
    "link": "http://TARGET/author/admin/"
  },
  {
    "id": 2,
    "name": "ch4p",
    "slug": "ch4p",
    "link": "http://TARGET/author/ch4p/"
  }
]
```

### Extracting User Information

We can use `jq` to extract specific fields.

**Display author names:**

```bash
curl -s http://TARGET/wp-json/wp/v2/users | jq '.[].name'
```

**Display author IDs:**

```bash
curl -s http://TARGET/wp-json/wp/v2/users | jq '.[].id'
```

**Display author slugs:**

```bash
curl -s http://TARGET/wp-json/wp/v2/users | jq '.[].slug'
```

**Display IDs and names together:**

```bash
curl -s http://TARGET/wp-json/wp/v2/users | jq '.[] | {id, name}'
```

**Extract names without quotation marks:**

```bash
curl -s http://TARGET/wp-json/wp/v2/users | jq -r '.[].name'
```

The `-r` option outputs raw strings without surrounding quotation marks.

### Important Considerations

- The REST API may expose publicly available author information.
- Access depends on the WordPress version and configuration.
- Modern WordPress installations may still expose public authors.
- Not every registered user is necessarily returned.
- Public author names and slugs do not always match login usernames.

---

## 3. Comparing Both Methods

| Method | Technique | Information |
|--------|-----------|-------------|
| Author ID | `?author=1` | Author slug through redirects |
| REST API | `/wp-json/wp/v2/users` | Public user information in JSON |

### Author ID Enumeration

- Queries individual author IDs.
- Examines HTTP response headers.
- May reveal author slugs through redirects.

### REST API Enumeration

- Queries a JSON endpoint.
- May return multiple authors in one request.
- Allows filtering results using `jq`.

---

## 4. Useful Commands

### Check HTTP Headers

```bash
curl -I http://TARGET/
```

### Enumerate an Author

```bash
curl -s -I http://TARGET/?author=1
```

### Follow Redirects

```bash
curl -s -L http://TARGET/?author=1
```

### Query WordPress Users

```bash
curl -s http://TARGET/wp-json/wp/v2/users | jq
```

### Extract Public Author Names

```bash
curl -s http://TARGET/wp-json/wp/v2/users | jq -r '.[].name'
```

---

## Key Takeaways

1. User Enumeration helps us identify valid WordPress accounts.
2. WordPress assigns numeric IDs to registered users.
3. The `author` parameter can reveal public author slugs.
4. HTTP redirects may expose information through the `Location` header.
5. The WordPress REST API may expose public author data.
6. `jq` allows us to extract information from JSON responses.
7. Identifying a public author does not guarantee that we know the corresponding login username.