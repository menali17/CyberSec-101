# Windows Sessions

Windows sessions can be divided into two main types:

* **Interactive sessions**
* **Non-interactive sessions**

The distinction is mainly based on whether a user actively authenticates and interacts with the system.

---

## Interactive Sessions

An **interactive logon session** is created when a user authenticates to a local or domain system using their credentials.

Examples include:

* Logging directly into a Windows machine.
* Starting another logon session with `runas`.
* Connecting through Remote Desktop Protocol (RDP).

For example, a secondary logon session can be started from the command line using:

```cmd id="39e4ol"
runas
```

The important idea is:

```text id="b2bav4"
User provides credentials
        ↓
Authentication
        ↓
Interactive session
```

In this type of session, a user is actively working with the Windows system.

---

## Non-Interactive Sessions

**Non-interactive accounts** are primarily used by Windows to run services, applications, and scheduled tasks automatically.

They do not require a normal user to log in or manually enter credentials.

These accounts generally:

* Have no password associated with them.
* Can start automatically during system boot.
* Are commonly used by Windows services.
* Can be used for scheduled tasks.

There are three main built-in non-interactive accounts:

* `LocalSystem`
* `LocalService`
* `NetworkService`

---

## Local System Account

The **Local System Account** is represented as:

```text id="f5rmhe"
NT AUTHORITY\SYSTEM
```

It is the most powerful built-in account on a Windows system.

It is used for operating-system-level tasks such as running important Windows services.

A key point is that `SYSTEM` has even more privileges than a normal account in the local Administrators group.

---

## Local Service Account

The **Local Service Account** is represented as:

```text id="f9bimk"
NT AUTHORITY\LocalService
```

It is a less privileged account than `SYSTEM`.

Its privileges are similar to those of a normal local user account, and it is commonly used for services that do not require extensive permissions.

---

## Network Service Account

The **Network Service Account** is represented as:

```text id="w8buj4"
NT AUTHORITY\NetworkService
```

Locally, it has privileges similar to `LocalService`.

Its main distinction is that it can establish authenticated network sessions for certain Windows services.

---

## Built-In Non-Interactive Accounts

| Account         | Identity                      | General Privilege Level | Main Use                                                |
| --------------- | ----------------------------- | ----------------------: | ------------------------------------------------------- |
| Local System    | `NT AUTHORITY\SYSTEM`         |               Very high | OS tasks and privileged services                        |
| Local Service   | `NT AUTHORITY\LocalService`   |                     Low | Services requiring limited local access                 |
| Network Service | `NT AUTHORITY\NetworkService` |             Low locally | Services that also require authenticated network access |

---

## Interactive vs. Non-Interactive

| Interactive                             | Non-Interactive                                           |
| --------------------------------------- | --------------------------------------------------------- |
| User actively authenticates             | No normal user login required                             |
| Credentials are entered                 | No password normally associated with the built-in account |
| User directly interacts with the system | Used mainly by Windows and services                       |
| Examples: local login, `runas`, RDP     | Examples: `SYSTEM`, `LocalService`, `NetworkService`      |

---

## Security Perspective

The account associated with a Windows session or service is important because it determines what actions can be performed.

For example:

```text id="wlj4hm"
Service running as LocalService
        ↓
Limited privileges

Service running as SYSTEM
        ↓
Very high privileges
```

This is why seeing a process or service running as:

```text id="0zw6zz"
NT AUTHORITY\SYSTEM
```

is significant during Windows security analysis.

It means that process is executing with one of the highest privilege levels available on the machine.

---

# Quick Reference

### Interactive Session

Created when a user actively authenticates through:

```text id="83jqlv"
Local login
runas
RDP
```

### Non-Interactive Accounts

```text id="8we16r"
NT AUTHORITY\SYSTEM
NT AUTHORITY\LocalService
NT AUTHORITY\NetworkService
```

### Privilege Comparison

```text id="cwa4w2"
SYSTEM
  ↓
Highest privilege

LocalService / NetworkService
  ↓
Much more restricted
```

---

## Key Takeaway

**Interactive sessions are created when a user actively authenticates to Windows, such as through a local login, `runas`, or RDP. Non-interactive accounts are used by Windows to run services and scheduled tasks without requiring a normal user login. The three main built-in accounts are `LocalSystem`, `LocalService`, and `NetworkService`, with `NT AUTHORITY\SYSTEM` being the most powerful account on the system.**
