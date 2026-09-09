# What Is a Flag?

A **flag** is a specific text string hidden or produced by a CTF challenge, submitted to the platform as proof the challenge was solved (a "Challenge Completion Token").

```
Solve challenge → find/obtain flag → submit → platform verifies → points
```

The flag isn't always the last piece of info you touch — getting to it might require decoding data, exploiting a bug, recovering something from network traffic, etc. The flag is the *proof*, not necessarily the *goal itself*.

## Flag Format

Competitions define a recognizable format, e.g. `picoCTF{...}`, `flag{...}`, `CTF{...}` — split conceptually into a **prefix** and **content**: `picoCTF` + `{l33tsp34k_phr4s3_1234abcd}`.

⚠️ `picoCTF{}` is **not universal** — it's specific to picoCTF. Always check the current competition's actual format before assuming.

## Flag Content

Can be a meaningful phrase, random characters, hex, encoded data, or leetspeak (numbers standing in for letters — `3→E`, `1→L/I`, `4→A`, `5→S`, `7→T`, so `l33tsp34k` = "leetspeak"). Don't assume content must be meaningful — many competitions generate flags randomly.

## Using the Format as a Search Clue

Once you know the format, it becomes a search pattern across almost any category:

| Category | Where to search |
|---|---|
| Forensics | File contents, metadata, strings |
| Network | PCAP data, HTTP requests/responses |
| Reverse Engineering | Program strings, memory |
| Binary Exploitation | Program output, memory, files |
| Web | HTML, JS, HTTP responses |
| Cryptography | Decrypted output |
| OSINT | Websites, public info |

E.g. if the format is `flag{...}`, grepping for `flag{` across dumped data, decompiled strings, or captured traffic is a cheap first move.

⚠️ A string matching the format is a **candidate**, not a confirmed flag — always verify by submitting it or confirming via the intended solution path.

## Cheat Sheet

```
Flag = proof of completion, submitted for points
Format = competition-specific (prefix{content}) — never assume it, check it
Content = may be meaningful OR random — don't rely on it "making sense"
Known format → useful grep pattern across files/traffic/output
Match ≠ confirmed — verify before trusting it
```
