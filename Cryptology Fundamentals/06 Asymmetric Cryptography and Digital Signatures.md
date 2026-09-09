# 06 Asymmetric Cryptography and Digital Signatures

## Related: Chapter 04 for the 1976 Diffie-Hellman / 1977 RSA history behind this chapter; Chapter 05 for the key-distribution problem this approach solves; Chapter 13 for asymmetric cryptography's role in the TLS handshake.

> A scope note before diving in: the source material behind this notebook goes very deep on symmetric algorithms and hash algorithms (Chapters 05, 07–08), but doesn't include a matching deep technical dive into RSA or other asymmetric algorithms themselves — the coverage here reflects that. What follows is the asymmetric-cryptography material that *is* present: the basic public/private key model, and its most concrete application, digital signatures.

## Asymmetric Cryptography, Briefly

**Asymmetric cryptography** — also called **public-key cryptography** — uses two different, but mathematically related, keys instead of one shared key:

- A **public key**, which can be freely shared with anyone.
- A **private key** (or secret key), which must be kept secret by its owner.

In the common *encryption* scenario, the public key encrypts and the matching private key decrypts — the reverse of how signing works, described below. **RSA** (Rivest–Shamir–Adleman, 1977) is the classic example of an asymmetric algorithm. The whole approach traces back to 1976, when Whitfield Diffie, Martin Hellman, and Ralph Merkle showed that two parties could perform cryptographic operations together without ever having shared a secret key beforehand — solving, in principle, the exact key-distribution headache that symmetric cryptography runs into (Chapter 05).

## What Is a Digital Signature?

A **digital signature** is a cryptographic mechanism that verifies who created and signed a piece of data, provides **non-repudiation**, and verifies the data's **integrity**. It's the digital analogue of a handwritten signature, though the underlying mechanism is completely different — it depends on asymmetric cryptography, not on anything resembling handwriting.

One concrete, everyday example: Windows executable (`.exe`) files can carry a digital signature. On Windows, right-clicking a file → **Properties** → the **Digital Signatures** tab → **Details** shows who signed the file and lets you verify that it was actually signed by that party.

## Signing Uses the Keys in the Opposite Direction from Encryption

This is the detail most worth remembering clearly, because it's easy to get backwards:

| Key | Purpose |
|---|---|
| **Private Key** | Used to *create/sign* the digital signature. Must stay secret. |
| **Public Key** | Used to *verify* a signature. Can be shared publicly. |

> **Private key → Sign**
> **Public key → Verify**

That's the reverse of the "public key encrypts, private key decrypts" pattern described above for ordinary asymmetric encryption — here, the *private* key is the one doing the active operation (signing), and the *public* key is what everyone else uses to check the result. The reason the private key must remain secret is straightforward: anyone who has it can produce signatures that appear to come from its rightful owner. The public key gets distributed precisely so that anyone can verify a signature produced by the corresponding private key, without ever needing access to the secret itself.

### The Signing and Verification Flow

```text
Sender
  |
  | Sign data with private key
  v
Signed Data
  |
  | Send
  v
Receiver
  |
  | Verify using sender's public key
  v
Signature Verification
```

## What Digital Signatures Actually Guarantee

Digital signatures deliver on two of the four objectives from Chapter 02 specifically:

- **Non-repudiation** — because only the holder of a specific private key could have produced a valid signature, that signature helps establish that the data really was signed by that key's owner.
- **Integrity** — verification confirms that the signed data hasn't been altered since it was signed; any modification breaks the signature check.

Because signing and verifying inherently require two *different* keys, digital signatures are necessarily built on **asymmetric** cryptography — a symmetric scheme, where the same key does both operations, couldn't provide this kind of one-directional, verifiable-by-anyone guarantee.

## Related: Chapter 07 explains why large data gets *hashed* before it's signed, rather than signed directly — a detail that connects this chapter directly to hash algorithms.

---

## Self-Test

1. For ordinary asymmetric *encryption*, the public key encrypts and the private key decrypts. For digital *signatures*, which key performs the active operation, and why is that the opposite direction from encryption?
2. If an attacker manages to steal someone's private signing key, what specifically can they now do, and why does the public key being freely available not help the attacker at all in this scenario?
3. Why couldn't a symmetric algorithm (Chapter 05) provide the same non-repudiation guarantee that a digital signature does, even if both parties trusted each other?

## Cheat Sheet

- **Asymmetric cryptography:** public key (shareable) + private key (secret), mathematically related.
- **Encryption:** public key encrypts → private key decrypts.
- **Digital signature:** private key signs → public key verifies (the reverse direction from encryption).
- **Digital signatures provide:** non-repudiation + integrity.
- **1976 Diffie-Hellman-Merkle → 1977 RSA:** the historical origin of this entire category (Chapter 04).
