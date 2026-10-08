# Oracle TNS (Transparent Network Substrate)

---

## Overview

Oracle TNS is a networking protocol used by Oracle Net Services to facilitate communication between database clients and Oracle database servers.

It supports:

- Network communication between clients and databases.
- Name resolution.
- Connection management.
- Load balancing.
- Optional encryption and security mechanisms.

### Default Port

- **TCP 1521:** Default Oracle TNS Listener port.

---

## Oracle TNS Listener

The TNS Listener is a server-side process that receives incoming database connection requests.

It helps clients establish connections with the appropriate Oracle database service or instance.

The Listener is not the database itself.

### Configuration Files

| File | Description |
|---|---|
| `tnsnames.ora` | Client-side database connection definitions |
| `listener.ora` | Server-side Listener configuration |

Common directory:

```text
$ORACLE_HOME/network/admin/
```

### Important Connection Parameters

| Parameter | Description |
|---|---|
| `HOST` | Server address |
| `PORT` | Listener port |
| `PROTOCOL` | Network protocol |
| `SERVICE_NAME` | Logical database service name |
| `SID` | Identifier of a database instance |
| `SERVER` | Connection server type |
| `CONNECT_TIMEOUT` | Connection timeout |

---

## SID vs. Service Name

### SID (System Identifier)

A SID identifies an Oracle database instance.

An instance consists of memory structures and processes used to manage database operations.

Example:

```text
XE
```

### Service Name

A Service Name identifies a logical database service that clients can request.

A service may be associated with one or more instances.

**SID identifies an instance, while Service Name identifies a logical service.**

---

## Footprinting Oracle TNS

### 1. Service Discovery

```bash
nmap -sV -p1521 --open <IP>
```

- `-sV`: Detects service versions.
- `-p1521`: Scans the Oracle TNS port.
- `--open`: Displays open or possibly open ports.

Example result:

```text
1521/tcp open oracle-tns Oracle TNS listener 11.2.0.2.0
```

### 2. SID Enumeration

```bash
nmap -sV -p1521 --script oracle-sid-brute <IP>
```

The `oracle-sid-brute` NSE script tests candidate SIDs.

Example:

```text
oracle-sid-brute:
  XE
```

This indicates that the script identified `XE` as a valid SID.

---

## ODAT (Oracle Database Attacking Tool)

ODAT is a Python-based security testing tool for Oracle databases.

It supports multiple enumeration and security assessment modules.

### Check Installation

```bash
./odat.py -h
```

### Run Available Checks

```bash
./odat.py all -s <IP>
```

- `all`: Executes multiple ODAT modules.
- `-s`: Specifies the target server.

ODAT may identify:

- Database services and instances.
- Valid credentials.
- Security misconfigurations.
- Accessible database functionality.
- Potential vulnerabilities.

Some checks may be intrusive and should only be performed on authorized targets.

---

## Connecting with SQL*Plus

SQL*Plus is Oracle's command-line database client.

### Connection Syntax

```bash
sqlplus <username>@<IP>/<service_name>
```

The client can prompt us for the password.

### HTB Example

```bash
sqlplus scott@10.129.204.235/XE
```

The example credentials provided in the lesson are:

- Username: `scott`
- Password: `tiger`
- Connection identifier: `XE`

These credentials are specific to the lesson example.

---

## Oracle Database Enumeration

After successfully authenticating, we can execute SQL queries.

### 1. List Accessible Tables

```sql
SELECT table_name FROM all_tables;
```

The `ALL_TABLES` view provides information about tables accessible to the current user.

### 2. Enumerate User Roles

```sql
SELECT * FROM user_role_privs;
```

This query displays roles granted to the current user.

### 3. Check Current User

```sql
SELECT USER FROM dual;
```

This returns the current Oracle database username.

---

## Administrative Privileges

Oracle supports administrative privileges such as `SYSDBA`.

The lesson demonstrates a connection using:

```bash
sqlplus scott@<IP>/XE as sysdba
```

However, requesting `SYSDBA` does not automatically grant administrative access.

The account must already possess the required privileges.

Administrative access may expose highly sensitive database information.

---

## Security Considerations

Potential Oracle TNS security issues include:

- Exposed TNS Listeners.
- Weak or default credentials.
- Outdated Oracle Database versions.
- Excessive user privileges.
- Insecure Listener configurations.
- Unauthorized access to database information.
- Unsafe database file operations.

The lesson also demonstrates password hash retrieval and file upload operations after obtaining the necessary privileges.

These operations go beyond basic service enumeration.

---

## Enumeration Methodology

1. Discover the Oracle TNS Listener on TCP 1521.
2. Identify the Listener version.
3. Enumerate available SIDs.
4. Use ODAT for additional authorized security checks.
5. Connect using SQL*Plus and valid credentials.
6. Enumerate accessible tables.
7. Inspect user roles and privileges.
8. Analyze the database configuration and exposed information.

**Our objective is to identify Oracle database services, understand how clients connect to them, and enumerate accessible information.**