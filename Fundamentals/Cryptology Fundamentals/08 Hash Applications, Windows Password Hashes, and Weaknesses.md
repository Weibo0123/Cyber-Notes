# 08 Hash Applications, Windows Password Hashes, and Weaknesses

## Related: Chapter 07 for the underlying algorithms (MD5, SHA family, HMAC) this chapter puts to use.

## Data Identification

Because a hash acts as a compact, deterministic fingerprint of a piece of data, it can serve as a **digital identity** for a file. This is a common technique in malware analysis specifically: a piece of malware can be identified by its hash, and malware-analysis databases can be queried using a known hash to instantly check whether a given sample has already been seen and analyzed before — no need to re-analyze the same file from scratch every time it turns up.

## Integrity Verification: Software Downloads

Hashes are the standard way to verify that a downloaded file matches what it's supposed to be, independent of whatever transport mechanism delivered it. The process works the same way whether you're verifying a general file or a specific software release:

1. The provider calculates a hash (SHA-256 is typical) of the original file.
2. That hash gets published alongside the download link.
3. A user downloads the file.
4. The user independently calculates the hash of the downloaded file.
5. The two hash values are compared.

If they match, the downloaded file has exactly the expected contents. If they don't match, the file has been corrupted or modified somewhere along the way — deliberately or otherwise. On Linux, this is as simple as running `sha256sum filename` and comparing the output against the published value.

## Integrity Verification: Digital Signatures

Chapter 06 covered what digital signatures guarantee; this is where hashing fits into how they actually work in practice. Asymmetric signing operations are computationally expensive, especially on large amounts of data — so instead of signing an entire large file directly, the standard approach is:

1. Hash the original data.
2. Sign the resulting fixed-size hash (not the original data).
3. The recipient gets both the original data and the signature.
4. The recipient independently recalculates the hash of the received data.
5. That freshly calculated hash is compared against the signed hash.
6. A match confirms the data's integrity has been preserved end-to-end.

```text
Large Data
    ↓
  Hash
    ↓
Hash Value
    ↓
Digital Signature
```

This is exactly why hashing and digital signatures are usually discussed together — hashing is what makes signing large data practical at all.

## Secure Credential Management

Hash functions let systems avoid ever storing a plaintext password. The basic login flow looks like this:

1. A user creates or changes their password.
2. The system hashes it immediately.
3. Only the resulting hash gets stored in the database — never the plaintext.
4. At login, the entered password gets hashed the same way.
5. The new hash is compared against the stored hash.
6. A match means authentication succeeds.

```text
Password
   ↓
 Hash Function
   ↓
Password Hash
   ↓
Database
```

Because hashing is one-way (Chapter 07), even someone with direct access to the database can't simply reverse a stolen hash back into the original password — they'd have to fall back on the kinds of attacks described below.

## Windows Password Hashes

Windows stores passwords as cryptographic hashes rather than as plaintext. Two historically important Windows hashing systems are worth knowing specifically, because they illustrate very different levels of security within the same category of problem.

### NTLM Hash

The **NTLM hash** underlies NTLM authentication. **NTLMv1** derives its NTLM hash using **MD4**; **NTLMv2** instead uses **HMAC-MD5** as part of its authentication process, and is meaningfully more secure than NTLMv1 as a result.

### LM Hash

The **LM (LAN Manager) hash** is an older, now-outdated and insecure password-storage method. Its process: the password gets split into 7-character blocks, each block is converted to uppercase, and **DES** is used to process the blocks, producing two separate password hashes. LM hashing has been dropped from modern versions of Windows entirely because of these accumulated weaknesses (splitting into fixed 7-character blocks and forcing uppercase both dramatically shrink the effective search space an attacker needs to brute-force).

## Strengths

**Fast computation** — hashes can generally be calculated very quickly, which makes them practical even for high-volume data processing and verification tasks.

**Fixed-length output** — regardless of input size, hash output stays a consistent length, which makes hash values easy and cheap to compare directly.

## Weaknesses

**Precomputed tables.** Because hashing is deterministic (Chapter 07), an attacker can precompute hashes for huge numbers of likely inputs ahead of time and simply look up a stolen hash against that table — **rainbow tables** are the standard example of this technique. This is part of why modern password-hashing systems add unique per-user "salt" values before hashing, though that detail isn't covered in this notebook's source material.

**Collisions.** A collision — two different inputs producing the same hash — becomes more likely, and more exploitable, the weaker or more outdated the underlying algorithm is. This is exactly why MD5 and SHA-1 are considered broken for security purposes today (Chapter 07): both have practical, demonstrated collision attacks, whereas SHA-2 and SHA-3 don't.

## Related: Chapter 07 for why one-wayness and collision resistance matter in the first place; Chapter 10 for how the security strength of specific hash algorithms (like the SHA-1 weakness referenced above) gets formally quantified and tracked over time.

---

## Self-Test

1. Why is a file's hash useful as a "digital identity" for malware analysis specifically, rather than just comparing files byte-by-byte? What property of hashing makes this shortcut possible?
2. When a large file gets digitally signed, the signature is actually applied to a *hash* of the file, not the file itself. What problem does this solve, and what would happen to a signature's validity if even one byte of the original file changed afterward?
3. LM hashes split passwords into 7-character uppercase blocks before hashing them with DES. Why does that specific design choice make LM hashes dramatically easier to attack than a modern password hash, even before considering DES's own weaknesses?

## Cheat Sheet

- **Digital identity:** a file's hash can substitute for the file itself when checking "have we seen this before" (e.g. malware databases).
- **Software/download verification:** compare a published hash against a locally computed one (`sha256sum`).
- **Digital signatures:** sign the *hash* of large data, not the data directly — the recipient re-hashes and compares.
- **Password storage:** store `hash(password)`, never the plaintext; re-hash at login and compare.
- **NTLMv1** → MD4-derived NTLM hash; **NTLMv2** → HMAC-MD5-based, more secure.
- **LM hash:** legacy, 7-char uppercase blocks + DES — badly weakened by its own design, now removed from modern Windows.
- **Weaknesses of hashing generally:** rainbow tables (precomputed hash lookups) and collisions (worse in older algorithms like MD5/SHA-1).
