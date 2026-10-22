
# Databases — Metasploit Framework

## Overview

Metasploit supports PostgreSQL databases to organize and store penetration testing results.

The database can store:

- Hosts
- Services
- Vulnerabilities
- Credentials
- Loot
- Notes

## Database Setup

Check PostgreSQL status:

```bash
sudo service postgresql status
```

Start PostgreSQL:

```bash
sudo systemctl start postgresql
```

Initialize the Metasploit database:

```bash
sudo msfdb init
```

Check the connection inside MSFconsole:

```text
db_status
```

## Workspaces

Workspaces separate assessment data into different projects.

```text
workspace
workspace -a HTB_Lab
workspace HTB_Lab
```

The `*` indicates the active workspace.

## Nmap Integration

### Import an Existing Scan

Save Nmap results in XML format:

```bash
nmap -sV -oX scan.xml <target_ip>
```

Import the file:

```text
db_import scan.xml
```

### Run Nmap Through Metasploit

```text
db_nmap -sV <target_ip>
```

This executes Nmap and automatically stores the results in the active workspace.

## Querying Stored Data

- `hosts` — List discovered hosts.
- `services` — List discovered services.
- `vulns` — List recorded vulnerabilities.
- `creds` — List stored credentials.
- `loot` — List stored artifacts.
- `notes` — List recorded notes.

## Exporting Data

```text
db_export -f xml backup.xml
```

Exports the current workspace to an XML file.

## Key Takeaways

- Metasploit uses PostgreSQL to store assessment results.
- Workspaces organize results by assessment.
- `db_import` imports existing scan results.
- `db_nmap` runs Nmap and stores its results.
- `hosts`, `services`, and `vulns` query existing database records.
- Databases simplify information management across multiple targets.
