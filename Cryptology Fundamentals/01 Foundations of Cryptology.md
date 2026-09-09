# 01 Foundations of Cryptology

## Cryptology, Cryptography, and Cryptanalysis

**Cryptology** is the science of designing and analyzing cryptographic algorithms in order to provide data security. It draws on mathematics, electrical engineering, and computer science, and it splits into two complementary fields.

**Cryptography** is the constructive side: it develops the techniques and algorithms that transform data into a protected form. **Cryptanalysis** is the destructive (or evaluative) side: it studies those same algorithms looking for weaknesses, and asks whether encrypted data can be recovered without the intended key. The two fields depend on each other — cryptography can't claim an algorithm is secure until cryptanalysis has genuinely tried to break it, and cryptanalysis needs cryptographic constructions to analyze in the first place. Chapter 09 (Cryptographic Attacks) is entirely about the cryptanalysis side of this relationship.

## Plaintext and Ciphertext

**Plaintext** is the original, unprotected input to a cryptographic algorithm — it can be ordinary text, binary data, or any other format the algorithm accepts. **Ciphertext** is what comes out the other side: the protected form produced from plaintext by an encryption algorithm.

A simple illustration, using a Caesar cipher (covered in depth in Chapter 04) with a shift of 3:

```text
Plaintext:  cipher
Ciphertext: flskhu
```

Each letter has simply been shifted three places forward in the alphabet (c→f, i→l, p→s, h→k, e→h, r→u).

## Encryption and Decryption

**Encryption** is the process of turning plaintext into ciphertext using a cryptographic algorithm. **Decryption** is the reverse: turning ciphertext back into plaintext, again using a cryptographic algorithm (and, in almost every practical scheme, the correct key).

## The Key

A **key** is the value fed into an encryption or decryption algorithm that actually controls what the algorithm does to the data. Two people running the *same* algorithm with two *different* keys will get two different, unrelated ciphertexts from the same plaintext. This is worth internalizing early, because it underlies almost everything else in this notebook: the algorithm is usually public knowledge, and the key is the part that has to stay secret (see Chapter 03 for Kerckhoffs' Principle, which makes this idea precise).

## Encoding and Decoding Are Not Encryption

It's easy to mentally lump "encoding" in with "encryption" because both transform data into a different-looking form, but they solve different problems:

| Concept | Purpose | Requires a Key? |
|---|---|---|
| Encryption | Protect data from unauthorized reading | Yes |
| Decryption | Recover the original data from ciphertext | Yes |
| Encoding | Change how data is represented | No |
| Decoding | Reverse an encoding | No |

**Encoding** just converts data into a specific representation or character set — it doesn't use a key, and it isn't designed to provide any security at all. **Decoding** reverses that conversion. **Base64** is the most common encoding scheme you'll run into; it's covered in detail in Chapter 12, including why it should never be mistaken for an encryption mechanism.

## Related: Chapter 03 (Key Cryptographic Vocabulary) for Kerckhoffs' Principle and keyspace; Chapter 04 (Historical Ciphers) for more on the Caesar cipher; Chapter 12 (Base64) for encoding in practice.

---

## Self-Test

1. Cryptography and cryptanalysis are often described as "two sides of the same coin." What does each side actually do, and why can't cryptography be evaluated without cryptanalysis?
2. Base64-encoded text and AES-encrypted ciphertext can both look like meaningless strings of characters at a glance. What's the actual difference in what's happening to the data, and what does that imply about trying to use Base64 to "protect" something?
3. If someone tells you a cryptographic algorithm is secure "because the algorithm itself is a trade secret," what concept from this chapter should make you skeptical?

## Cheat Sheet

- **Cryptology** = Cryptography (build) + Cryptanalysis (break/evaluate).
- **Plaintext** → (encryption, using a key) → **Ciphertext** → (decryption, using a key) → **Plaintext**.
- **Key**: the secret value that determines what an encryption/decryption algorithm actually does.
- **Encoding/Decoding**: no key, no security guarantee — just a change of representation (e.g. Base64).
- **Encryption/Decryption**: requires a key and is meant to provide actual security.
