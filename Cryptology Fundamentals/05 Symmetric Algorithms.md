# 05 Symmetric Algorithms

## Related: Chapter 02 for where symmetric algorithms fit among the broader categories; Chapter 03 for block/stream cipher vocabulary and Kerckhoffs' Principle; Chapter 04 for DES and AES in their historical context.

## The Basic Model

**Symmetric cryptography** uses the *same* key for both encryption and decryption. In the classic two-party scenario: Alice encrypts a plaintext message using a shared secret key, sends the resulting ciphertext across an otherwise insecure channel, and Bob — who has the same key — decrypts it to recover the plaintext. The key itself has to reach both parties through some secure channel beforehand and stay secret afterward. Assuming the algorithm itself has no exploitable weakness, an attacker who intercepts the ciphertext in transit can't recover the plaintext without also obtaining that key.

This is worth restating from Chapter 03 in context: Kerckhoffs' Principle says the algorithm doesn't need to be secret, only the key does. A symmetric system that remains secure even when its algorithm is fully public (as DES and AES both are) is doing exactly what Kerckhoffs' Principle asks for — and a system that only "works" because the algorithm's inner workings are hidden hasn't really been tested by the broader cryptanalysis community at all.

### Confidentiality Is Not the Same as Integrity

Symmetric encryption on its own provides **confidentiality** — an attacker who intercepts the ciphertext can't read it. It does **not**, by itself, provide **integrity**: nothing stops an attacker from tampering with the ciphertext in transit, since flipping bits in an encrypted blob doesn't require understanding what it decrypts to. Detecting that kind of tampering needs additional mechanisms layered on top (this is part of why HMAC, covered in Chapter 07, combines a hash function with a secret key rather than relying on encryption alone).

Symmetric algorithms are heavily used specifically because they're fast and efficient, especially on large volumes of data — which is exactly the trade-off explored below.

## Important Symmetric Algorithms

### DES — Data Encryption Standard

DES is a symmetric-key **block cipher** developed by IBM and adopted as a U.S. federal standard in 1977 (Chapter 04). It uses a 64-bit block size and a 56-bit key, processed through 16 rounds of a **Feistel network**, bracketed by an initial and a final permutation. DES is now considered insecure — not because of a mathematical flaw in the design, but simply because its 56-bit key is far too short to resist a brute-force attack (Chapter 09) with modern computing power.

### 3DES / Triple DES

**Triple DES (3DES)** was developed to extend DES's useful life by applying the DES algorithm three times in sequence, and it saw wide adoption from the late 1990s onward. It keeps DES's 64-bit block size but can use either three separate 56-bit keys (giving a nominal 168-bit key length) or two 56-bit keys (giving 112 bits). It's meaningfully more secure than single DES, but today it's mainly associated with legacy systems rather than new designs.

### AES — Advanced Encryption Standard

**AES** is the modern standard symmetric block cipher, selected by NIST as the winner of its encryption-standard competition in October 2000 and formally published as the FIPS 197 standard in November 2001 (see the ⚠️ note in Chapter 04 on why both dates appear in different sources), replacing DES and 3DES. It uses a fixed 128-bit block size with a choice of 128-, 192-, or 256-bit keys, and — structurally, unlike DES's Feistel network — is built around a **Substitution-Permutation Network (SPN)**, running multiple rounds of substitution, permutation, and mixing operations. With an appropriate key length, AES provides strong security while remaining fast and efficient in both software and hardware, which is why it's the default choice across secure communications, data storage, and most modern encryption systems generally.

### IDEA — International Data Encryption Algorithm

IDEA is a symmetric block cipher developed by James Massey and Xuejia Lai, first released in 1991. It uses 64-bit blocks with a 128-bit key, combining substitution, permutation, and modular arithmetic. It was previously used in secure communications and data storage, but has largely been superseded by AES; its adoption was also historically limited by patent restrictions.

### Skipjack

Skipjack is a symmetric block cipher developed by the U.S. National Security Agency (NSA) in the 1990s as part of the **Clipper Chip** initiative. It uses 64-bit blocks, an 80-bit key, and a 32-round Feistel network, and it was specifically designed to allow government access for law-enforcement and national-security purposes. The algorithm was initially classified and later declassified; the controversy around its built-in government access contributed heavily to its lack of broader adoption.

### RC5

RC5, developed by Ronald Rivest in 1994, is notable for its simplicity and flexibility rather than for widespread deployment. It supports variable block sizes, variable key lengths, and a variable number of rounds (at least 12 were recommended for strong security), built out of simple operations like modular addition and bitwise XOR. It's seen far less real-world adoption than AES.

### Comparison

| Algorithm | Block Size | Key Size | Structure / Main Feature |
|---|--:|--:|---|
| DES | 64 bits | 56 bits | 16-round Feistel network |
| 3DES | 64 bits | 112/168 bits | DES applied three times |
| AES | 128 bits | 128/192/256 bits | Substitution-Permutation Network |
| IDEA | 64 bits | 128 bits | Substitution, permutation, modular arithmetic |
| Skipjack | 64 bits | 80 bits | 32-round Feistel network |
| RC5 | Variable | Variable | Variable rounds, modular addition, XOR |

## Strengths and Weaknesses

Symmetric algorithms are generally very fast and can encrypt large volumes of data efficiently, which makes them well-suited to online communication, data storage, and large-scale encryption tasks generally. With proper key management, they also provide genuinely strong encryption while staying computationally cheap.

The weaknesses are almost entirely about **key handling** rather than the math: keys need to be securely stored, securely shared, properly managed, and rotated when necessary — and this only gets harder as the number of communicating parties grows. The core problem underneath all of that is **key distribution**: two remote parties both need access to the *same* secret key, and getting that key to both of them securely, without it leaking in transit, is a genuinely hard problem on its own (this is precisely the problem asymmetric cryptography was invented to solve — see Chapter 06).

## Applications

Symmetric encryption shows up anywhere data needs protecting at rest, in transit, or during processing: encrypting files, folders, and databases; securing communication channels (it's the workhorse inside protocols like HTTPS, once the initial key exchange is done — see Chapter 13); protecting messages in secure messaging apps; encrypting entire disks or individual files so that data stays protected even if a device is physically stolen; and protecting backup data both while it's being transferred to remote/cloud storage and while it sits there afterward.

One use case is worth a clarifying note: symmetric encryption *can* technically be used to store passwords — encrypting them and decrypting for comparison at login time — and some legacy systems do exactly this, typically because they need to recover the original plaintext password for some other purpose. But for ordinary login-password storage, the modern best practice is **hashing**, not encryption (Chapters 07–08), specifically because a hash never needs to be reversed at all, which removes an entire class of risk that comes with keeping a decryption key around.

## Related: Chapter 06 for how asymmetric cryptography solves the key-distribution problem raised above; Chapter 07 for HMAC as the standard way to add integrity on top of symmetric encryption; Chapter 13 for AES's role inside the TLS handshake.

---

## Self-Test

1. Why does encrypting a message with a symmetric cipher protect its confidentiality but not automatically its integrity? What kind of attack could still succeed against ciphertext an attacker can't read?
2. AES uses a Substitution-Permutation Network while DES uses a Feistel network. Does that structural difference alone tell you which one is "more secure"? What actually determines that DES is now insecure while AES isn't?
3. If two remote employees need to start communicating securely with symmetric encryption, what's the actual hard problem they need to solve first, and why doesn't the algorithm itself help with that part?

## Cheat Sheet

- **Symmetric encryption:** one key, used for both encryption and decryption.
- **DES:** 64-bit blocks, 56-bit key, Feistel network — insecure today (key too short).
- **3DES:** DES applied 3×, 112/168-bit effective key — legacy use only.
- **AES:** 128-bit blocks, 128/192/256-bit keys, SPN structure — the modern standard.
- **IDEA / Skipjack / RC5:** historically notable, largely superseded by AES.
- **Main strength:** speed/efficiency at scale. **Main weakness:** key management and key distribution.
- **Symmetric provides confidentiality, not integrity, on its own.**
