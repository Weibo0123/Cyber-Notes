# Asymmetric Algorithms

---

## Overview

Traditional cryptography used **symmetric algorithms**, where the same key was required for encryption and decryption.

A major limitation was **secure key sharing**: communicating parties needed to share the secret key before they could securely communicate.

In **1976**, Whitfield Diffie and Martin Hellman published **"New Directions in Cryptography"**, introducing the concepts of:

- **Public key**
    
- **Private key**
    

This work established the foundation for **asymmetric cryptography** and addressed the problem of sharing secret keys.

### Public and Private Keys

Asymmetric cryptography uses a **key pair**:

|Key|General Role|
|---|---|
|**Public key**|Can be shared publicly|
|**Private key**|Must remain secret|

The security of asymmetric cryptography relies on mathematical problems that are computationally difficult to solve, making it impractical to derive the private key from publicly available information.

---

# Diffie-Hellman Protocol

The **Diffie-Hellman (DH) protocol** was introduced in the same 1976 paper.

> **Important:** Diffie-Hellman is a **protocol**, not an encryption algorithm.

Its purpose is to allow two parties to establish a **shared secret key** without directly transmitting that secret key.

### Basic Process

1. Both parties agree on a large prime number **p** and a primitive root **g** modulo `p`.
    
2. These parameters are publicly known.
    
3. Each party independently generates a **private key**.
    
4. Each party calculates a **public key** from its private key:
    
    - `A = g^a mod p`
        
    - `B = g^b mod p`
        
5. The parties exchange their public keys.
    
6. Each party combines the received public key with its own private key to calculate the same **shared secret**.
    

The important concept is that both parties can independently derive the same shared secret without directly transmitting it.

---

# Critical Asymmetric Algorithms

## RSA — Rivest-Shamir-Adleman

RSA is a widely known **asymmetric cryptographic algorithm** that uses a public/private key pair.

It was developed in **1977** by:

- **Ron Rivest**
    
- **Adi Shamir**
    
- **Leonard Adleman**
    

The algorithm is associated with MIT and Stanford University.

### Key Points

- Uses a **public key** and **private key**.
    
- Its security is based on the computational difficulty of **factoring the product of two large prime numbers**.
    
- Deriving the private key from the public key is computationally difficult when sufficiently large keys are used.
    
- Used for:
    
    - Secure communication
        
    - Digital signatures
        
    - Key exchange
        
- Enables secure communication over insecure networks such as the Internet.
    
- Longer key lengths generally provide stronger protection.
    
- RSA can support both:
    
    - **Encryption**
        
    - **Digital signing**
        

---

# Elliptic Curve Cryptography (ECC)

**Elliptic Curve Cryptography (ECC)** is a family of asymmetric cryptographic techniques based on the mathematics of **elliptic curves**.

ECC can provide strong security using relatively **short key lengths**.

The work was independently developed by:

- **Victor Miller** — 1986
    
- **Neal Koblitz** — 1987
    

### Key Points

- Uses mathematical properties of **elliptic curves over finite fields**.
    
- Can be used for:
    
    - Encryption
        
    - Digital signatures
        
    - Key exchange
        
- Can provide strong security with shorter keys, such as **256-bit keys**, compared with RSA.
    
- Generally requires fewer computational resources.
    
- Widely used in:
    
    - Secure communications
        
    - Mobile devices
        
    - IoT systems
        
- Security is based on the difficulty of the **Elliptic Curve Discrete Logarithm Problem (ECDLP)**.
    
- Its efficiency makes it suitable for **resource-constrained devices**.
    

> ECC is a cryptographic family rather than a single specific algorithm.

---

# ElGamal

**ElGamal** is an asymmetric encryption algorithm based on the mathematical difficulty of the **discrete logarithm problem**.

It was developed by **Taher ElGamal in 1985**.

### Key Points

- Uses:
    
    - Public key
        
    - Private key
        
    - Random session key
        
- Supports:
    
    - Secure data transmission
        
    - Digital signatures
        
    - Key exchange
        
- Security is based on the difficulty of solving the **discrete logarithm problem in a finite field**.
    
- Generally slower and requires larger keys than some alternatives such as RSA or ECC.
    
- A major characteristic is that encryption can **significantly increase the size of the encrypted message**; the source notes that it doubles the message length.
    

### Historical Note

ElGamal was initially advantageous because it was in the **public domain**, unlike RSA at the time.

RSA was released into the public domain in **2000**.

---

# Asymmetric Algorithm Comparison

|Algorithm / Protocol|Main Mathematical Basis|Main Characteristics|
|---|---|---|
|**Diffie-Hellman**|Discrete logarithm|Key agreement protocol|
|**RSA**|Integer factorization|Encryption and digital signatures|
|**ECC**|Elliptic Curve Discrete Logarithm Problem|Strong security with shorter keys|
|**ElGamal**|Discrete logarithm|Encryption, signatures, key exchange|

---

# Strengths and Weaknesses

## Strength

### Key Exchange Without Prior Sharing

The major advantage of asymmetric cryptography is that two parties can establish secure communication **without first sharing a secret key through a secure channel**.

This addresses one of the major limitations of symmetric encryption.

---

## Weaknesses

### Computational Complexity

Asymmetric operations are generally **more computationally intensive** than symmetric encryption.

Therefore, they are:

- Slower
    
- Less efficient for encrypting large amounts of data
    

### Key Length

Asymmetric algorithms generally require **longer keys** than symmetric algorithms to provide comparable security.

This can increase resource requirements.

### Quantum Computing

Future sufficiently powerful **quantum computers** could break many currently used asymmetric cryptographic algorithms.

This represents a potential long-term security concern.

---

# Applications of Asymmetric Algorithms

### Secure Communication

Used to help secure communication channels such as:

- Email
    
- Messaging applications
    
- VPNs
    

### SSL/TLS Security

Asymmetric cryptography can enable **secure key exchange between a client and server**.

This allows the parties to establish the symmetric encryption keys used for the communication session.

### Identity Verification

Asymmetric cryptography can be used to authenticate users and verify whether they are authorized to access a system or service.

### E-Commerce

Used to help protect online transactions and sensitive information exchanged between buyers and sellers.

### Electronic Signatures

Asymmetric cryptography enables the creation of **digital/electronic signatures**, providing:

- Authenticity
    
- Integrity
    

### Digital Certificates

**Public Key Infrastructure (PKI)** uses asymmetric cryptography to issue **digital certificates** that help validate the identities of individuals or entities in online transactions.

---

## Key Takeaways

- **Asymmetric cryptography** uses a **public/private key pair**.
    
- The **public key** can be shared; the **private key** must remain secret.
    
- Its major advantage is solving the **key-sharing problem** associated with symmetric cryptography.
    
- **Diffie-Hellman** is a **key-agreement protocol**, not an encryption algorithm.
    
- **RSA** is based on the difficulty of integer factorization.
    
- **ECC** is based on the difficulty of the Elliptic Curve Discrete Logarithm Problem.
    
- **ElGamal** is based on the discrete logarithm problem.
    
- Asymmetric cryptography is generally **slower and more computationally expensive** than symmetric cryptography.
    
- Common applications include **secure communication, SSL/TLS, identity verification, e-commerce, digital signatures, and PKI**.