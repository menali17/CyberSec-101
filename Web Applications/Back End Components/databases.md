# Databases

## Overview

Web applications use **databases** to store and retrieve information.

This may include:

* Usernames and passwords
* Posts and comments
* Files and images
* Application content
* User-specific data

Databases allow web applications to provide **dynamic content** and efficiently manage large amounts of information.

Important database characteristics include:

* Speed
* Storage capacity
* Scalability
* Cost

---

# Relational Databases (SQL)

**Relational databases** store data using:

```text
Tables
Rows
Columns
```

Tables can also be connected through **keys**.

Example:

```text
users
--------------------------------
id | username | first_name
1  | enzo     | Enzo
2  | admin    | John
```

Another table may contain posts:

```text
posts
--------------------------------
id | user_id | content
1  | 1       | Hello World
2  | 2       | Admin Post
```

The field:

```text
users.id
```

can be connected to:

```text
posts.user_id
```

This creates a relationship between the two tables.

---

# Keys and Relationships

A **key** identifies or connects data.

For example:

```text
users.id = 1
```

can identify:

```text
Enzo
```

while:

```text
posts.user_id = 1
```

indicates that the post belongs to that user.

Conceptually:

```text
Users Table
    |
    | id
    v
Posts Table
    |
    | post id
    v
Comments Table
```

These relationships form the database **schema**.

---

# Common SQL Databases

Some common relational databases include:

* MySQL
* Microsoft SQL Server (MSSQL)
* Oracle
* PostgreSQL
* SQLite
* MariaDB

### MySQL

A widely used, free, and open-source relational database.

### MSSQL

Microsoft's relational database system.

It is commonly found with:

```text
Windows Server
IIS
.NET
```

### Oracle

Common in large enterprise environments.

### PostgreSQL

Free and open source, with strong extensibility.

---

# Non-Relational Databases (NoSQL)

**NoSQL databases** do not necessarily use traditional tables, rows, columns, or relational schemas.

They are generally more flexible when dealing with data that does not follow a strict structure.

Common NoSQL models include:

1. Key-Value
2. Document-Based
3. Wide-Column
4. Graph

---

# Key-Value Model

The **Key-Value** model stores information as pairs:

```text
Key → Value
```

Example:

```json
{
  "100001": {
    "date": "01-01-2021",
    "content": "Welcome to this web application."
  }
}
```

Here:

```text
100001 → Key
```

and:

```json
{
  "date": "01-01-2021",
  "content": "Welcome to this web application."
}
```

is the value.

This is similar to dictionaries or maps in programming languages.

---

# Document-Based Model

Document databases store information in structured documents, commonly using JSON-like objects.

Example:

```json
{
  "username": "enzo",
  "email": "enzo@example.com",
  "roles": ["user"]
}
```

Each document can contain different fields and structures.

---

# Common NoSQL Databases

Some common NoSQL databases include:

* MongoDB
* Elasticsearch
* Apache Cassandra
* Redis
* Neo4j
* CouchDB
* Amazon DynamoDB

### MongoDB

A common document-based NoSQL database.

It stores data using JSON-like documents.

### Elasticsearch

Optimized for storing, searching, and analyzing large datasets.

### Apache Cassandra

Designed for scalability and handling large distributed datasets.

---

# SQL vs NoSQL

A simple comparison:

```text
SQL
→ Structured data
→ Tables
→ Rows
→ Columns
→ Relationships

NoSQL
→ Flexible data
→ Different storage models
→ Often JSON-like structures
→ High scalability
```

A useful way to remember:

```text
SQL   → Structured and relational

NoSQL → Flexible and non-relational
```

---

# Databases in Web Applications

A web application connects to a database through its back-end code.

Example in PHP:

```php
$conn = new mysqli("localhost", "user", "pass");
```

This creates a connection to a MySQL server.

A database can then be created:

```php
$sql = "CREATE DATABASE database1";
$conn->query($sql);
```

The application may later connect to it:

```php
$conn = new mysqli(
    "localhost",
    "user",
    "pass",
    "database1"
);
```

---

# Querying the Database

The application can retrieve data using SQL queries.

Example:

```php
$query = "select * from table_1";
$result = $conn->query($query);
```

The SQL query:

```sql
SELECT * FROM table_1;
```

means:

```text
SELECT
→ Retrieve data

*
→ All columns

FROM table_1
→ From this table
```

---

# User Input and Database Queries

Web applications frequently use user-controlled input when querying databases.

For example:

```php
$searchInput = $_POST['findUser'];

$query =
"select * from users where name like '%$searchInput%'";

$result = $conn->query($query);
```

The flow is:

```text
User Input
    ↓
Web Application
    ↓
SQL Query
    ↓
Database
    ↓
Results
    ↓
User
```

For example, if we search for:

```text
Enzo
```

the query may become:

```sql
SELECT *
FROM users
WHERE name LIKE '%Enzo%';
```

---

# SQL Injection Risk

The previous example directly inserts user-controlled input into the SQL query:

```php
"... name like '%$searchInput%'"
```

This can be dangerous if the input is not handled securely.

Conceptually:

```text
User Input
    ↓
Directly inserted into SQL
    ↓
Query structure may be modified
    ↓
SQL Injection
```

This is one of the main reasons databases are important in web penetration testing.

---

# Application Response

After retrieving information from the database, the application may return it to the user.

Example:

```php
while($row = $result->fetch_assoc()) {
    echo $row["name"]."<br>";
}
```

The full flow becomes:

```text
User
 ↓
HTTP Request
 ↓
Web Application
 ↓
Database Query
 ↓
Database
 ↓
Query Result
 ↓
Web Application
 ↓
HTTP Response
 ↓
User
```

---

# Pentesting Perspective

When analyzing a web application, we should identify where user-controlled input may interact with a database.

Useful questions include:

```text
Does our input reach a database query?

Which database technology is being used?

Is the application using SQL or NoSQL?

Is user input directly inserted into queries?

Are queries properly parameterized?

Can database errors reveal information?
```

A particularly important pattern is:

```text
User Input
    ↓
SQL Query
```

If input is improperly handled, this may create a path toward:

```text
SQL Injection
```

---

# Key Takeaways

* Databases store application and user data.
* Relational databases use tables, rows, columns, and relationships.
* SQL databases include MySQL, MSSQL, Oracle, and PostgreSQL.
* Keys connect related information between tables.
* The structure and relationships between tables form a schema.
* NoSQL databases use more flexible storage models.
* Common NoSQL models include Key-Value, Document, Wide-Column, and Graph.
* MongoDB is a common document-based NoSQL database.
* Web applications interact with databases through back-end code.
* User input is frequently used in database queries.
* Improper handling of user input can lead to SQL Injection.

---

# Pentesting Mindset

The most important flow to understand is:

```text
User Input
    ↓
Back-End Application
    ↓
Database Query
    ↓
Database
```

Whenever our input reaches a database query, we should consider whether the application handles that input securely.
