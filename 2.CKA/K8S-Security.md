# K8S Security

## SSL/TLS Basics:

### **What is a Secure Sockets Layer (SSL)?**

Secure Sockets Layer (SSL) is a now deprecated cryptographic protocol that was designed to secure communication over the internet — primarily between web browsers and servers. It established a secure, encrypted connection to ensure data privacy and integrity.

#### **What SSL Does ?**

SSL encrypts:
- Web traffic (HTTPS)
- Emails (SMTP over SSL)
- VPN connections

SSL ensures:
* Confidentiality – data can't be read by third parties
* Integrity – data isn't tampered with in transit
* Authentication – server identity is verified via a certificate

#### Key Facts About SSL:

* Purpose:  Encrypt data in transit (e.g., login credentials, credit card info)
* Introduced: 1995 by Netscape
* Replaced by: TLS (Transport Layer Security)
* Status: SSL is obsolete and no longer secure

### What is Transport Layer Security (TLS)?

Transport Layer Security (TLS) is a cryptographic protocol that provides privacy, integrity, and authentication for data transmitted over a network — such as between a web browser and a web server (HTTPS), or within internal systems like Kubernetes components.

It is the modern, secure replacement for SSL (Secure Sockets Layer).

TLS Ensures:
- Confidentiality: Encrypts traffic (no eavesdropping)
- Integrity:       Detects tampering using HMAC or AEAD
- Authentication: Uses digital certificates (X.509) to verify identity

Understanding TLS vs SSL is important because these terms are often used interchangeably — but they’re not exactly the same.

### TLS vs SSL – Quick Comparison:

| Feature             | **SSL (Secure Sockets Layer)** | **TLS (Transport Layer Security)** |
| ------------------- | ------------------------------ | ---------------------------------- |
| 🔢 Latest Version   | SSL 3.0 (deprecated)           | TLS 1.3 (latest)                   |
| 📅 Release Timeline | 1995–1996 (SSL v2/v3)          | 1999–2018 (TLS 1.0 to 1.3)         |
| 🔒 Security         | Obsolete and vulnerable        | Actively maintained, secure        |
| ⚙️ Performance      | Slower and less efficient      | Faster handshake, better security  |
| 🔁 Used Today       | ❌ No                           | ✅ Yes (TLS 1.2 or 1.3)             |
| 🧩 Compatibility    | Older browsers/tools           | Modern browsers and systems        |

**Note:**

`🔸  When people say "SSL Certificate", they usually mean a TLS certificate — just old naming habit.`

### What Is a SSL/TLS Handshake?

The SSL/TLS handshake is the initial process that occurs when a client (like a web browser or kubectl) connects securely to a server (like a web server or Kubernetes API server).

It establishes a secure, encrypted communication channel by:
* Authenticating both parties (usually the server),
* Exchanging encryption keys,
* Agreeing on encryption methods.

Purpose of the TLS/SSL Handshake:
- Encryption:      Set up encryption for the session
- Authentication:  Verify the server’s identity using certificates
- Key Exchange:    Securely agree on a symmetric session key
- Integrity:       Ensure message integrity via MAC or AEAD

<p align="center">
  <img src="images/k8s-27.JPG" alt="Description of my awesome image" width="600">
</p>

**Core SSL/TLS Terminology:**

| Term                                                     | Description                                                                      |
| -------------------------------------------------------- | -------------------------------------------------------------------------------- |
| **SSL (Secure Sockets Layer)**                           | Older encryption protocol (deprecated)                                           |
| **TLS (Transport Layer Security)**                       | Modern, secure protocol that replaced SSL                                        |
| **Handshake**                                            | Initial process to establish a secure connection                                 |
| **Cipher Suite**                                         | A set of algorithms used during TLS (includes key exchange, encryption, MAC)     |
| **Symmetric Encryption**                                 | Encryption using the same key for encryption and decryption (e.g., AES)          |
| **Asymmetric Encryption**                                | Uses a **public/private key pair** (e.g., RSA, ECDSA)                            |
| **Session Key**                                          | A symmetric key generated during the handshake, used to encrypt the actual data  |
| **X.509 Certificate**                                    | Digital certificate used to prove the identity of the server/client              |
| **Public Key**                                           | Used to encrypt data or verify signatures                                        |
| **Private Key**                                          | Used to decrypt data or sign information                                         |
| **CA (Certificate Authority)**                           | A trusted organization that issues and signs certificates                        |
| **CSR (Certificate Signing Request)**                    | A request to a CA to issue a certificate, generated with a private key           |
| **Digital Signature**                                    | A cryptographic proof that verifies the authenticity of a certificate or message |
| **MAC (Message Authentication Code)**                    | Ensures data integrity and authenticity                                          |
| **AEAD (Authenticated Encryption with Associated Data)** | Encryption mode that combines encryption and integrity (e.g., AES-GCM)           |
| **Perfect Forward Secrecy (PFS)**                        | Ensures that session keys are not compromised even if the private key is         |
| **ECDHE (Elliptic Curve Diffie-Hellman Ephemeral)**      | A key exchange algorithm that supports forward secrecy                           |
| **TLS Record Protocol**                                  | Part of TLS that encrypts application data and ensures integrity                 |
| **TLS Alert Protocol**                                   | Notifies both ends about errors or session termination                           |
| **SNI (Server Name Indication)**                         | Allows multiple TLS certs on a single IP address (e.g., virtual hosting)         |
| **Root Certificate**                                     | A self-signed certificate at the top of the trust chain                          |
| **Intermediate Certificate**                             | Bridges between root and server certs; used in certificate chains                |
| **Certificate Chain**                                    | A chain of trust: server cert → intermediate → root CA                           |
| **TLS Versions**                                         | TLS 1.0, 1.1 (deprecated), 1.2 (widely used), 1.3 (latest)                       |


**Symmetric Encryption:**

Symmetric encryption encrypts and decrypts using the same (one) key. Compared to asymmetric cryptography, this is more efficient. 
It needs less encryption time, uses fewer resources, and can transfer large volumes of data, while also maintaining confidentiality. 
Cipher text is the same or smaller than the plain text.

Some of the examples of widely-used symmetric encryption include:
* Data Encryption Standard(DES),
* Triple Data Encryption Standard(3DES),
* Advanced Encryption Standard (AES), more.


**Asymmetric Encryption:**

Asymmetric encryption uses two keys: a public and a private key. This means a message encrypted with a public key can only be decrypted with the corresponding private key. It provides better security as the keys are never shared and allow the user to authenticate data using digital signatures. It is slower than symmetric encryption and can
only be utilized for small amounts of data. Cipher text is the same or larger than plain text.

Some of the examples of widely-used asymmetric encryption include:
* Rivest Shamir Adleman (RSA),
* Digital Signature Algorithm (DSA),
* the Diffie-Hellman exchange method, and more.

#### A Note on Key and Certificate Naming Conventions:

* Certificates that include a public key typically use the extensions .crt or .pem (for example, server.crt, server.pem, client.crt, or client.pem). 
* Private keys are usually indicated by the extension .key or may include the word “key” in the filename (e.g., server.key or server-key.pem). 
Adhering to these naming conventions helps distinguish between public certificates and private keys.

<p align="center">
  <img src="images/k8s-31.JPG" alt="Description of my awesome image" width="600">
</p>

<p align="center">
  <img src="images/k8s-29.JPG" alt="Description of my awesome image" width="600">
</p>

<p align="center">
  <img src="images/k8s-30.JPG" alt="Description of my awesome image" width="600">
</p>

#### The Role of Digital Certificates:

Digital certificates serve as more than just containers for public keys. They provide essential details including:
* Certificate owner's identity (subject)
* Issuer’s identity
* Validity dates
* Subject Alternative Names (SANs) for multiple domain support

