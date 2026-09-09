# Traditional CTF Categories

Categories describe the *primary skill* a challenge tests, not a strict boundary — many challenges span several at once.

## 1. General Skills

Foundation for everything else: Linux command line, shell/scripting, filesystems & permissions, reading source code, encoding/decoding (hex, Base64), env variables, SSH/basic networking, basic Python.

```
cat  less  head  tail  grep  find  file  strings  xxd  chmod  ssh
```
The goal isn't memorizing commands — it's becoming comfortable investigating unfamiliar data and environments. (This is exactly what the Linux Fundamentals set covers in depth.)

## 2. Cryptography

**Encoding vs. encryption — the key distinction:**

| | Encoding | Encryption |
|---|---|---|
| Purpose | representation change | confidentiality |
| Needs a key | No | Yes |
| Examples | Base64, hex, URL-encoding | AES, RSA, ChaCha20 |

Advanced crypto challenges rarely mean "break a strong algorithm" — they usually exploit **weak parameters, bad implementation, poor key management, or protocol mistakes** (weak RNG, nonce reuse, padding bugs). Common topics: classical ciphers (Caesar, Vigenère, substitution), RSA, AES weaknesses.

Triage order: is this encoding? → if not, is it encryption? → which algorithm? → is a key required? → is there an implementation/parameter weakness?

## 3. Forensics

Investigating digital artifacts — images, audio, video, documents, archives, disk images, memory dumps, PCAPs, logs — to recover hidden info or reconstruct events.

- **File analysis:** verify actual file type (`file`, `strings`, `xxd`) — never trust the extension.
- **Image forensics:** EXIF/metadata, steganography (incl. LSB), embedded/appended data, channel analysis.
- **Network forensics:** PCAP/PCAPNG via Wireshark/tshark/tcpdump → identify protocols → find suspicious traffic → inspect streams → extract & analyze files.
- **Disk/memory forensics:** deleted/hidden files, filesystem artifacts, process info, timelines.

Key idea: forensics is evidence-driven — you analyze artifacts rather than modify a live system to exploit it.

## 4. Web Exploitation

```
Browser → HTTP Request → Web Server → Application → Database/Files/Other Services
```
Requires understanding HTTP: requests/responses, headers, params, GET/POST, cookies, sessions, auth.

**Common vulnerabilities:**
- **SQL Injection** — user input incorrectly incorporated into a SQL query
- **XSS** — attacker content executed as JS by the browser (reflected / stored / DOM-based)
- **Auth vs. Authz** — Authentication = *who are you?*; Authorization = *what can you do?* Many web bugs are broken authorization logic, not broken authentication.
- Also common: path traversal, command injection, SSRF, SSTI, file inclusion, insecure deserialization, prototype pollution, session bugs.

Key idea: you need both how the app is *supposed* to work, and how attacker-controlled input makes it behave *differently*.

## 5. Reverse Engineering

Given a compiled binary (no source), recover its logic:
```
Binary → static/dynamic analysis → understand logic → identify key conditions → construct correct input
```
Tools: `file`, `strings`, `readelf`, `nm`, `objdump`, radare2, Ghidra, `gdb`, `strace`, `ltrace`.

Focus questions: where's `main()`? Where does input enter? How is it processed/validated? What indicates success, and what happens after? You rarely need to understand *every* instruction — just the path relevant to the flag condition.

## 6. Binary Exploitation / Pwn

The RE/Pwn distinction: RE asks *"what does this program do?"*; Pwn asks *"how do I make it do something it wasn't meant to?"*

**Common vulnerability classes:**
- **Buffer overflow** — writing past a buffer, corrupting adjacent memory/control data, potentially redirecting execution
- **Format string bugs** — unsafe use of user-controlled format strings → info leaks, arbitrary reads/writes
- **Use-after-free** — using an object after it's freed (`allocate → use → free → use again → vulnerability`)

Also: heap memory, integer bugs, ROP, control-flow hijacking, and mitigations you'll need to work around or account for (NX, ASLR, PIE, stack canaries).

Typical foundation stack: C → memory → assembly → calling conventions → ELF → debugging → RE → exploitation. (This is where the Computer Architecture notes — stack, calling convention, ELF, buffer overflow/EIP control — plug directly in.)

## How Categories Relate

```
General Skills underlies everything
Forensics / Web / Crypto often stand semi-independently
Forensics can feed into Reverse Engineering
Reverse Engineering underlies Binary Exploitation
```
Not a strict hierarchy — e.g. a Web challenge might simultaneously need crypto, Linux, and scripting knowledge.

## Cheat Sheet

```
General Skills     → Linux, shell, encoding, scripting        (foundation for all others)
Cryptography         → encoding(no key) vs encryption(key); attack the WEAKNESS, not the algorithm
Forensics             → verify real file type; EXIF/stego/PCAP/disk/memory artifacts
Web Exploitation       → HTTP flow; SQLi, XSS, auth vs authz, path traversal, SSRF, SSTI
Reverse Engineering      → recover logic from a binary: main → input → validation → success condition
Binary Exploitation/Pwn   → make the program misbehave: overflow, format string, UAF, ROP, mitigations

Strong CTF players build transferable fundamentals (Linux, scripting, networking, debugging)
and apply them across categories — categories test skills, not isolated trivia.
```
