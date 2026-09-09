# 12 Base64 Encoding and Decoding

## Related: Chapter 01 for the encoding-vs-encryption distinction this entire chapter depends on — worth re-reading first if that boundary feels fuzzy.

## Base32 and Base64 Are Encoding, Not Encryption

**Base32** and **Base64** are encoding schemes: they represent binary data using a specific set of ASCII characters. They're purely data-*representation* methods — as established in Chapter 01, that means no key, and no security guarantee whatsoever. It's worth stating this plainly and directly, because Base64-encoded text superficially *looks* like it could be ciphertext to someone unfamiliar with it: **Base32 and Base64 are not encryption/decryption algorithms and should never be used as a security mechanism.**

(As background: **ASCII**, the American Standard Code for Information Interchange, is simply the standardized mapping of numeric values to characters that lets different systems represent the same characters consistently — Base64's character set is built directly on top of it.)

### Why Encode at All?

Base32/Base64 encoding exists to represent binary data as plain text without losing any information, specifically because many systems handle text far more reliably than arbitrary raw binary. It avoids characters that could cause problems during transmission or processing along the way.

### Base32 vs. Base64

The number in each name refers to the size of its encoding alphabet:

| Encoding | Character Set | Bits per Character |
|---|---|--:|
| **Base64** | 26 lowercase + 26 uppercase + 10 digits + `+` + `/` | 6 bits |
| **Base32** | 26 uppercase letters + 6 digits | 5 bits |

Because Base32 packs only 5 bits into each output character while Base64 packs 6, **Base32 output ends up longer than Base64 output for the same input data**. For example, encoding the string `base64_encoding`:

```text
Base64: YmFzZTY0X2VuY29kaW5n
Base32: MJQXGZJTGJPWK3TDN5SGS3TH
```

## How Base64 Encoding Actually Works

Base64 converts input data into groups of **6 bits**, then maps each 6-bit value onto a character from the Base64 alphabet. Working through the text `base64`:

**1. Convert the text to binary:**

```text
base64
↓
01100010 01100001 01110011 01100101 00110110 00110100
```

**2. Divide that binary data into 6-bit groups** (rather than the original 8-bit bytes).

**3. Map each 6-bit value to its corresponding Base64 character**, giving:

```text
YmFzZTY0
```

Decoding simply runs this whole process in reverse, recovering the exact original bytes.

## Padding

Base64 fundamentally operates on **3-byte input groups** (since 3 bytes = 24 bits = exactly four 6-bit groups, with no remainder). When the input length *isn't* a multiple of 3 bytes, `=` characters get added as **padding** to round the output out to a valid length. For example, `base` is 4 bytes long — not a multiple of 3 — so its Base64 representation needs padding:

```text
YmFzZQ==
```

## Base64 on the Linux Command Line

The `base64` command handles both directions:

```bash
# Encode
echo "base" | base64

# Decode (the -d flag means "decode")
echo "YmFzZQ==" | base64 -d
```

## Related: Chapter 09 (Cryptographic Attacks) — since Base64 provides no security at all, none of the attack categories there are even relevant to it; recognizing Base64-encoded data on sight (and knowing it can simply be decoded, not "cracked") is itself a useful, low-effort skill in forensics/CTF contexts generally.

---

## Self-Test

1. Someone finds a string of letters, digits, `+`, `/`, and possibly trailing `=` characters and assumes it must be encrypted. What in that description alone should immediately suggest it might just be Base64, and how would you check?
2. Why does Base32 always produce longer output than Base64 for the same input, given that both are "just" encoding schemes with no security properties?
3. If Base64 provides no security, why does it get used so heavily in practice — including inside genuinely secure systems (e.g. certificates, tokens) — instead of being seen as pointless?

## Cheat Sheet

- **Base64/Base32 = encoding, not encryption.** No key, no security — see Chapter 01.
- **Base64:** 64-character alphabet, 6 bits/character.
- **Base32:** 32-character alphabet, 5 bits/character → longer output than Base64 for the same data.
- **Padding:** Base64 works in 3-byte groups; `=` pads output when input length isn't a multiple of 3.
- **Linux:** `base64` to encode, `base64 -d` to decode.
