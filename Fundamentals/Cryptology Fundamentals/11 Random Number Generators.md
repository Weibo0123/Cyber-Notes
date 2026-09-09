# 11 Random Number Generators

## Related: Chapter 03 for keyspace, which random key generation ultimately has to fill unpredictably; Chapter 05 for symmetric keys, which are exactly the kind of value these generators need to produce securely.

## Why Randomness Matters in Cryptography

Randomness underpins a lot of what makes cryptography actually work — most directly, generating secure keys (Chapter 01, Chapter 03). A cryptographic key needs to be both **long enough** and **sufficiently random**; a key that's technically the right length but generated in a predictable way offers an attacker a shortcut that completely bypasses the algorithm's mathematical strength. There are three distinct types of random number generator worth knowing apart from each other:

- **True Random Number Generator (TRNG)**
- **Pseudorandom Number Generator (PRNG)**
- **Cryptographically Secure Pseudorandom Number Generator (CSPRNG)**

## True Random Number Generators (TRNG)

A **TRNG** generates values from genuine physical processes — coin flips and dice rolls are the intuitive examples, though in practice computer-based TRNGs draw on physical phenomena like electrical noise or radioactive decay instead. TRNGs provide real physical randomness, but they're generally slower and less practical for everyday software use than the alternatives below.

## Pseudorandom Number Generators (PRNG)

A **PRNG** generates values using a deterministic mathematical algorithm, starting from an initial value called a **seed** (which may itself come from a TRNG). The algorithm repeatedly calculates each next value from the previous internal state — which means the same seed will always reproduce the exact same sequence of "random" values. This makes PRNGs much faster and more practical than TRNGs, but it also means standard PRNGs are **not sufficiently unpredictable for cryptographic use**: anyone who figures out the seed (or the internal state) can reproduce the entire sequence going forward.

The C standard library's `rand()` function is a textbook example of an ordinary PRNG — useful for simulations or games, but never appropriate for generating a cryptographic key.

## Cryptographically Secure Pseudorandom Number Generators (CSPRNG)

A **CSPRNG** is a PRNG specifically engineered for cryptographic use. It produces values that are *computationally* unpredictable — meaning an attacker who has observed previous output still shouldn't be able to mathematically predict the next value, even knowing the general algorithm being used. This is the category actually appropriate for generating cryptographic keys, initialization vectors, and similar security-sensitive values.

| Type | Source | Predictability | Cryptographic Use |
|---|---|---|---|
| TRNG | Physical processes | Genuinely random | Possible |
| PRNG | Mathematical algorithm + seed | Predictable if state/seed is known | No |
| CSPRNG | Cryptographically secure algorithm | Computationally unpredictable | Yes |

## Random Number Generation on Linux

### `$RANDOM`

`$RANDOM` is a shell-level **PRNG** (not cryptographically secure — see the table above):

```bash
echo $RANDOM
```

It produces values in the range 0–32767.

### `/dev/random`

Linux also provides `/dev/random` as an actual source of random data suitable for cryptographic use. The `od` command can read and display values from it:

```bash
od -An -N2 -i /dev/random
```

Breaking down the options: `-A` sets the address/radix display format, and `-An` specifically suppresses the address column entirely; `-N2` tells `od` to read exactly 2 bytes (the `-N` parameter always controls how many bytes get read); and `-i` displays the resulting data as decimal integers rather than the default octal.

## Related: Chapter 03 (Keyspace) and Chapter 05 (Symmetric Algorithms) both depend directly on the keys these generators produce actually being unpredictable, not just correctly sized.

---

## Self-Test

1. `$RANDOM` and `/dev/random` are both available on Linux, but only one of them is generally appropriate as a source for a cryptographic key. Which one, and why does the other one fall short?
2. A PRNG seeded with the current Unix timestamp would produce a "random-looking" sequence, but it would be a poor choice for a cryptographic key. Why specifically — what could an attacker exploit here?
3. Why does it even matter whether a key is unpredictable, if it's already the "correct" length for its algorithm (per the keyspace discussion in Chapter 03)? What does a predictable key actually give an attacker?

## Cheat Sheet

- **TRNG:** physical randomness (e.g. electrical noise); slow but genuinely random.
- **PRNG:** algorithm + seed; fast but deterministic and predictable if the seed/state leaks — not safe for crypto keys.
- **CSPRNG:** a PRNG specifically hardened to be computationally unpredictable — the correct choice for cryptographic use.
- **Linux:** `$RANDOM` = ordinary shell PRNG (0–32767, not crypto-safe); `/dev/random` = a proper randomness source, readable via `od -An -N2 -i /dev/random`.
