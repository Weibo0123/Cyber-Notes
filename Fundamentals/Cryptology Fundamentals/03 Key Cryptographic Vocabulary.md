# 03 Key Cryptographic Vocabulary

This chapter collects a handful of precise, load-bearing terms that get referenced constantly in the rest of this notebook. It's worth reading in full even if a term feels familiar — the exact distinctions here (protocol vs. algorithm, transposition vs. substitution) matter more than they first appear to.

## Cryptographic Algorithm vs. Cryptographic Protocol

A **cryptographic algorithm** is a set of mathematical rules and procedures that transforms plaintext into ciphertext (encryption) or ciphertext back into plaintext (decryption), providing confidentiality, integrity, and/or authenticity. AES is a cryptographic algorithm.

A **cryptographic protocol** is a different kind of thing: it's a set of rules defining *how* information is securely exchanged over a network or communication channel — specifying how mechanisms like encryption, authentication, and integrity protection actually get applied during real communication. HTTPS and TLS/SSL are cryptographic protocols. It's worth noting explicitly that not every network protocol is a *cryptographic* one — DNS, TCP, UDP, and ICMP are ordinary networking protocols with no cryptographic function of their own.

| Cryptographic Algorithm | Cryptographic Protocol |
|---|---|
| Mathematical rules for cryptographic operations | Rules for securely exchanging information |
| Performs operations like encryption/decryption | Defines how security mechanisms are used during communication |
| Example: AES | Example: TLS |

## Keyspace

The **keyspace** is the total set of all possible keys a cryptographic system could use. For a key of *n* bits, the number of possible keys is 2ⁿ. A 4-bit key, for instance, has a keyspace of 2⁴ = 16 possible values. The larger the keyspace, the more values an attacker would have to try in a brute-force search (Chapter 09) — which is exactly why key length is one of the first things to check when evaluating an algorithm's real-world security (Chapter 10 goes further into how key sizes across different algorithms are actually compared).

## Kerckhoffs' Principle

**Kerckhoffs' principle** states that the security of a cryptographic system should depend on the secrecy of the *key*, not the secrecy of the *algorithm*. In practice this means:

- The algorithm itself can be — and usually is — publicly known and published.
- The key is the one thing that has to stay secret.
- Knowing exactly how the algorithm works shouldn't compromise the system, as long as the key remains secure.

The reasoning behind this is that a publicly known algorithm can be scrutinized by the entire cryptanalysis research community, which tends to surface weaknesses that a "security through obscurity" approach never would. Chapter 05 revisits this principle specifically in the context of symmetric encryption.

## Substitution vs. Transposition

Classical ciphers are generally built from one (or both) of two basic operations:

A **substitution cipher** replaces each plaintext character with a different character or symbol, according to a rule or key. It comes in two flavors: **monoalphabetic** substitution uses one fixed mapping throughout (the Caesar cipher is the standard example), while **polyalphabetic** substitution uses multiple substitution alphabets across the message (the Vigenère cipher is the standard example). Both are covered in depth in Chapter 04.

A **transposition cipher**, in contrast, doesn't change any characters at all — it only rearranges their *positions*, based on some pattern or key. It's a permutation of the plaintext rather than a substitution of it. Transposition is mainly a classical-cryptography concept; modern algorithms don't use it directly as a standalone technique.

| Transposition | Substitution |
|---|---|
| Rearranges characters | Replaces characters |
| Characters remain unchanged | Characters are changed |
| Changes character order | Changes character representation |
| Based on permutation | Based on a mapping/rule |

## Block Cipher vs. Stream Cipher

These two terms describe *how* a symmetric-key algorithm processes data, and the distinction resurfaces throughout Chapter 05.

A **block cipher** processes plaintext in fixed-size chunks — typically 64 or 128 bits — with each block run through the encryption algorithm as a unit.

A **stream cipher** instead encrypts data sequentially, one bit or one byte at a time, using a **keystream**: a random or pseudorandom stream of data generated alongside the plaintext. Encryption is commonly just an XOR operation:

```text
Ciphertext = Plaintext XOR Keystream
```

Because the keystream advances alongside the plaintext, successive pieces of data get combined with successive keystream values rather than all being processed through one fixed block operation.

| Block Cipher | Stream Cipher |
|---|---|
| Processes fixed-size blocks | Processes data sequentially |
| Typical blocks: 64 or 128 bits | Bit or byte at a time |
| Symmetric-key encryption | Symmetric-key encryption |
| Operates on blocks | Uses a keystream |

## Related: Chapter 04 (Historical Ciphers) for substitution/transposition in action; Chapter 05 (Symmetric Algorithms) for block vs. stream ciphers applied to real algorithms; Chapter 09 (Cryptographic Attacks) for how keyspace size determines brute-force feasibility.

---

## Self-Test

1. TLS is often loosely called "an encryption algorithm" in casual conversation. Why is that description technically wrong, and what should it be called instead?
2. A 4-bit key has a keyspace of 16. What's the keyspace of an 8-bit key, and what does that tell you about why key length matters so much for brute-force resistance?
3. A cipher rearranges the letters of "SECRET" into "TERCSE" without changing any of the letters themselves. Is this substitution or transposition, and how do you know?

## Cheat Sheet

- **Algorithm** (e.g. AES) performs crypto operations; **protocol** (e.g. TLS) defines how algorithms get used in real communication.
- **Keyspace** for an *n*-bit key = 2ⁿ possible keys.
- **Kerckhoffs' Principle:** security lives in the key, not in hiding the algorithm.
- **Substitution** = changes *which* characters appear; **Transposition** = changes *where* characters appear.
- **Block cipher** = fixed-size chunks; **Stream cipher** = sequential, bit/byte-at-a-time, via a keystream and XOR.
