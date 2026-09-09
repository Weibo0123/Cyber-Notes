# 07 Hash Functions and Hash Algorithms

## Related: Chapter 02 for where hash algorithms fit among the three major categories; Chapter 01 for why hashing is fundamentally different from encoding *and* from encryption; Chapter 08 for where hashing gets applied in practice.

## What a Hash Function Actually Is

A **hash function** is a mathematical function that operates on data at the bit level and produces a fixed-length output called a **hash** (or hash value/digest), usually represented in hexadecimal. Critically, hash functions are **not** encryption/decryption algorithms, and they don't use a key at all — this is the same core distinction from Chapter 01 (encoding vs. encryption), but hashing is its own third category, distinct from both.

A reliable cryptographic hash function has a specific set of properties:

- **Deterministic** — the same input always produces exactly the same hash.
- **Fast to compute** — even for very large inputs.
- **Fixed-length output** — regardless of how large or small the input is.
- **Avalanche effect** — a tiny change to the input, even a single bit, should produce a dramatically different-looking hash.
- **Collision-resistant** — different inputs should, as much as possible, avoid producing the same hash. When two different inputs *do* produce the same hash, that's called a **collision**, and resistance to collisions is one of the most important properties a hash function needs (see the Weaknesses section in Chapter 08).
- **One-way** — computing a hash from an input is easy; going backwards, from a hash to the original input, is not meant to be feasible at all. This is what makes hashing fundamentally different from encryption, which is specifically designed to be *reversible* given the right key.

## Major Hash Algorithms

| Algorithm | Output Size | Key Information |
|---|--:|---|
| **MD5** | 128 bits | Published 1991; vulnerable to collision attacks |
| **SHA-1** | 160 bits | Deprecated; vulnerable to collision attacks |
| **SHA-2** | 224–512 bits | Includes SHA-256 and SHA-512; widely used today |
| **SHA-3** | 224–512 bits | Based on the Keccak sponge construction |

### MD5

**MD5 (Message-Digest Algorithm 5)** was developed by Ronald Rivest and published in 1991. It produces a 128-bit (16-byte) hash. It's no longer considered secure for cryptographic purposes — it's vulnerable to collision attacks — and has largely been replaced by SHA-256 in security-critical applications, though it still shows up in some non-cryptographic contexts. On Linux, you can calculate an MD5 hash directly from the command line:

```bash
echo "LetsDefend" | md5sum
md5sum filename
```

### SHA-1

**SHA-1 (Secure Hash Algorithm 1)** was developed by the NSA and produces a 160-bit (20-byte) hash. It was once widely used for data integrity checks, digital signatures, and by certificate authorities, but it's now deprecated because of practical collision vulnerabilities and has been succeeded by stronger algorithms like SHA-256 and SHA-3 (Chapter 10 quantifies exactly how weak SHA-1's security has become).

### SHA-2

**SHA-2** is actually a family of hash functions — SHA-224, SHA-256, SHA-384, SHA-512, SHA-512/224, and SHA-512/256 — developed by the NSA, published by NIST, and introduced in 2001. SHA-2 provides substantially stronger security than SHA-1 and was specifically designed to resist the kind of collision attacks that eventually broke SHA-1. SHA-256 produces a 256-bit hash and SHA-512 produces a 512-bit hash, as the names suggest.

### SHA-3

**SHA-3** is a separate family of hash functions — SHA3-224, SHA3-256, SHA3-384, SHA3-512 — selected through a public NIST competition, with the winner announced on October 2, 2012. Unlike SHA-1 and SHA-2 (which share a related internal structure), SHA-3 is built on the fundamentally different **Keccak sponge construction**, giving it strong security properties and resistance to a wide range of cryptographic attacks.

On Linux, `sha256sum` calculates a SHA-256 hash directly:

```bash
sha256sum filename
```

## HMAC: Adding a Secret Key to a Hash Function

**HMAC (Hash-based Message Authentication Code)** is a keyed operation that combines a secret key with a hash function, and it's the most direct way this notebook resolves the "symmetric encryption gives confidentiality but not integrity" gap raised in Chapter 05. HMAC is used specifically to verify **integrity** (the data wasn't modified) and **authentication** (the data came from someone who actually possesses the shared secret key).

HMAC was developed in 1996 by Mihir Bellare, Ran Canetti, and Hugo Krawczyk. It requires a secret key known to both the sender and receiver, and it can be paired with different underlying hash functions — HMAC-SHA-256 and HMAC-MD5 are both common — though, like a plain hash, the resulting HMAC value is itself non-reversible. Its security ultimately depends on the strength and length of the secret key involved.

> **Hash function → no secret key.**
> **HMAC → hash function + secret key.**

## Related: Chapter 05 revisits HMAC as the fix for symmetric encryption's missing integrity guarantee; Chapter 08 covers exactly where MD5/SHA/HMAC get applied in practice, including Windows password hashing and the weaknesses (rainbow tables, collisions) that follow from the properties listed above; Chapter 10 covers how the security strength of these specific algorithms is tracked and compared over time.

---

## Self-Test

1. A hash function is described as "one-way." What does that actually mean in terms of what's easy vs. hard to compute, and why does that make hashing fundamentally different from encryption, which is deliberately built to be reversible?
2. HMAC and a plain hash function (like SHA-256 alone) both take an input and produce a fixed-size output. What's the one structural difference that makes HMAC usable for authentication in a way a plain hash isn't?
3. SHA-2 and SHA-3 are both currently considered secure, but they're built on very different internal structures. Why might having two structurally unrelated "healthy" hash families matter for long-term security, beyond just having a backup option?

## Cheat Sheet

- **Hash function properties:** deterministic, fast, fixed-length output, avalanche effect, collision-resistant, one-way.
- **MD5** (128-bit) and **SHA-1** (160-bit): both broken/deprecated for security-critical use.
- **SHA-2** (224–512 bit family) and **SHA-3** (224–512 bit family, Keccak sponge): the current standards.
- **HMAC** = hash function + secret key → provides integrity *and* authentication (a plain hash provides neither by itself).
- Linux: `md5sum`, `sha256sum` to hash files or piped input directly from the command line.
