# 13 SSL and TLS Protocol

## Related: Chapter 02 for cryptographic protocols and hybrid schemes in general — this chapter is the worked example that section was previewing; Chapter 05 for the symmetric algorithm used after the handshake; Chapter 06 for the asymmetric/certificate concepts used during it.

## What SSL/TLS Actually Is

**SSL/TLS (Secure Sockets Layer / Transport Layer Security)** is a cryptographic protocol (Chapter 03) — not an algorithm — designed to provide secure communication between two parties over a network. It's most closely associated with **HTTPS**, but it's also used to secure email, instant messaging, and VoIP traffic, among other things.

SSL/TLS serves three main purposes at once:

| Purpose | Description |
|---|---|
| **Encryption** | Prevents anyone other than the two communicating parties from reading the data. |
| **Authentication** | Verifies that the parties involved really are who they claim to be. |
| **Integrity** | Ensures the data hasn't been modified in transit. |

Those three purposes map directly onto three of the four objectives from Chapter 02 (confidentiality, authentication, and integrity) — SSL/TLS is, in effect, one concrete protocol built to deliver on several of cryptography's core goals simultaneously, and it does so by combining the cryptographically strong algorithms covered in earlier chapters rather than inventing new cryptography of its own.

## SSL vs. TLS: Same Lineage, Different Name

**SSL (Secure Sockets Layer)** was the original name for this protocol; it was later renamed **TLS (Transport Layer Security)**. So "SSL" is really the historical name and "TLS" is the current one — they're not two competing, unrelated protocols. **TLS 1.3** is the current recommended version; older versions, including **SSL 3.0**, are now obsolete and considered insecure.

## Certificates

An **SSL/TLS certificate** is a data file stored on the server that lets a connecting client do two things: verify the server's identity, and obtain the server's public key for the encrypted communication that follows (Chapter 06 covers exactly what a public key is used for). Browsers maintain a built-in list of trusted **Certificate Authorities (CAs)**, and if a server presents a certificate that doesn't trace back to one of those trusted CAs, the browser treats the connection as potentially insecure and warns the user accordingly.

In a browser, clicking the padlock icon in the address bar surfaces the underlying certificate details — validity dates, the signature hash algorithm used (Chapter 07), the server's public key, and so on.

## The SSL/TLS Handshake

Before any encrypted data actually flows, the two parties run through a sequence of steps called the **SSL/TLS Handshake**:

1. The client sends a request to the server.
2. The server responds with its SSL/TLS certificate and public key.
3. The client's browser checks whether that certificate is valid (i.e., traces back to a trusted CA).
4. The client generates a random **symmetric key**, and encrypts *that* key using the server's public key.
5. The server decrypts the symmetric key using its own private key.
6. The server uses this now-shared symmetric key to encrypt data sent to the client.
7. The client uses the same symmetric key to decrypt what it receives.

```text
Client
  |
  | Request
  v
Server
  |
  | Certificate + Public Key
  v
Client
  |
  | Generate symmetric key
  | Encrypt it with server's public key
  v
Server
  |
  | Decrypt with private key
  v
Shared Symmetric Key
  |
  | Encrypted communication
  v
Client <-----------------> Server
```

## Why This Is a Hybrid Scheme

This handshake is exactly the "hybrid scheme" previewed in Chapter 02: it uses **asymmetric cryptography** (the server's public/private key pair, Chapter 06) purely for the initial key exchange, and then switches to **symmetric cryptography** (Chapter 05) for the actual, ongoing data transfer.

| Cryptography | Role |
|---|---|
| Asymmetric | Used with the server's public/private key pair during the initial key exchange |
| Symmetric | Used to encrypt the subsequent bulk communication, using the now-shared symmetric key |

The reason for combining the two rather than picking one is exactly the trade-off discussed in earlier chapters: asymmetric cryptography elegantly solves the key-distribution problem that plain symmetric cryptography can't (Chapter 05/06), but it's computationally expensive to use for large volumes of ongoing traffic; symmetric cryptography is fast and efficient for that bulk traffic, but needs a securely shared key to start with. SSL/TLS uses each exactly where its strength matters most: asymmetric cryptography gets the symmetric key safely to both sides, and symmetric cryptography then does the heavy lifting for the rest of the session. The overall goal of the handshake is simply to establish everything needed for secure, authenticated, encrypted communication for the rest of the session.

## Related: Chapter 05 (Symmetric Algorithms) and Chapter 06 (Asymmetric Cryptography and Digital Signatures) together cover every cryptographic building block this chapter assembles; Chapter 02 for hybrid schemes as a general concept.

---

## Self-Test

1. During the TLS handshake, asymmetric cryptography is used only briefly, at the very start. Why doesn't TLS just use asymmetric cryptography for the entire session instead of switching to symmetric cryptography partway through?
2. If a browser encounters a certificate that doesn't trace back to a trusted Certificate Authority, what specifically about the handshake process (steps 2–3 above) has failed to be confirmed?
3. "SSL" and "TLS" are sometimes used interchangeably in casual conversation. Is that accurate, and if not, what's the more precise way to describe the relationship between the two names?

## Cheat Sheet

- **SSL/TLS:** a cryptographic protocol (not an algorithm) providing encryption + authentication + integrity.
- **SSL → renamed → TLS.** TLS 1.3 is current; SSL 3.0 and older are obsolete.
- **Certificate:** proves server identity + delivers the server's public key; validated against trusted CAs.
- **Handshake, in one line:** asymmetric crypto exchanges a symmetric key securely → symmetric crypto handles the rest of the session.
- **This is the standard example of a hybrid cryptographic scheme** (Chapter 02).
