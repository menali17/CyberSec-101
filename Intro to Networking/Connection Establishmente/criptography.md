# Cryptography

Cryptography is used to protect data against unauthorized access and manipulation.

Encryption transforms readable data (**plaintext**) into an unreadable form (**ciphertext**) using cryptographic algorithms and keys.

Two main encryption approaches are:

* **Symmetric encryption**
* **Asymmetric encryption**

---

# Symmetric Encryption

**Symmetric encryption** uses the **same key** to encrypt and decrypt data.

`Plaintext + Secret Key → Ciphertext`

`Ciphertext + Same Secret Key → Plaintext`

Both the sender and receiver must possess the same secret key.

Main characteristics:

* Uses one shared secret key.
* Fast and efficient.
* Suitable for encrypting large amounts of data.
* Key distribution is a major security challenge.
* If the key is compromised, encrypted data may also be compromised.

Common symmetric algorithms include:

* **AES**
* **DES**
* **3DES**

---

# Asymmetric Encryption

**Asymmetric encryption**, also called **public-key encryption**, uses two different keys:

* **Public Key**
* **Private Key**

The public key can be shared with anyone, while the private key must remain secret.

Basic concept:

`Public Key → Encrypt`

`Private Key → Decrypt`

This allows anyone to encrypt data for a recipient, but only the holder of the corresponding private key can decrypt it.

Examples include:

* **RSA**
* **PGP**
* **ECC**

Common uses:

* Digital signatures
* SSL/TLS
* VPNs
* SSH
* PKI
* Cloud services

---

# Symmetric vs Asymmetric Encryption

| Symmetric                           | Asymmetric                                 |
| ----------------------------------- | ------------------------------------------ |
| One shared key                      | Public + private key                       |
| Faster                              | Slower                                     |
| Efficient for large amounts of data | Useful for authentication and key exchange |
| Key distribution can be difficult   | Public key can be openly distributed       |
| AES, DES, 3DES                      | RSA, ECC                                   |

A common approach is to combine both:

`Asymmetric cryptography → Establish/protect keys`

`Symmetric cryptography → Encrypt actual data`

---

# Public-Key Encryption

Public-key cryptography helps solve one of the major problems of symmetric encryption: **secure key distribution**.

Because the public key does not need to remain secret, it can be distributed openly.

The corresponding **private key** remains known only to its owner.

Public-key cryptography also enables:

* Authentication
* Digital signatures
* Secure key exchange
* Confidential communication

Its security relies on mathematical problems that are computationally difficult to solve.

---

# Data Encryption Standard (DES)

**DES (Data Encryption Standard)** is a symmetric block cipher.

It uses the **same key for encryption and decryption**.

DES processes data in:

`64-bit blocks`

Its key is technically 64 bits long, but:

* 8 bits are used for parity/check purposes.
* The effective cryptographic key length is **56 bits**.

Therefore:

`DES Key Strength = 56 bits`

Because this key size is too small by modern standards, DES is considered insecure.

---

# Triple DES (3DES)

**3DES (Triple DES)** was developed to improve the security of DES.

It applies DES operations multiple times.

A typical process is:

`Encrypt → Decrypt → Encrypt`

using multiple keys.

This is commonly known as:

`EDE — Encrypt, Decrypt, Encrypt`

3DES provides better security than DES, but it is slower and has also been replaced by more modern algorithms such as AES.

---

# Advanced Encryption Standard (AES)

**AES (Advanced Encryption Standard)** is a symmetric block cipher and the modern successor to DES.

AES supports the following key sizes:

* **AES-128**
* **AES-192**
* **AES-256**

AES provides stronger security and better performance than DES.

Common uses include:

* WLAN / IEEE 802.11i
* IPsec
* SSH
* VoIP
* PGP
* OpenSSL

AES is widely used for encrypting large amounts of data efficiently.

---

# DES vs AES

|                    | DES             | AES                       |
| ------------------ | --------------- | ------------------------- |
| Type               | Symmetric       | Symmetric                 |
| Effective Key Size | 56 bits         | 128 / 192 / 256 bits      |
| Security           | Weak / obsolete | Strong                    |
| Performance        | Slower          | Faster and more efficient |
| Current Use        | Legacy systems  | Widely used               |

---

# Block Ciphers

A **block cipher** encrypts data in fixed-size blocks.

For example:

`Plaintext → Block → Encryption → Ciphertext Block`

Algorithms such as AES encrypt blocks of data rather than processing an entire arbitrary-length message directly.

To encrypt larger messages securely, a **cipher mode of operation** is used.

---

# Cipher Modes

A **cipher mode** defines how individual blocks of plaintext are processed and combined when using a block cipher.

Common modes include:

* ECB
* CBC
* CFB
* OFB
* CTR
* GCM

---

## ECB — Electronic Codebook

**ECB (Electronic Codebook)** encrypts each block independently.

Conceptually:

```text
Block 1 → Encrypt → Cipher Block 1
Block 2 → Encrypt → Cipher Block 2
Block 3 → Encrypt → Cipher Block 3
```

The main problem is that identical plaintext blocks produce identical ciphertext blocks.

This means patterns in the original data can remain visible.

Therefore:

**ECB is generally not recommended.**

Main weakness:

`Same Plaintext Block → Same Ciphertext Block`

This can allow statistical or pattern analysis.

---

# CBC — Cipher Block Chaining

**CBC (Cipher Block Chaining)** links encrypted blocks together.

Each plaintext block depends on the previous ciphertext block.

Conceptually:

`Previous Ciphertext + Current Plaintext → Encryption`

This chaining helps hide patterns that would otherwise appear with ECB.

The first block requires an:

**IV — Initialization Vector**

CBC has been used in applications such as:

* Disk encryption
* Email encryption
* TLS / SSL
* TrueCrypt
* VeraCrypt

---

# CFB — Cipher Feedback

**CFB (Cipher Feedback)** allows a block cipher to operate similarly to a stream cipher.

It is useful for encrypting data streams.

Common use cases include:

* Network communication
* File encryption during transmission
* PKCS
* BitLocker

CFB can process data without waiting for an entire large message to be available.

---

# OFB — Output Feedback

**OFB (Output Feedback)** also converts a block cipher into a stream-like encryption system.

It generates a **keystream** that is combined with the plaintext.

It is suitable for real-time communication and continuous data streams.

Examples mentioned include:

* PKCS
* SSH

---

# CTR — Counter Mode

**CTR (Counter Mode)** transforms a block cipher into a stream cipher by encrypting sequential counter values.

Conceptually:

```text
Counter 1 → Encrypt → Keystream Block 1
Counter 2 → Encrypt → Keystream Block 2
Counter 3 → Encrypt → Keystream Block 3
```

The generated keystream is combined with the plaintext.

CTR is useful for:

* Real-time data
* Network communication
* Disk encryption
* IPsec
* BitLocker

It can also be efficiently parallelized.

---

# GCM — Galois/Counter Mode

**GCM (Galois/Counter Mode)** combines encryption with authentication.

It provides:

* **Confidentiality**
* **Integrity**

This makes it especially useful for secure network communication.

Common uses include:

* Wireless communication
* VPNs
* Secure communication protocols

GCM is based on counter mode but also includes an authentication mechanism to detect data modification.

---

# Cipher Mode Comparison

| Mode    | Main Characteristic           | Main Use / Issue                  |
| ------- | ----------------------------- | --------------------------------- |
| **ECB** | Encrypts blocks independently | Reveals patterns; not recommended |
| **CBC** | Chains blocks together        | Hides patterns; requires IV       |
| **CFB** | Stream-like encryption        | Network/file streams              |
| **OFB** | Generates a keystream         | Real-time streams                 |
| **CTR** | Uses counter values           | Fast and parallelizable           |
| **GCM** | Encryption + authentication   | Confidentiality + integrity       |

---

# Quick Reference

**Cryptography**
→ Protects data through mathematical algorithms.

**Plaintext**
→ Original readable data.

**Ciphertext**
→ Encrypted unreadable data.

**Symmetric Encryption**
→ Same key encrypts and decrypts.

**Asymmetric Encryption**
→ Public key + private key.

**DES**
→ Symmetric cipher with a 56-bit effective key; obsolete.

**3DES**
→ Applies DES multiple times; stronger than DES but outdated.

**AES**
→ Modern symmetric cipher using 128-, 192-, or 256-bit keys.

**Block Cipher**
→ Encrypts fixed-size blocks of data.

**ECB**
→ Independent blocks; leaks patterns.

**CBC**
→ Blocks are chained together.

**CFB**
→ Stream-like encryption using cipher feedback.

**OFB**
→ Generates a keystream for continuous encryption.

**CTR**
→ Uses encrypted counters to generate a keystream.

**GCM**
→ Provides encryption and integrity/authentication.

---

## Key Takeaway

**Symmetric encryption is fast and efficient for protecting large amounts of data, while asymmetric encryption uses public/private key pairs to solve key distribution and enable authentication. AES is the modern standard for symmetric encryption, and cipher modes such as CBC, CTR, and GCM define how block ciphers process larger amounts of data.**
