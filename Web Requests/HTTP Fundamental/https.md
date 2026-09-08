# Hypertext Transfer Protocol Secure (HTTPS)

**Hypertext Transfer Protocol Secure (HTTPS)** is the secure version of HTTP.

The main weakness of plain HTTP is that data is transferred in **clear text**. This means that someone positioned between the client and the server may be able to intercept and read the traffic through a **Man-in-the-Middle (MITM)** attack.

HTTPS addresses this problem by encrypting the communication between the client and the server.

---

# Why HTTPS Is Needed

With plain HTTP, sensitive information may be visible directly in network traffic.

For example, an HTTP login request could expose:

```text id="2d7tpr"
username=admin
password=password
```

If an attacker captures the request, those credentials may be readable and reused.

This is especially dangerous on untrusted networks such as public Wi-Fi.

With HTTPS, the traffic is encrypted, so intercepted packets do not directly reveal the application data.

---

# HTTP vs. HTTPS

| HTTP                               | HTTPS                      |
| ---------------------------------- | -------------------------- |
| Data is transferred in clear text  | Data is encrypted          |
| Default port `80`                  | Default port `443`         |
| URL begins with `http://`          | URL begins with `https://` |
| Easier to intercept sensitive data | Protects data in transit   |
| No encrypted TLS session           | Uses TLS for encryption    |

HTTPS has become the standard for modern websites because it protects information such as:

* Credentials
* Session data
* Personal information
* Application requests
* Other sensitive content

---

# Identifying HTTPS

A website using HTTPS has a URL beginning with:

```text id="rvq1z7"
https://
```

For example:

```text id="m97dsv"
https://www.google.com
```

Browsers also indicate that the connection is secure through connection information in the address bar.

The important point is that the traffic between the browser and the web server is encrypted.

---

# HTTPS Does Not Hide Everything

HTTPS encrypts the HTTP communication itself, but other network activity may still reveal information.

For example, if DNS resolution is performed through an unencrypted DNS server, an observer may still learn which domain is being resolved.

So HTTPS primarily protects the **contents of the HTTP communication**, not necessarily every piece of metadata involved in reaching the destination.

---

# HTTPS Flow

At a high level, HTTPS communication begins by establishing a secure encrypted connection before normal HTTP data is exchanged.

A common flow is:

1. The client attempts to access the website.
2. The server may redirect HTTP traffic to HTTPS.
3. The client and server begin a TLS handshake.
4. Cryptographic parameters and certificates are exchanged.
5. The secure session is established.
6. HTTP communication continues inside the encrypted connection.

---

## HTTP to HTTPS Redirect

If we visit:

```text id="e8y4wd"
http://inlanefreight.com
```

a server that enforces HTTPS may first receive the request over:

```text id="32a6nq"
TCP/80
```

The server can then redirect the client to:

```text id="iz2rwb"
TCP/443
```

using a response such as:

```text id="h5k9mt"
301 Moved Permanently
```

The browser then connects using HTTPS.

---

# TLS Handshake

Before encrypted HTTP communication can begin, the client and server perform a **TLS handshake**.

At a high level, the process includes:

### Client Hello

The client sends information about the connection capabilities it supports.

### Server Hello

The server responds with its selected parameters.

### Certificate and Key Exchange

The server presents its certificate and cryptographic information needed to establish the secure session.

The client validates the certificate and continues the key exchange process.

### Secure Session Established

Once the handshake succeeds, both sides can communicate through an encrypted channel.

After that, normal HTTP requests and responses continue inside the TLS-protected connection.

The exact cryptographic details are outside the scope of this section.

---

# Certificates

Certificates help the client verify the identity of the server.

If a website presents an invalid or untrusted certificate, the client cannot safely confirm that it is communicating with the intended server.

This protects against certain MITM attacks where an attacker tries to impersonate the legitimate website.

---

# HTTPS Downgrade Attacks

A possible attack against HTTPS is an **HTTP downgrade attack**.

The general idea is to force or manipulate communication so that the client uses insecure HTTP instead of HTTPS.

If successful, traffic may become readable in clear text.

Such attacks typically require an attacker to be positioned between the client and server, for example through a MITM proxy.

Modern browsers and servers include protections that make these attacks more difficult.

---

# cURL and HTTPS

cURL automatically handles HTTPS communication.

When we request an HTTPS URL, cURL performs the secure handshake and handles the encryption and decryption automatically.

Example:

```bash id="q5x4js"
curl https://inlanefreight.com
```

If the server presents an invalid certificate, cURL normally refuses to continue.

Example:

```bash id="n3rj7w"
menali@htb[/htb]$ curl https://inlanefreight.com

curl: (60) SSL certificate problem: Invalid certificate chain
More details here: https://curl.haxx.se/docs/sslcerts.html
...SNIP...
```

This behavior helps protect us from communicating with a server whose identity cannot be trusted.

---

# Ignoring Certificate Validation with `-k`

During penetration testing or lab environments, we may encounter:

* Self-signed certificates
* Expired certificates
* Invalid certificate chains
* Internal testing certificates

In these cases, cURL allows us to skip certificate verification using:

```text id="o9t2fd"
-k
```

Example:

```bash id="y7b4wp"
menali@htb[/htb]$ curl -k https://www.inlanefreight.com

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
...SNIP...
```

The request succeeds because cURL no longer rejects the certificate.

---

# Important Note About `-k`

The `-k` option disables certificate validation.

This means cURL still uses encrypted HTTPS communication, but it no longer verifies that the certificate belongs to a trusted server.

Therefore:

```text id="s2q6cx"
Encryption remains
Certificate trust verification is skipped
```

This is useful in controlled labs, but it reduces protection against impersonation and MITM attacks.

---

# HTTP and HTTPS Ports

| Protocol | Default Port |
| -------- | -----------: |
| HTTP     |         `80` |
| HTTPS    |        `443` |

---

# Quick Reference

### Normal HTTPS Request

```bash id="a4f8vk"
curl https://example.com
```

### Ignore Certificate Validation

```bash id="s1y3zr"
curl -k https://example.com
```

### HTTP

```text id="w8d6nr"
http://
TCP/80
Clear-text communication
```

### HTTPS

```text id="j2c7mq"
https://
TCP/443
Encrypted communication
```

### High-Level HTTPS Process

```text id="v5h0pc"
Client connects
→ TLS handshake
→ Certificate validation
→ Secure session established
→ Encrypted HTTP requests/responses
```

---

## Key Takeaway

**HTTPS protects HTTP communication by encrypting data between the client and server using TLS. This prevents intercepted traffic from directly exposing sensitive information such as credentials. HTTPS commonly uses port 443, requires a secure handshake before normal HTTP communication begins, and relies on certificates to help verify the server's identity. cURL handles HTTPS automatically and can use `-k` to bypass certificate validation in controlled environments.**
