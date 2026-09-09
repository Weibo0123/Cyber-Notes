# 02 Objectives, Classification, and Types of Cryptography

## Related: Chapter 01 (Foundations of Cryptology) for the underlying definitions this chapter builds on.

## Why Cryptography Has Multiple Objectives

Cryptography is not only about keeping secrets confidential — the source material for this notebook consistently organizes cryptographic goals around four core objectives: **confidentiality**, **integrity**, **authentication**, and **non-repudiation**.

### Confidentiality

Confidentiality ensures that information exchanged between two parties can't be understood by anyone else who intercepts it. A cryptographic algorithm has to resist attempts to decrypt protected data without authorization — and, per Kerckhoffs' Principle (Chapter 03), that resistance has to come from the secrecy of the key, not from keeping the algorithm itself hidden.

### Integrity

Integrity ensures that data hasn't been modified or tampered with in transit. This matters because an attacker performing a **Man-in-the-Middle (MitM)** attack can potentially intercept a communication and alter it before it reaches the recipient. Cryptographic mechanisms need to give the recipient a reliable way to detect that kind of unauthorized modification — encryption alone doesn't automatically provide this (see Chapter 05 for why symmetric encryption specifically doesn't guarantee integrity on its own).

### Authentication

Authentication ensures that communicating parties can actually verify each other's identity, preventing an attacker from impersonating a legitimate participant. The everyday example is a username/password login; **Two-Factor Authentication (2FA)** — an SMS code or an authenticator app, on top of the password — adds a second, independent verification step.

### Non-repudiation

Non-repudiation ensures a sender can't credibly deny having sent a message or performed an action, because the communication carries evidence tying it back to that specific sender. This becomes especially relevant when someone later tries to disown something they actually did — see Chapter 06, where digital signatures are built specifically to provide this guarantee.

| Objective | Main Goal |
|---|---|
| Confidentiality | Prevent unauthorized parties from reading information |
| Integrity | Detect unauthorized modification of information |
| Authentication | Verify the identity of communicating parties |
| Non-repudiation | Prevent a sender from denying an action or message |

> A note on scope: some broader treatments of information security also list **availability/accessibility** — making sure systems and data remain reachable — as a core security requirement alongside these four. Cryptography itself, though, is mainly the tool for the four objectives above; availability is usually addressed by other means (redundancy, capacity planning, etc.), which is why it's mentioned here only in passing.

## Why So Many Different Algorithms Exist

Given these different objectives, it makes sense that no single algorithm can serve every need. A few concrete reasons drive the diversity of cryptographic algorithms in use today:

- **Different security needs** — an algorithm tuned for one objective (say, integrity) isn't automatically well-suited to another (say, confidentiality).
- **Diverse use cases** — communication, data-at-rest storage, and authentication all place different demands on an algorithm.
- **Advances in computing power** — algorithms that were secure decades ago can become breakable as hardware gets faster (Chapter 10 covers how this gets tracked over time).
- **Ongoing research** — new algorithms can improve speed, efficiency, or resistance to cryptanalysis.
- **Standardization** — shared standards let otherwise-unrelated systems and devices interoperate securely.

## Classifying Cryptographic Algorithms

The most basic classification splits cryptographic algorithms into three major categories:

| Category | Main Characteristic |
|---|---|
| **Symmetric Algorithms** | Use one shared key for both encryption and decryption |
| **Asymmetric Algorithms** | Use a mathematically related pair of keys — public and private |
| **Hash Algorithms** | Produce a fixed-length hash from input data, without using any encryption key at all |

Symmetric algorithms are covered in Chapter 05, asymmetric algorithms and digital signatures in Chapter 06, and hash algorithms across Chapters 07–08.

## Types of Cryptography, Including Protocols

Looking at cryptography a level up from individual algorithms, it's useful to think in terms of three broader types:

**Symmetric ciphers** use the same key for encryption and decryption, which means the communicating parties have to share that key ahead of time. DES, 3DES, and AES (Chapter 05) are the common examples.

**Asymmetric ciphers**, also called public-key cryptography, use two mathematically related but different keys: a public key that can be freely shared, and a private key that must stay secret. In the typical encryption scenario, the public key encrypts and the matching private key decrypts. RSA is the classic example. Public-key cryptography itself dates to 1976, introduced through work associated with Whitfield Diffie, Martin Hellman, and Ralph Merkle (see Chapter 04 for the fuller historical timeline).

**Cryptographic protocols** define how algorithms — symmetric, asymmetric, or both — actually get used within a real security system, rather than being algorithms themselves. TLS is the standard example, and it's worth previewing here because it illustrates the next point directly: many real-world protocols combine symmetric and asymmetric cryptography into a **hybrid scheme**, using each where its particular strengths matter most. Chapter 13 (SSL/TLS Protocol) walks through exactly how this plays out step by step.

## Related: Chapter 03 for the precise vocabulary (keyspace, block vs. stream cipher, substitution vs. transposition) that underlies these categories; Chapter 13 for a worked example of a hybrid symmetric/asymmetric protocol.

---

## Self-Test

1. Why is "confidentiality" not the only objective cryptography needs to satisfy? Give an example of a scenario where confidentiality alone wouldn't be enough.
2. What's the difference between an "algorithm" classification (symmetric/asymmetric/hash) and a "protocol" like TLS? Why can TLS use more than one category of algorithm at once?
3. Non-repudiation and authentication both involve verifying something about identity. What's the actual distinction between what each one guarantees?

## Cheat Sheet

- **Four core cryptographic objectives:** Confidentiality, Integrity, Authentication, Non-repudiation.
- **Three algorithm categories:** Symmetric (shared key), Asymmetric (public/private key pair), Hash (no key, fixed-length output).
- **Cryptographic protocol** (e.g. TLS) ≠ cryptographic algorithm — it's the rulebook for how algorithms get combined and used.
- **Hybrid scheme** = symmetric + asymmetric cryptography combined in one protocol, to get the benefits of both.
