# CTF Challenge Solving Methodology

The core skill in CTFs isn't knowing the answer immediately — it's investigating systematically. This workflow applies across Web, Crypto, Forensics, RE, Pwn, OSINT, and Misc.

## 1. Read the Description Carefully

Look for keywords, filenames, URLs, IPs, ports, credentials, and hints about technique. Don't assume a detail is flavor text — a seemingly minor line is sometimes the clue.

> **Principle:** understand exactly what information you've already been given before touching the challenge.

## 2. Check the Hints

If hints are available and not penalized, use them — but try to understand *why* a hint works rather than following it mechanically. That's what turns it into transferable knowledge.

## 3. Identify Provided Files — Don't Trust Names/Extensions

```bash
file challenge
strings challenge
xxd challenge | head
```
Ask: what is this file, really? Compressed? Encrypted? Embedded data? Metadata? Other files inside it? An `image.jpg` might actually be a ZIP, a corrupted image, a polyglot, or contain steganographic data.

> **Principle:** identify the data before trying to solve the data.

## 4. Inspect All Endpoints and Services

A challenge may expose more than one entry point — a webpage might have APIs behind it, a binary might talk to another service, a server might expose multiple ports.

```
Web:      HTML → JS → HTTP headers → cookies → parameters → APIs
Network:  IP → Port → Protocol → Service → Input/Output
```

> **Principle:** don't assume the obvious interface is the only interface.

## 5. Identify the Challenge Type

Once you've looked at what you're given, ask "what am I actually looking at?" — a file suggests forensics/RE/stego/crypto; a binary suggests RE/pwn; a website suggests web exploitation; a PCAP suggests network forensics; strange text suggests encoding/cipher/compression. Correctly identifying the category eliminates a large space of wrong approaches early.

## 6. Start With Cheap, Simple Checks First

```bash
file target; strings target; ls -la; xxd target | head
checksec --file=binary; readelf -h binary   # binaries specifically
```
There's no reason to spend an hour reversing a binary if `strings` already reveals the clue. Cheap checks first, expensive techniques second.

## 7. Form and Test a Hypothesis

E.g. *"this looks Base64-encoded"* or *"the site trusts a client-controlled parameter."* Then test it:
```
Observe → Hypothesis → Test → Evidence → Update hypothesis → Test again
```
This beats randomly trying tools.

## 8. Time-Box Yourself

Getting stuck is normal. Give yourself a rough limit (e.g. ~30 minutes) of active investigation; if you make no progress, step away and come back — a fresh look often reveals what you missed.

## 9. Search the Internet — for Concepts, Not Just Solutions

Search unfamiliar concepts (`ELF PT_INTERP meaning`, `format string vulnerability`) rather than the challenge name directly. Searching *concepts* builds transferable understanding; searching *"[challenge name] writeup"* just gets you an answer — both have their place, but know which one you're doing.

## 10. Read Writeups When Truly Stuck

```
Try yourself → research concepts → try again → still stuck? → read writeup
→ understand the author's reasoning → reproduce it yourself
```
Don't just copy the flag — ask what clue you missed, what assumption was wrong, and how you'd recognize this pattern next time.

## 11. Use AI as a Learning Tool, Not an Answer Machine

Ask for a hint, a direction, or an explanation rather than "give me the flag" — e.g. *"the program crashes after ~32 bytes, what should I investigate next?"* A useful escalation ladder:
```
1. What should I investigate?
2. Give me a small hint.
3. Explain the relevant concept.
4. Show me how to apply it.
5. Show the complete solution.
```
Use the smallest amount of help that lets you keep learning.

## 12. Keep Notes While Solving

Track file type, interesting strings/functions, your current hypothesis, and next steps as you go — this avoids repeated work and keeps your reasoning reproducible, especially for RE/Pwn/Forensics.

## The Investigation Loop

```
Read description → check hints → identify files/services → initial analysis
→ form hypothesis → test hypothesis
   progress?  → yes: continue
              → no:  research → try again → still stuck? → writeup → understand why → reproduce
```

## Cheat Sheet

```
Practical checklist:
[ ] Read description, identify the goal, check hints
[ ] Download files, identify REAL file types (don't trust names)
[ ] Identify endpoints/services (there may be more than one)
[ ] Determine likely category
[ ] Cheap checks first: file / strings / xxd / ls -la
[ ] Form a hypothesis → test it → gather evidence → repeat
[ ] Time-box yourself; step away if stuck
[ ] Search CONCEPTS when unfamiliar, not just "challenge writeup"
[ ] Keep notes: file type, strings, functions, hypothesis, next step
[ ] Writeup only after genuinely trying — then reproduce + understand why

Core mindset: you rarely know the answer up front.
The skill is systematically reducing uncertainty, not memorizing tricks.
```
