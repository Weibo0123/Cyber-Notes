# 10 Monitoring Cryptographic Algorithm Security

## Related: Chapter 05 for AES/RSA key sizes referenced below; Chapter 07 for the SHA-1 weakness quantified here; Chapter 09 for why key length determines brute-force feasibility in the first place.

## Security Isn't a One-Time Judgment

An algorithm's security isn't a fixed, permanent fact — it changes over time as computing power increases, new cryptanalytic attacks are discovered, and recommended standards get revised. Because of this, evaluating whether an algorithm is "still secure enough" should rely on authoritative, actively maintained standards rather than intuition or dated assumptions. **NIST SP 800-57** is the key reference for this in the source material — it provides formal guidance on cryptographic key management and security strength.

## Security Strength

**Security strength** is a way of quantifying, in bits, the approximate computational effort required to break a given cryptographic mechanism. For hash algorithms specifically (Chapter 07), the strengths break down like this:

| Security Strength | Hash Algorithms |
|--:|---|
| ≤ 80 bits | SHA-1 |
| 112 bits | SHA-224, SHA-512/224, SHA3-224 |
| 128 bits | SHA-256, SHA-512/256, SHA3-256 |
| 192 bits | SHA-384, SHA3-384 |
| ≥ 256 bits | SHA-512, SHA3-512 |

**SHA-1** specifically provides less than 80 bits of security against collision attacks for digital-signature purposes — and that weak collision resistance is precisely why SHA-1 is now considered unsuitable for modern security-critical applications (Chapter 08 covers what a collision attack against a hash actually threatens).

## Key Size Is Not the Whole Story

Key size matters, but three important rules govern how to actually reason about it:

1. **Within the same algorithm**, a larger key generally does provide greater security.
2. A smaller key size for the same algorithm isn't automatically insecure — **AES-128** (Chapter 05) is still considered strong; **AES-256** simply provides a higher security strength on top of that.
3. **Key sizes cannot be compared directly across different algorithms.** A numerically smaller key in one algorithm can be dramatically stronger than a numerically larger key in a completely different algorithm — for example, **AES-128 is much stronger than RSA-1024**, despite RSA-1024's key being numerically larger.

The takeaway: **key length alone does not determine an algorithm's security.** The algorithm's own mathematical structure and known weaknesses have to be considered together with key length, not instead of it.

## Security Strength Equivalents Across Algorithm Types

Because different algorithm families are attacked in structurally different ways, they need different key sizes to reach roughly *equivalent* real-world security strength. NIST SP 800-57 tabulates this directly across four algorithm families: **Symmetric** (e.g. AES), **FFC** (Finite Field Cryptography — DSA, DH, MQV), **IFC** (Integer Factorization Cryptography — RSA), and **ECC** (Elliptic Curve Cryptography — ECDSA, EdDSA, DH, MQV).

### Strong Security Levels

| Security Strength | Symmetric | FFC (DSA, DH, MQV) | IFC (RSA) | ECC (ECDSA, EdDSA, DH, MQV) |
|--:|---|---|---|---|
| 128 bits | AES-128 | L=3072, N=256 | k=3072 | f=256–383 |
| 192 bits | AES-192 | L=7680, N=384 | k=7680 | f=384–511 |
| 256 bits | AES-256 | L=15360, N=512 | k=15360 | f=512+ |

### Lower Security Levels

| Security Strength | Symmetric | FFC | IFC (RSA) | ECC |
|--:|---|---|---|---|
| ≤ 80 bits | 2TDEA | L=1024, N=160 | k=1024 | f=160–223 |
| 112 bits | 3TDEA | L=2048, N=224 | k=2048 | — |

`L`, `N`, `k`, and `f` are the parameters representing key/domain sizes for each respective family — the exact numbers matter less than the overall pattern: reaching 128-bit-equivalent security takes a 128-bit *symmetric* key, but a **3072-bit** RSA key. This directly illustrates the "can't compare key sizes across algorithms" rule above — an RSA key needs to be far larger, in raw bits, to match the same practical security strength as a much smaller AES key, because the two families are attacked through entirely different mathematical avenues (brute-forcing a symmetric keyspace vs. factoring a large integer, in RSA's case).

## Key Takeaways

Cryptographic security changes over time, so evaluating it needs authoritative standards (NIST SP 800-57 is the reference used throughout this chapter) rather than guesswork. Security strength is measured in bits; larger keys generally help *within* the same algorithm, but key sizes can't be compared meaningfully *across* different algorithms — AES-128 being stronger than RSA-1024 despite its smaller numerical key size is the clearest illustration of that. And SHA-1's weak collision resistance is a concrete, current example of why an algorithm that used to be considered secure can stop being adequate as attacks and computing power both advance.

## Related: Chapter 05 for AES and RSA themselves; Chapter 07 for the hash algorithms whose strengths are tabulated above; Chapter 09 for why key length is the lever brute-force attacks pull on.

---

## Self-Test

1. AES-128 has a numerically smaller key than RSA-1024, yet AES-128 is considered much stronger. What's the actual reason for this, beyond "bigger numbers are always stronger"?
2. SHA-1 provides "less than 80 bits of security" for digital signatures specifically. Does that mean SHA-1 is completely useless for every purpose, or does the concern apply specifically to collision-resistant use cases like signatures?
3. Why does NIST publish a table equating, say, "128-bit security" across AES, RSA, and ECC, instead of just saying "use a 128-bit key everywhere"?

## Cheat Sheet

- **Security strength** = bits of computational effort estimated to break a mechanism; not a fixed property forever.
- **NIST SP 800-57**: the authoritative reference for cryptographic key-management and strength guidance.
- **Within one algorithm:** bigger key = more security (AES-128 → AES-256).
- **Across different algorithms:** key sizes are NOT directly comparable (AES-128 ≫ RSA-1024, despite RSA's larger number).
- **SHA-1:** < 80-bit security against collision attacks — unsuitable for modern digital signatures.
- **128-bit-equivalent security roughly needs:** AES-128 (symmetric) ≈ RSA-3072 (IFC) ≈ ECC 256–383 bits.
