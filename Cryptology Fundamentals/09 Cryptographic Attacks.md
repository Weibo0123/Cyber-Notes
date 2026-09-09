# 09 Cryptographic Attacks

## Related: Chapter 01 for cryptanalysis as a concept; Chapter 03 for keyspace, which brute-force attacks target directly; Chapter 04 for the Caesar cipher this chapter uses as a worked example.

## Three Broad Categories of Attack

**Cryptanalysis** is the branch of cryptology that evaluates the security and reliability of cryptographic algorithms — cryptanalysts try to uncover weaknesses by developing attack techniques tailored to a specific algorithm's design. Those attacks generally fall into one of three categories:

1. **Classical cryptanalysis** — attacking the mathematical/statistical design of the cipher itself.
2. **Implementation attacks** — attacking how the algorithm was actually implemented, rather than its underlying math.
3. **Social engineering** — attacking the human beings who hold the keys, rather than the cryptography at all.

## Classical Cryptanalysis

Classical cryptanalysis tries to recover either the plaintext or the key directly from ciphertext, typically by exploiting structural weaknesses in the algorithm, a limited keyspace (Chapter 03), or other mathematical/statistical regularities in how the cipher behaves. Historical ciphers (Chapter 04) are the classic targets for this category of attack. Two important examples:

### Brute-Force Attacks

A **brute-force attack** simply tries every possible key against the ciphertext, checking each result for whether it produces meaningful plaintext. The process, for each candidate key, is: apply the key to the ciphertext, examine the result, and treat meaningful output as evidence that the correct key has been found.

Whether a brute-force attack is *feasible* comes down to two factors: **key length** and **available processing power**. A short key has a correspondingly small keyspace (Chapter 03) and is therefore much easier to exhaust completely — this is exactly the reason DES's 56-bit key (Chapter 05) is now considered insecure, even though nothing is mathematically "broken" about the algorithm itself.

A trivial illustration: if a password consists of exactly one numeric digit, there are only 10 possible values (0–9), so every single one can be tried. Note that for encrypted data specifically, "success" isn't a login prompt accepting a password — it's recognizing when a candidate key produces output that actually looks like meaningful plaintext, rather than random noise.

### Letter Frequency Analysis

**Letter frequency analysis** is a statistical technique that exploits how unevenly letters are distributed in natural language. It's particularly effective against **substitution ciphers** (Chapter 03) specifically, because a substitution cipher maps each plaintext letter to a *fixed* ciphertext letter — which means the statistical frequency pattern of the original language survives intact in the ciphertext, just relabeled.

In English text, **E** is the most frequent letter overall, at roughly 13%, and **T** is another notably frequent letter, at roughly 9%. An attacker can therefore: count how often each letter appears in the ciphertext, compare those counts against known language frequencies, infer likely plaintext-to-ciphertext letter mappings from the closest matches, and use those mappings to recover the plaintext. (Non-alphabetic characters like spaces and punctuation can generally just be ignored during this analysis.)

The Caesar cipher (Chapter 04) is a particularly easy target for this technique, precisely because it only has 26 possible shifts in total — frequency analysis can often pin down the exact shift directly, without needing to try all 26. For example, spotting this pattern in a ciphertext:

| Plaintext | Ciphertext |
|---|---|
| E | H |
| T | W |
| A | D |

immediately reveals a consistent shift of 3 across all three letters, identifying the cipher as a Caesar cipher with shift 3 (i.e., "ROT3").

## Implementation Attacks

**Implementation attacks** target *how* a cryptographic algorithm was actually built and deployed, rather than any weakness in its mathematical design — and they generally require more advanced technical knowledge than classical cryptanalysis.

### Side-Channel Attacks

A **side-channel attack** extracts secret information by observing physical or implementation-level characteristics of a system that have nothing to do with the algorithm's mathematics directly. A classic example: measuring a processor's electrical power consumption while it performs cryptographic operations, then applying signal-processing techniques to that power-consumption data in an attempt to recover secret information — potentially even the key itself.

## Social Engineering

**Social engineering** attacks bypass the cryptography entirely by exploiting human vulnerabilities instead. In a cryptographic context, this usually means trying to obtain a secret key or password directly from a person, rather than attacking the ciphertext at all — techniques include bribery, blackmail, espionage, coercion, and telephone-based persuasion. A common real-world pattern is impersonating someone from an organization's IT department and asking a user for their password or key under a plausible-sounding pretext, such as a routine software update. The effectiveness of this category of attack ultimately comes down to how convincingly the attacker can persuade the target — no cryptographic strength protects against a target who simply hands over the key.

## Summary

| Attack Type | Main Target | Basic Idea |
|---|---|---|
| Classical Cryptanalysis | Cryptographic algorithm/cipher | Exploit mathematical, structural, or statistical weaknesses |
| Brute Force | Key space | Try all possible keys |
| Letter Frequency Analysis | Substitution cipher | Exploit statistical letter frequencies |
| Implementation Attack | Implementation/system | Extract information from implementation behavior |
| Side-Channel Attack | Physical/observable characteristics | Analyze information such as power consumption |
| Social Engineering | Human | Manipulate or persuade people to reveal secrets |

## Related: Chapter 08 covers rainbow tables and collisions as the corresponding weaknesses specific to hash algorithms; Chapter 10 covers how algorithm/key strength is measured well before an attack ever becomes practical.

---

## Self-Test

1. A brute-force attack and a letter-frequency attack against the same Caesar-cipher ciphertext would both eventually recover the plaintext. What makes frequency analysis typically faster in this specific case, given how small the Caesar cipher's keyspace already is?
2. Side-channel attacks are described as "implementation attacks" rather than "classical cryptanalysis." Why does that distinction matter — what's fundamentally different about what each approach is actually attacking?
3. If an organization has a mathematically unbreakable encryption algorithm in place, why can social engineering still succeed against it? What does this imply about where an organization's security efforts should also be focused?

## Cheat Sheet

- **Three attack categories:** Classical cryptanalysis, Implementation attacks, Social engineering.
- **Brute force:** try every key; feasibility depends on key length + available compute (Chapter 03's keyspace).
- **Letter frequency analysis:** exploits fixed letter-frequency patterns preserved by substitution ciphers; especially effective against Caesar ciphers (only 26 keys).
- **Side-channel attack:** extracts secrets from physical signals (e.g. power consumption), not from the math.
- **Social engineering:** targets people, not algorithms — bribery, blackmail, impersonation, coercion.
