# MySQL

---

## Overview

MySQL is an open-source Relational Database Management System (RDBMS).

It uses SQL (Structured Query Language) to store, retrieve, modify, and delete data.

MySQL follows a client-server architecture:

- **MySQL Server:** Manages databases and processes queries.
- **MySQL Client:** Connects to the server and executes SQL commands.

MySQL is commonly used in web applications, including WordPress.

### Default Port

- **TCP 3306:** MySQL database service.

### MySQL vs. MariaDB

MariaDB is a fork of MySQL.

Both systems share many SQL commands and database management features.

---

## Database Structure

MySQL organizes information into databases and tables.

- **Database:** Collection of related tables.
- **Table:** Collection of records organized into rows and columns.
- **Column:** Defines an attribute and its data type.
- **Row:** Represents an individual record.

Example:

| id | username | email |
|---|---|---|
| 1 | robin | robin@example.com |
| 2 | admin | admin@example.com |

---

## Default Configuration

A common MySQL configuration file on Ubuntu is:

`/etc/mysql/mysql.conf.d/mysqld.cnf`

Important settings include:

| Setting | Description |
|---|---|
| `port` | Defines the MySQL listening port |
| `user` | Defines the OS user running MySQL |
| `datadir` | Database storage directory |
| `admin_address` | Administrative network interface address |
| `secure_file_priv` | Restricts certain file import/export operations |
| `debug` | Controls debugging behavior |

Misconfigurations may expose database information or enable unauthorized access.

---

## Footprinting MySQL

### 1. Service Enumeration

```bash
nmap -sV -sC -p3306 <IP>
```

- `-sV`: Detects service versions.
- `-sC`: Runs default NSE scripts.
- `-p3306`: Scans the MySQL port.

### MySQL NSE Scripts

```bash
nmap -sV -sC -p3306 --script 'mysql*' <IP>
```

The `mysql*` pattern selects MySQL-related NSE scripts.

Some scripts perform authentication attempts or brute-force checks and are more intrusive.

### Important Lesson: False Positives

Automated enumeration tools may return incorrect results.

For example, a script may incorrectly report that the `root` account has an empty password.

We should manually verify important findings.

---

## Connecting to MySQL

```bash
mysql -u <username> -h <IP> -p
```

- `-u`: Specifies the username.
- `-h`: Specifies the server address.
- `-p`: Prompts for the password.

Once authenticated, we can execute SQL statements.

---

## MySQL Enumeration Commands

### 1. List Databases

```sql
SHOW DATABASES;
```

Displays accessible databases.

### 2. Select a Database

```sql
USE <database>;
```

Selects a database for subsequent queries.

### 3. List Tables

```sql
SHOW TABLES;
```

Lists tables in the selected database.

### 4. Enumerate Columns

```sql
SHOW COLUMNS FROM <table>;
```

Displays the columns and their definitions.

### 5. Retrieve Records

```sql
SELECT * FROM <table>;
```

Retrieves all columns and rows from a table.

### 6. Filter Records

```sql
SELECT * FROM <table> WHERE <column> = 'value';
```

Retrieves records matching a specified condition.

### 7. Identify Server Version

```sql
SELECT VERSION();
```

Returns the database server version.

---

## System Databases

MySQL commonly includes several internal databases:

| Database | Purpose |
|---|---|
| `information_schema` | Provides metadata about database objects |
| `mysql` | Stores internal system and privilege information |
| `performance_schema` | Collects performance and instrumentation data |
| `sys` | Provides simplified views of performance information |

Access to internal information depends on user permissions.

---

## MySQL TLS Connection Issues

When connecting to a MySQL server, we may encounter TLS certificate validation errors.

Example:

```text
ERROR 2026 (HY000): TLS/SSL error:
self-signed certificate in certificate chain
```

### MySQL Client

```bash
mysql -u <user> -h <IP> -p --ssl-mode=REQUIRED
```

- `--ssl-mode=REQUIRED`: Requires TLS encryption without enforcing certificate verification by default.

### MariaDB Client

```bash
mysql -u <user> -h <IP> -p --disable-ssl-verify-server-cert
```

- `--disable-ssl-verify-server-cert`: Disables server certificate verification.


## Security Considerations

Common MySQL security concerns include:

- Exposed database services.
- Weak or missing authentication.
- Excessive user privileges.
- Sensitive data stored insecurely.
- Exposed configuration files.
- Information disclosure through error messages.
- SQL injection vulnerabilities in applications.

MySQL exposure does not automatically mean the server is vulnerable.

Findings must be validated before drawing conclusions.

---

## Enumeration Methodology

1. Discover the MySQL service using Nmap.
2. Identify available service information.
3. Verify potential authentication findings.
4. Authenticate using valid credentials.
5. Enumerate databases with `SHOW DATABASES`.
6. Select a database with `USE`.
7. Enumerate tables with `SHOW TABLES`.
8. Inspect table structures and records using SQL queries.
9. Analyze potentially sensitive information.

**Our objective is to identify the database service, understand its configuration, and enumerate accessible information.**