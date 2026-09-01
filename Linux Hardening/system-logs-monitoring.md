# System Logs

System logs are files that record information about:

```text
System activity
Application activity
User activity
Network activity
Security events
```

Logs are important for both:

```text
Troubleshooting
+
Security monitoring
```

For penetration testing, logs can reveal abnormal activity such as unauthorized logins, attempted attacks, clear-text credentials, or unusual file access. They can also show whether our own security testing generated alerts or warnings.

---

# Why Logs Matter

A useful mental model is:

```text
Something happens
      │
      ▼
System / Application
      │
      ▼
Log entry created
      │
      ▼
Log file
      │
      ▼
Analysis
```

For example:

```text
Someone attempts SSH login
        │
        ▼
sshd processes request
        │
        ▼
Authentication event logged
        │
        ▼
/var/log/auth.log
```

Logs therefore provide a historical record of what happened on the system.

---

# Security Perspective

During a security investigation, we may want to answer questions such as:

```text
Who logged in?

When did the login occur?

From which IP?

Was authentication successful?

Was sudo used?

Which command was executed?

Which files were accessed?

Were services started or stopped?

Did the firewall detect anything?

Did an application generate an error?
```

Logs can provide evidence for answering these questions.

---

# Proper Log Management

The material emphasizes several important aspects of log management:

```text
Appropriate log levels
Log rotation
Secure storage
Protection from unauthorized access
Regular review and analysis
```

Log rotation prevents log files from continuously growing until they consume excessive disk space.

Logs should also be protected because attackers who can modify them may attempt to hide their activity.

---

# Main Types of Linux Logs

The material introduces five categories:

```text
Kernel Logs
System Logs
Authentication Logs
Application Logs
Security Logs
```

A useful overview is:

| Type           | Main Purpose                              |
| -------------- | ----------------------------------------- |
| Kernel         | Kernel and hardware events                |
| System         | General system events                     |
| Authentication | Login and authentication activity         |
| Application    | Application-specific activity             |
| Security       | Security-tool and security-related events |

---

# Kernel Logs

Kernel logs contain information related to the Linux kernel.

The material identifies:

```text
/var/log/kern.log
```

as the relevant log file.

Kernel logs may contain information about:

```text
Hardware drivers
System calls
Kernel events
System crashes
Resource limitations
Suspicious kernel activity
```

---

# Kernel Log Mental Model

```text
Hardware / Kernel Event
        │
        ▼
Linux Kernel
        │
        ▼
Kernel Log
        │
        ▼
/var/log/kern.log
```

These logs can help identify problems such as vulnerable or outdated drivers, crashes, resource problems, or potentially suspicious kernel activity.

---

# System Logs

The material identifies:

```text
/var/log/syslog
```

as a general system log.

It may contain information about:

```text
Service starts
Service stops
Login attempts
System reboots
Cron activity
Kernel messages
Other system-level events
```

---

# Syslog Example

The material provides:

```bash
Feb 28 2023 15:00:01 server CRON[2715]: (root) CMD (/usr/local/bin/backup.sh)
Feb 28 2023 15:04:22 server sshd[3010]: Failed password for htb-student from 10.14.15.2 port 50223 ssh2
Feb 28 2023 15:05:02 server kernel: [  138.303596] ata3.00: exception Emask 0x0 SAct 0x0 SErr 0x0 action 0x6 frozen
Feb 28 2023 15:06:43 server apache2[2904]: 127.0.0.1 - - [28/Feb/2023:15:06:43 +0000] "GET /index.html HTTP/1.1" 200 13484 "-" "Mozilla/5.0"
Feb 28 2023 15:07:19 server sshd[3010]: Accepted password for htb-student from 10.14.15.2 port 50223 ssh2
Feb 28 2023 15:09:54 server kernel: [  367.543975] EXT4-fs (sda1): re-mounted. Opts: errors=remount-ro
Feb 28 2023 15:12:07 server systemd[1]: Started Clean PHP session files.
```

Notice how several different components appear in the same example:

```text
CRON
sshd
kernel
apache2
systemd
```

---

# Reading a Log Entry

Consider:

```bash
Feb 28 2023 15:04:22 server sshd[3010]: Failed password for htb-student from 10.14.15.2 port 50223 ssh2
```

We can extract:

```text
Date
→ Feb 28 2023

Time
→ 15:04:22

Host
→ server

Service
→ sshd

PID
→ 3010

Event
→ Failed password

User
→ htb-student

Source IP
→ 10.14.15.2

Source port
→ 50223
```

This is why logs are so useful during investigations.

---

# Authentication Logs

Authentication logs focus specifically on authentication activity.

The material identifies:

```text
/var/log/auth.log
```

as the authentication log location used in the example system.

It records:

```text
Successful authentication
Failed authentication
SSH activity
sudo activity
User sessions
Other authentication-related events
```

The material notes that `syslog` may contain similar information, but `auth.log` specifically focuses on authentication attempts.

---

# Auth.log Example

The material provides:

```bash
Feb 28 2023 18:15:01 sshd[5678]: Accepted publickey for admin from 10.14.15.2 port 43210 ssh2: RSA SHA256:+KjEzN2cVhIW/5uJpVX9n5OB5zVJ92FtCZxVzzcKjw
Feb 28 2023 18:15:03 sudo:   admin : TTY=pts/1 ; PWD=/home/admin ; USER=root ; COMMAND=/bin/bash
Feb 28 2023 18:15:05 sudo:   admin : TTY=pts/1 ; PWD=/home/admin ; USER=root ; COMMAND=/usr/bin/apt-get install netcat-traditional
Feb 28 2023 18:15:08 sshd[5678]: Disconnected from 10.14.15.2 port 43210 [preauth]
Feb 28 2023 18:15:12 kernel: [  778.941871] firewall: unexpected traffic allowed on port 22
Feb 28 2023 18:15:15 auditd[9876]: Audit daemon started successfully
Feb 28 2023 18:15:18 systemd-logind[1234]: New session 4321 of user admin.
Feb 28 2023 18:15:21 CRON[2345]: pam_unix(cron:session): session opened for user root by (uid=0)
Feb 28 2023 18:15:24 CRON[2345]: pam_unix(cron:session): session closed for user root
```

---

# Reading Authentication Activity

Consider:

```bash
Accepted publickey for admin from 10.14.15.2 port 43210
```

This tells us:

```text
Authentication
→ successful

Method
→ public key

User
→ admin

Source
→ 10.14.15.2

Source port
→ 43210
```

Then:

```bash
sudo: admin : TTY=pts/1 ; PWD=/home/admin ; USER=root ; COMMAND=/bin/bash
```

shows that:

```text
admin
   │
   │ sudo
   ▼
USER=root
   │
   ▼
COMMAND=/bin/bash
```

So we can reconstruct actions performed after authentication.

---

# Authentication Timeline

Logs become especially useful when events are combined chronologically.

For example:

```text
18:15:01
admin authenticates successfully
        │
        ▼
18:15:03
admin uses sudo to run /bin/bash
        │
        ▼
18:15:05
admin installs netcat-traditional
        │
        ▼
18:15:18
new admin session appears
```

Instead of looking at isolated events, we can reconstruct a sequence of activity.

---

# Application Logs

Applications often maintain their own logs.

The material provides examples such as:

```text
Apache
→ /var/log/apache2/error.log

MySQL
→ /var/log/mysql/error.log
```

Application logs can help us understand how specific applications process requests and data.

They may reveal:

```text
Errors
Requests
Misconfigurations
Suspicious activity
Potential vulnerabilities
```

---

# Web Server Logs

For a web server, logs can show requests made by clients.

Conceptually:

```text
Client
   │
   │ GET /index.html
   ▼
Apache
   │
   ├── processes request
   │
   ▼
Access Log
```

This can reveal information such as:

```text
Client IP
Requested resource
Timestamp
HTTP method
HTTP status
User-Agent
```

---

# Access Logs

The material describes access logs as records of user and process activity.

Examples include:

```text
Login attempts
File access
Network connections
```

An example entry provided is:

```bash
2023-03-07T10:15:23+00:00 servername privileged.sh: htb-student accessed /root/hidden/api-keys.txt
```

---

# Reading the Access Log

From:

```bash
2023-03-07T10:15:23+00:00 servername privileged.sh: htb-student accessed /root/hidden/api-keys.txt
```

we can identify:

```text
Time
→ 2023-03-07T10:15:23+00:00

Host
→ servername

Program
→ privileged.sh

User
→ htb-student

Action
→ accessed

Resource
→ /root/hidden/api-keys.txt
```

This is extremely valuable during security investigations.

---

# Audit Logs

Audit logs focus on:

```text
security-relevant events
```

The material gives examples such as:

```text
Changes to system configuration files
Attempts to modify system files
Changes to system settings
```

Conceptually:

```text
User / Process
      │
      ▼
Security-sensitive action
      │
      ▼
Audit Log
      │
      ▼
Investigation
```

Access and audit logs can therefore help identify attacks, suspicious behavior, or security breaches.

---

# Common Log Locations

The material provides several common locations:

| Service                | Log Location                                      |
| ---------------------- | ------------------------------------------------- |
| Apache                 | `/var/log/apache2/access.log`                     |
| Nginx                  | `/var/log/nginx/access.log`                       |
| OpenSSH on Ubuntu      | `/var/log/auth.log`                               |
| OpenSSH on CentOS/RHEL | `/var/log/secure`                                 |
| MySQL                  | `/var/log/mysql/mysql.log`                        |
| PostgreSQL             | `/var/log/postgresql/postgresql-version-main.log` |
| Systemd                | `/var/log/journal/`                               |

The exact location may vary depending on the distribution and configuration.

---

# Security Logs

Security applications may create their own logs.

The material provides two examples:

```text
Fail2ban
→ /var/log/fail2ban.log

UFW
→ /var/log/ufw.log
```

Other security events may also appear in:

```text
/var/log/syslog
/var/log/auth.log
```

---

# Fail2ban Logs

Recall from the Linux Security section:

```text
Fail2ban
→ monitors repeated failed login attempts
```

Its log can therefore provide information about actions taken by Fail2ban.

The material identifies:

```text
/var/log/fail2ban.log
```

as its log file.

---

# UFW Logs

Recall:

```text
UFW
→ firewall management
```

The material identifies:

```text
/var/log/ufw.log
```

as a location where UFW firewall activity may be recorded.

This connects the Firewall section with log analysis:

```text
Network packet
      │
      ▼
Firewall
      │
      ▼
Security event
      │
      ▼
Firewall log
```

---

# Logs from a Penetration Testing Perspective

As penetration testers, logs can help us understand both:

```text
What happened on the target
```

and:

```text
What evidence our activity generated
```

For example:

```text
SSH testing
    │
    ▼
auth.log

Web requests
    │
    ▼
Apache / Nginx logs

Firewall interaction
    │
    ▼
UFW logs

System changes
    │
    ▼
System / audit logs
```

The material specifically notes that reviewing logs after security testing can reveal whether our activities triggered security events, IDS alerts, or system warnings.

---

# Analyzing Logs

The material mentions command-line tools such as:

```text
tail
grep
sed
```

for accessing and analyzing logs.

These connect directly with the previous **Filter Contents** section.

---

# `tail`

`tail` is useful when we want to inspect the most recent log entries.

Conceptually:

```bash
tail /var/log/auth.log
```

means:

```text
Show the end of auth.log
```

Since new log entries are generally appended, the newest entries are commonly found near the end.

---

# `grep`

`grep` is useful for searching for specific patterns.

Conceptually:

```bash
grep "Failed password" /var/log/auth.log
```

means:

```text
Search auth.log
       │
       ▼
Only lines containing
"Failed password"
```

This is useful when searching large logs for specific events.

---

# Combining Tools

The Linux concepts from previous sections can be combined.

For example:

```text
Log File
   │
   ▼
grep
   │
   ▼
Relevant Entries
```

or:

```text
Log File
   │
   ▼
tail
   │
   ▼
Recent Entries
```

This is why commands such as `grep`, `tail`, and pipes become increasingly useful as we progress through Linux.

---

# Log Analysis Mental Model

Instead of reading thousands of lines manually:

```text
LOG FILE
   │
   │ filter
   ▼
Interesting Event
   │
   │ correlate
   ▼
Other Events
   │
   ▼
TIMELINE
   │
   ▼
Understand what happened
```

This is the core idea behind basic log analysis.

---

# Example Investigation

Suppose we find:

```text
Failed SSH login
        │
        ▼
Successful SSH login
        │
        ▼
sudo command
        │
        ▼
Sensitive file access
```

Individually, these are separate log events.

Together, they may form:

```text
Potential security incident
```

This is why timestamps, users, source IPs, commands, and accessed resources are important.

---

# Important Paths to Remember

```text
/var/log/kern.log
→ Kernel events
```

```text
/var/log/syslog
→ General system events
```

```text
/var/log/auth.log
→ Authentication activity
```

```text
/var/log/apache2/access.log
→ Apache requests
```

```text
/var/log/nginx/access.log
→ Nginx requests
```

```text
/var/log/fail2ban.log
→ Fail2ban events
```

```text
/var/log/ufw.log
→ UFW firewall activity
```

```text
/var/log/journal/
→ Systemd journal data
```

---

# Quick Reference

| Log                           | Information             |
| ----------------------------- | ----------------------- |
| `/var/log/kern.log`           | Kernel events           |
| `/var/log/syslog`             | General system events   |
| `/var/log/auth.log`           | Authentication activity |
| `/var/log/apache2/access.log` | Apache access           |
| `/var/log/apache2/error.log`  | Apache errors           |
| `/var/log/nginx/access.log`   | Nginx access            |
| `/var/log/mysql/mysql.log`    | MySQL activity          |
| `/var/log/fail2ban.log`       | Fail2ban activity       |
| `/var/log/ufw.log`            | UFW activity            |
| `/var/log/journal/`           | Systemd journal         |

---

# What to Remember First

We do not need to memorize every possible Linux log location.

The most important three from this section are:

```text
/var/log/syslog
→ What is happening on the system?
```

```text
/var/log/auth.log
→ Who is authenticating and using privileges?
```

```text
Application logs
→ What is happening inside a specific application?
```

For security investigations, always look for:

```text
WHEN?
→ timestamp

WHO?
→ user

WHERE FROM?
→ source IP

WHAT?
→ action

RESULT?
→ success / failure
```

And remember:

```text
tail
→ recent entries

grep
→ find interesting entries

sed
→ process/transform text
```

---

## Key Takeaway

**Linux logs provide a historical record of system, authentication, application, and security activity. As penetration testers, we can use them to identify abnormal behavior, reconstruct events, investigate authentication and privilege use, understand application activity, and determine whether our own testing generated security alerts. The most important skill is not memorizing every log path, but knowing what type of evidence we need and where that evidence is likely to be recorded.**
