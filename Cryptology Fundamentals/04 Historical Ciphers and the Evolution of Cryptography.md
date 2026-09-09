# 04 Historical Ciphers and the Evolution of Cryptography

## Related: Chapter 03 for the substitution/transposition vocabulary this chapter puts to use; Chapter 09 for how these classical ciphers are actually broken.

## Substitution Ciphers, in Practice

Chapter 03 defined a substitution cipher abstractly as replacing each character with another according to a rule. In practice, that rule is usually a **substitution table**, which effectively acts as the cipher's key. For example:

```text
Plaintext:  cipher
Ciphertext: ynausk
```

Given the table used to produce this mapping, decrypting the message is straightforward. Without it, the ciphertext can still potentially be broken through analysis — Chapter 09 covers exactly how (letter frequency analysis in particular).

## The Shift Cipher (Caesar Cipher)

The **Caesar cipher**, also called the **shift cipher**, is a specific, simple substitution cipher: every letter is shifted a fixed number of positions through the alphabet, and that shift amount *is* the key. With a key of 3:

```text
Plaintext:  cipher
Ciphertext: flskhu
```

Encryption shifts every letter three positions forward; decryption shifts three positions back. When the shift runs past the end of the alphabet, it simply wraps back around to the beginning — so with a key of 19, for instance, A maps to T and B maps to U.

The cipher is historically tied to **Julius Caesar**, who is credited with using a similar method for secret military communications in the 1st century BC — making it one of the oldest documented ciphers still taught today.

### ROT13

**ROT13** is the specific Caesar cipher with a shift of 13. Because the English alphabet has 26 letters, 13 + 13 = 26 — meaning applying ROT13 twice in a row returns the original text exactly:

```text
Plaintext → ROT13 → Ciphertext
Ciphertext → ROT13 → Plaintext
```

This self-reversing property is unique to a 13-letter shift on a 26-letter alphabet, and it's why ROT13 shows up so often as a quick, informal "obfuscation" trick (it provides essentially no real security — it's a Caesar cipher, and Caesar ciphers have only 26 possible keys in total).

## A Practical Tool: CyberChef

**CyberChef** is a web-based tool that supports a wide range of encoding, decoding, and cryptographic operations, and it's a genuinely useful way to experiment with everything in this chapter without writing any code. Its interface has four main areas:

1. **Operations** — the library of available algorithms and transformations.
2. **Recipe** — the specific operations you've selected, along with their parameters.
3. **Input** — the data being processed.
4. **Output** — the result of running the recipe on the input.

For an operation that needs a parameter — the Caesar cipher's shift amount, for instance — that value gets configured directly in the Recipe section.

## From Ancient Ciphers to Modern Standards: A Timeline

Cryptography's development tracks fairly closely with broader advances in mathematics, engineering, and computing. The origins are genuinely ancient — early methods are associated with Egyptian hieroglyphs and Roman ciphers, though there's limited concrete detail about exactly how those early schemes worked. From there, a handful of developments mark the major turning points:

**1st century BC — the Caesar cipher**, described above.

**16th century — the Vigenère cipher.** Associated with Blaise de Vigenère, this is a **polyalphabetic** substitution cipher — a meaningful step up in complexity from Caesar's single fixed shift. It uses a key (rather than one number) together with a **Vigenère table**, so that different characters in the plaintext can end up encrypted using different substitution alphabets depending on where they fall relative to the key. The ciphertext comes from finding the intersection of each plaintext character and the corresponding key character in the table. For a long time this was considered very difficult to break, until cryptanalysis techniques advanced enough to catch up with it.

**20th century — the Enigma machine.** Used by Germany during World War II, Enigma was a complex electromechanical device built around a **rotor cipher**: symmetric-key, letter-by-letter encryption, where each letter is transformed into another letter, and — critically — that transformation itself changes as the rotors physically rotate with each keystroke, rather than staying fixed the way a Caesar shift does. The same key configuration was used for both encryption and decryption. Breaking Enigma was one of the defining events in the history of cryptanalysis.

**1976 — Diffie-Hellman and the birth of asymmetric cryptography.** Whitfield Diffie and Martin Hellman published *New Directions in Cryptography*, showing that two parties could perform cryptographic operations together without having to share a secret key beforehand. This is the conceptual foundation of all public-key (asymmetric) cryptography (Chapter 06).

**1977 — RSA.** Ron Rivest, Adi Shamir, and Leonard Adleman developed RSA, turning the Diffie-Hellman insight into a practical public-key algorithm, and RSA remains a foundational part of modern cryptography.

**1977 — DES standardized.** NIST published the **Data Encryption Standard (DES)**, an important early step toward standardizing cryptographic algorithms rather than leaving every system to invent its own (Chapter 05 covers DES itself in detail).

**Early 1990s — hash algorithms emerge**, including MD5, as a fundamentally different category of cryptographic mechanism: keyless, one-way, and not intended to be decrypted at all (Chapters 07–08).

**2000–2001 — AES.** ⚠️ Two dates get used interchangeably in casual descriptions of AES's history, and it's worth being precise: NIST announced the selection of the Rijndael algorithm as the winner of the AES competition in **October 2000**, but AES only became a formal standard when NIST published it as **FIPS 197 in November 2001**. Either year can be defensible depending on whether "standardized" means "selected as the winner" or "formally published as a standard" — this notebook uses 2001 consistently in Chapter 05 to match the formal publication date. AES went on to become the most widely used symmetric algorithm in modern use.

| Period | Development | Key Concept |
|---|---|---|
| Ancient times | Early cryptographic methods | Hieroglyphs, Roman ciphers |
| 1st century BC | Caesar cipher | Fixed alphabetic shift |
| 16th century | Vigenère cipher | Polyalphabetic substitution |
| 20th century | Enigma machine | Rotor-based encryption |
| 1976 | Diffie-Hellman | Asymmetric cryptography |
| 1977 | RSA | Public-key cryptography |
| 1977 | DES standardized (NIST) | Cryptographic standardization |
| Early 1990s | Hash algorithms / MD5 | Keyless hashing |
| 2000–2001 | AES selected / standardized (NIST) | Modern symmetric encryption |

## Related: Chapter 05 (Symmetric Algorithms) for DES and AES in technical depth; Chapter 06 (Asymmetric Cryptography and Digital Signatures) for what Diffie-Hellman and RSA actually enabled; Chapters 07–08 for hash algorithms.

---

## Self-Test

1. Why does applying ROT13 twice return the original plaintext, but applying, say, a shift-of-5 Caesar cipher twice does not (it would produce a shift-of-10 result instead)?
2. What's the key structural difference between the Vigenère cipher and the Caesar cipher, and why does that difference make Vigenère harder to break?
3. Enigma used the "same key" for encryption and decryption, and so does a modern AES implementation — does that mean Enigma and AES are equally secure by modern standards? What else, besides "same key for both directions," determines an algorithm's strength?

## Cheat Sheet

- **Caesar cipher:** monoalphabetic substitution, fixed shift = key, only 26 possible keys.
- **ROT13:** Caesar cipher with shift 13; self-reversing on a 26-letter alphabet.
- **Vigenère cipher:** polyalphabetic substitution using a key + table; much harder to break than Caesar.
- **Enigma:** rotor-based, symmetric, letter-by-letter, transformation changes as rotors rotate.
- **1976 Diffie-Hellman → 1977 RSA:** birth of practical asymmetric/public-key cryptography.
- **1977 DES, 2000/2001 AES:** the two major NIST-standardized symmetric ciphers.
