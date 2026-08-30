# Key Exchange Methods

Key exchange methods allow two parties to securely establish **cryptographic keys** over an insecure communication channel.

The main goal is to create a **shared secret key** that can later be used to encrypt communication without directly transmitting the secret itself.

## Diffie-Hellman (DH)

**Diffie-Hellman (DH)** allows two parties to establish a shared secret without previously sharing private information.

* Used for secure key establishment.
* Both parties exchange public values while keeping their private values secret.
* Each party independently calculates the same **shared secret**.
* Commonly used as part of protocols such as **TLS**.
* DH by itself does **not authenticate** the communicating parties.
* Without authentication, DH is vulnerable to **Man-in-the-Middle (MITM)** attacks.
* Traditional finite-field DH is generally slower than ECDH at comparable security levels.

### MITM Risk

An attacker can position themselves between both parties:

`Client ↔ Attacker ↔ Server`

The attacker can establish one secret with the client and another with the server, allowing them to intercept or modify communications.

DH should therefore be combined with authentication mechanisms such as **digital certificates or digital signatures**.

---

## RSA

**RSA (Rivest–Shamir–Adleman)** is an asymmetric cryptographic algorithm based on the difficulty of factoring large numbers.

RSA uses two keys:

* **Public Key:** can be distributed publicly.
* **Private Key:** must remain secret.

Common uses include:

* Asymmetric encryption.
* Digital signatures.
* Authentication.
* Protection of sensitive information.
* Historically, key exchange in older versions of **SSL/TLS**.
* Authentication mechanisms such as **PKINIT in Kerberos**.

Modern protocols generally prefer **(EC)DHE** for key establishment, while RSA is still widely used for signatures and authentication.

---

## Elliptic Curve Diffie-Hellman (ECDH)

**ECDH** is a variant of Diffie-Hellman that uses **Elliptic Curve Cryptography (ECC)**.

It provides the same general purpose as DH—establishing a shared secret—but with better efficiency at comparable security levels.

Main characteristics:

* Smaller keys than traditional DH for comparable security.
* Faster and more efficient.
* Used in modern protocols such as **TLS**.
* Can provide **Forward Secrecy** when ephemeral keys are used (`ECDHE`).
* Used in protocols such as **IKE** for VPN communication.

### Forward Secrecy

With ephemeral key exchange such as **ECDHE**, temporary keys are generated for individual sessions.

This means that compromising a long-term private key in the future does not automatically allow an attacker to decrypt previously captured sessions.

---

## ECDSA

**ECDSA (Elliptic Curve Digital Signature Algorithm)** uses ECC to create **digital signatures**.

Unlike ECDH, ECDSA is **not a key exchange algorithm**.

Its main purposes are:

* Authentication.
* Digital signatures.
* Integrity verification.

Important distinction:

`ECDH → Key establishment`

`ECDSA → Digital signatures / Authentication`

---

## Algorithm Comparison

| Algorithm | Purpose                 | Key Point                                                               |
| --------- | ----------------------- | ----------------------------------------------------------------------- |
| **DH**    | Key establishment       | Establishes a shared secret but requires authentication to prevent MITM |
| **RSA**   | Encryption / Signatures | Asymmetric algorithm; historically used for TLS key exchange            |
| **ECDH**  | Key establishment       | ECC-based DH with better efficiency                                     |
| **ECDSA** | Digital signatures      | ECC-based authentication and signatures                                 |

---

# Internet Key Exchange (IKE)

**Internet Key Exchange (IKE)** is a protocol used to establish and maintain secure communication sessions, especially for **IPsec VPNs**.

IKE is responsible for negotiating:

* Cryptographic algorithms.
* Security parameters.
* Authentication methods.
* Key material.
* Shared secrets.

It commonly combines technologies such as:

* **DH/ECDH** → key establishment.
* **RSA/ECDSA/PSK** → authentication.
* **AES** → symmetric encryption.

A simple way to remember the relationship:

`IKE → Negotiates keys and security parameters`

`IPsec → Uses them to protect network traffic`

---

## IKE Main Mode

**Main Mode** is an **IKEv1** negotiation mode.

It uses **6 messages (3 exchanges)** to establish the secure session.

Characteristics:

* More message exchanges.
* Provides identity protection.
* Generally considered more secure than Aggressive Mode.
* Slower than Aggressive Mode because more messages are exchanged.

---

## IKE Aggressive Mode

**Aggressive Mode** is another **IKEv1** negotiation mode designed to reduce the number of exchanges.

It uses **3 messages**.

Characteristics:

* Faster negotiation.
* Fewer messages.
* Does **not provide the same identity protection** as Main Mode.
* Can expose information useful for offline attacks against weak PSKs.

### Main Mode vs Aggressive Mode

|                     | Main Mode | Aggressive Mode |
| ------------------- | --------- | --------------- |
| Messages            | 6         | 3               |
| Speed               | Slower    | Faster          |
| Identity Protection | Yes       | No              |
| Security            | Higher    | Lower           |

> **Note:** Main Mode and Aggressive Mode are concepts associated with **IKEv1**, not IKEv2.

---

## Pre-Shared Key (PSK)

A **Pre-Shared Key (PSK)** is a secret value that both parties already know before establishing the connection.

Example:

`VPN Client → PSK ← VPN Server`

The PSK is used to **authenticate the communicating parties** during IKE negotiation.

### Advantages

* Simple to configure.
* Provides authentication between both parties.
* Common in VPN configurations.

### Limitations

* The key must be securely distributed beforehand.
* A compromised PSK can compromise authentication.
* Weak PSKs may be vulnerable to dictionary or brute-force attacks.
* Managing PSKs becomes difficult when many devices or users are involved.

---

## Quick Reference

**DH**
→ Establishes a shared secret.

**ECDH**
→ DH using elliptic curves; more efficient.

**RSA**
→ Asymmetric encryption and digital signatures.

**ECDSA**
→ ECC-based digital signatures.

**IKE**
→ Negotiates keys and security parameters for IPsec.

**PSK**
→ Pre-existing shared secret used for authentication.

**Main Mode**
→ IKEv1, 6 messages, identity protection.

**Aggressive Mode**
→ IKEv1, 3 messages, faster but less secure.

### Key Takeaway

**DH/ECDH establish shared secrets, RSA/ECDSA can provide authentication, and IKE coordinates the negotiation of keys and security parameters used to establish secure VPN/IPsec communication.**
