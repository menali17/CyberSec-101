# MSSQL (Microsoft SQL Server)

---

## Overview

Microsoft SQL Server (MSSQL) is a relational database management system developed by Microsoft.

It uses T-SQL (Transact-SQL), Microsoft's extension of SQL, to manage relational databases.

MSSQL is commonly found in Windows environments and integrates with Microsoft technologies such as .NET and Active Directory.

### Default Port

- **TCP 1433:** Default MSSQL database connection port.

### MSSQL vs. MySQL

| Feature | MySQL | MSSQL |
|---|---|---|
| Vendor | Oracle | Microsoft |
| Default Port | 3306 | 1433 |
| SQL Dialect | MySQL SQL dialect | T-SQL |
| Common Environment | Linux / Web Applications | Windows / Enterprise Applications |
| Common CLI Client | mysql | Impacket mssqlclient |

---

## MSSQL Clients

We can interact with MSSQL servers using different clients.

### Common Clients

- SQL Server Management Studio (SSMS)
- mssql-cli
- SQL Server PowerShell
- HeidiSQL
- Impacket's mssqlclient.py

**SSMS** is a graphical client used to administer SQL Server instances.

It can be installed on systems other than the database server.

### Impacket

Impacket provides a command-line MSSQL client commonly available in penetration testing environments.

We can locate the tool with:

```bash
locate mssqlclient
```

---

## Default System Databases

| Database | Description |
|---|---|
| `master` | Stores essential SQL Server instance information |
| `model` | Template for creating new databases |
| `msdb` | Stores SQL Server Agent jobs and alerts |
| `tempdb` | Stores temporary objects and data |
| `resource` | Read-only database containing internal system objects |

---

## Authentication

MSSQL supports different authentication methods.

### Windows Authentication

Uses Windows identities, which may be authenticated through local Windows accounts or Active Directory.

### SQL Server Authentication

Uses accounts managed by SQL Server itself.

The `sa` account is a built-in, highly privileged SQL Server login.

Weak credentials or excessive privileges can introduce serious security risks.

---

## Footprinting MSSQL

### 1. Service Discovery

```bash
nmap -sV -sC -p1433 <IP>
```

- `-sV`: Detects the service version.
- `-sC`: Executes default NSE scripts.
- `-p1433`: Scans the MSSQL port.

### 2. MSSQL NSE Scripts

```bash
nmap -sV -p1433 --script ms-sql-info,ms-sql-ntlm-info <IP>
```

These scripts may reveal:

- SQL Server version.
- Windows hostname.
- SQL Server instance name.
- Windows domain information.
- Authentication-related metadata.

Other MSSQL NSE scripts can perform more intrusive checks and may require credentials.

### Important Findings

Example:

```text
1433/tcp open ms-sql-s Microsoft SQL Server 2019
Windows server name: SQL-01
Instance name: MSSQLSERVER
Version: 15.00.2000.00
```

`MSSQLSERVER` is the conventional name of the default SQL Server instance.

---

## Named Pipes

Named pipes are an interprocess communication mechanism supported by SQL Server.

A named pipe can provide another way for clients to communicate with the database instance.

Example:

```text
\\10.129.201.248\pipe\sql\query
```

Identifying named pipe information can help us understand the available connection methods.

---

## MSSQL Enumeration with Metasploit

Metasploit provides the `mssql_ping` auxiliary scanner.

```text
msfconsole
use auxiliary/scanner/mssql/mssql_ping
set RHOSTS <IP>
run
```

The scanner may reveal:

- Server name.
- Instance name.
- SQL Server version.
- TCP port.
- Named pipe information.

---

## Connecting with Impacket

### Windows Authentication

```bash
impacket-mssqlclient Administrator@<IP> -windows-auth
```

- `Administrator`: Windows username.
- `<IP>`: Target SQL Server.
- `-windows-auth`: Uses Windows authentication.

The client will prompt us for the password.

For SQL Server authentication, we can omit `-windows-auth` and use an appropriate SQL login.

---

## Database Enumeration

After authentication, we can interact with the database using T-SQL.

### List Databases

```sql
SELECT name FROM sys.databases;
```

The `sys.databases` catalog view contains information about databases in the SQL Server instance.

Example results:

```text
master
tempdb
model
msdb
```

Access to database information depends on our permissions.

---

## Dangerous Configurations

Potential MSSQL security concerns include:

- Unencrypted connections.
- Improperly validated self-signed certificates.
- Insecure named pipe configurations.
- Weak or default credentials.
- Highly privileged database accounts.

These conditions should be investigated and validated rather than automatically treated as vulnerabilities.

---

## Enumeration Methodology

1. Discover MSSQL using Nmap.
2. Identify the SQL Server version and instance.
3. Enumerate available service information.
4. Investigate authentication methods.
5. Connect with valid credentials using Impacket.
6. Enumerate accessible databases.
7. Analyze the information returned.

**Our objective is to identify MSSQL services, understand their configuration, and enumerate accessible databases during footprinting.**