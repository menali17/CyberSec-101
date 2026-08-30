# Authentication Protocols

Authentication protocols are used to **verify the identity of users, devices, and other entities** in a network.

They provide a standardized way to authenticate entities and help prevent unauthorized access. Some authentication protocols also provide mechanisms for securely exchanging information.

---

## Common Authentication Protocols

| Protocol     | Description                                                                                        |
| ------------ | -------------------------------------------------------------------------------------------------- |
| **Kerberos** | KDC-based authentication protocol that uses tickets, commonly used in domain environments          |
| **SRP**      | Password-based authentication protocol designed to protect against eavesdropping and MITM attacks  |
| **SSL**      | Cryptographic protocol for secure network communication                                            |
| **TLS**      | Successor to SSL; provides secure communication over networks                                      |
| **OAuth**    | Authorization standard that allows third-party access without sharing the user's password          |
| **OpenID**   | Decentralized authentication protocol that allows one identity to be used across multiple websites |
| **SAML**     | XML-based standard for exchanging authentication and authorization information                     |
| **2FA**      | Authentication using two different authentication factors                                          |
| **FIDO**     | Open standards focused on strong authentication                                                    |
| **PKI**      | System based on public/private keys, certificates, encryption, and digital signatures              |
| **SSO**      | Allows one set of credentials to access multiple applications                                      |
| **MFA**      | Authentication using multiple independent factors                                                  |
| **PAP**      | Simple authentication protocol that sends passwords in clear text                                  |
| **CHAP**     | Authentication protocol using a three-way handshake                                                |
| **EAP**      | Framework that supports multiple authentication methods                                            |
| **SSH**      | Secure protocol for remote access, command execution, and file transfer                            |
| **HTTPS**    | HTTP protected by SSL/TLS for encrypted web communication                                          |
| **LEAP**     | Cisco wireless authentication protocol based on EAP and RC4                                        |
| **PEAP**     | EAP-based authentication protocol that creates a TLS-protected tunnel                              |

---

# Authentication Factors

Authentication can be based on different types of factors:

**Something you know**

* Password
* PIN

**Something you have**

* Smartphone
* Security token
* Smart card

**Something you are**

* Fingerprint
* Face recognition
* Other biometric information

### 2FA vs MFA

**2FA (Two-Factor Authentication)** requires exactly two different authentication factors.

**MFA (Multi-Factor Authentication)** uses multiple authentication factors.

Example:

`Password + Smartphone Token`

This combines:

`Something you know + Something you have`

---

# Kerberos

**Kerberos** is a network authentication protocol commonly used in **domain environments**.

It relies on a **Key Distribution Center (KDC)** and uses **tickets** to authenticate users and services.

Key concepts:

* **KDC** → Key Distribution Center
* **Tickets** → Used to prove authentication
* Common in centralized/domain environments

Basic concept:

`User → KDC → Ticket → Service`

Instead of repeatedly sending credentials to different services, the user obtains tickets that can be used for authentication.

---

# OAuth

**OAuth** is an **authorization standard**, rather than a traditional authentication protocol.

It allows users to grant applications access to certain resources **without giving the application their password**.

Example:

`User → Authorizes Application → Application receives limited access`

The important distinction is:

**Authentication**
→ Who are you?

**Authorization**
→ What are you allowed to access?

---

# SAML

**SAML (Security Assertion Markup Language)** is an XML-based standard used to exchange **authentication and authorization information** between parties.

It is commonly associated with centralized authentication and **Single Sign-On (SSO)** environments.

---

# SSO

**Single Sign-On (SSO)** allows users to authenticate once and access multiple applications using the same authentication session or identity.

Conceptually:

`Login Once → App A + App B + App C`

This reduces the need to authenticate separately to every application.

---

# PKI

**Public Key Infrastructure (PKI)** provides mechanisms for securely exchanging information using:

* Public keys
* Private keys
* Digital certificates
* Encryption
* Digital signatures

PKI can help verify the identity of servers and other entities.

---

# PAP

**Password Authentication Protocol (PAP)** is a simple authentication protocol.

The major security problem is that it sends the user's password in **clear text** over the network.

`Username + Password → Network`

Because the credentials are not properly protected, PAP should not be used over insecure networks without additional protection.

---

# CHAP

**Challenge-Handshake Authentication Protocol (CHAP)** authenticates users using a **three-way handshake**.

Unlike PAP, the password itself does not need to be transmitted directly in clear text.

The basic process is:

`Challenge → Response → Verification`

---

# EAP

**Extensible Authentication Protocol (EAP)** is a **framework** that supports multiple authentication methods.

EAP itself does not define one specific authentication mechanism. Instead, different authentication methods can operate through it.

Examples include:

* EAP-TLS
* PEAP
* LEAP

This makes EAP particularly useful in wireless and enterprise authentication environments.

---

# LEAP

**LEAP (Lightweight Extensible Authentication Protocol)** is a wireless authentication protocol developed by Cisco.

Characteristics:

* Based on EAP.
* Provides mutual authentication between client and server.
* Uses **RC4** for encryption.
* Vulnerable to dictionary attacks.
* Considered obsolete/insecure compared with modern alternatives.

LEAP has largely been replaced by stronger protocols such as:

`EAP-TLS` and `PEAP`

---

# PEAP

**Protected Extensible Authentication Protocol (PEAP)** is an EAP-based protocol used in wired and wireless networks.

PEAP uses **TLS** to create a protected authentication tunnel.

Characteristics:

* Uses TLS to protect communication.
* Uses a server-side certificate to authenticate the server.
* Can support different methods for client authentication.
* Commonly used in enterprise networks.
* More secure than LEAP.

PEAP protects authentication information such as **MSCHAPv2 hashes**, while LEAP does not provide the same protection.

---

## LEAP vs PEAP

|                     | LEAP               | PEAP                    |
| ------------------- | ------------------ | ----------------------- |
| Based on            | EAP                | EAP                     |
| Encryption          | RC4                | TLS                     |
| Server Certificate  | No                 | Yes                     |
| MSCHAPv2 Protection | Weak / exposed     | Protected inside TLS    |
| Security            | Weaker             | Stronger                |
| Current Use         | Largely deprecated | Enterprise environments |

Although PEAP is more secure than LEAP, both have been increasingly replaced by stronger solutions such as **EAP-TLS**.

---

# SSH

**Secure Shell (SSH)** provides encrypted communication between a client and server.

Common uses:

* Remote command-line access
* Remote command execution
* Secure file transfer
* Authentication

SSH protects communication against eavesdropping and tampering.

---

# HTTPS

**HTTPS** is the secure version of HTTP.

It uses **SSL/TLS** to protect web communication.

HTTPS provides:

* Encryption
* Authentication
* Protection against interception
* Protection against modification of transmitted data

Digital certificates and **PKI** allow the client to verify the identity of the server, helping prevent **MITM attacks**.

---

# Quick Reference

**Kerberos**
→ KDC + tickets; common in domain environments.

**OAuth**
→ Authorization without sharing passwords.

**SAML**
→ XML-based exchange of authentication/authorization information.

**SSO**
→ One authentication for multiple applications.

**PKI**
→ Public/private keys + certificates + digital signatures.

**PAP**
→ Sends password in clear text; insecure.

**CHAP**
→ Challenge-response authentication using a three-way handshake.

**EAP**
→ Framework supporting multiple authentication methods.

**LEAP**
→ Cisco EAP protocol using RC4; vulnerable and largely deprecated.

**PEAP**
→ EAP inside a TLS-protected tunnel; more secure than LEAP.

**SSH**
→ Secure remote access and communication.

**HTTPS**
→ HTTP protected with SSL/TLS.

### Key Takeaway

**Authentication protocols verify the identity of users and devices. Different protocols are designed for different environments: Kerberos uses tickets in domain environments, EAP provides a framework for network authentication, PEAP protects authentication through TLS, and protocols such as SSH and HTTPS use encryption and certificates to secure communication.**
