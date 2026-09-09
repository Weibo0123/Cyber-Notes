# 01 What Is Digital Forensics

## Definition

In computer security, "Digital Forensics" is often shortened to just "Forensics." It's the branch of cybersecurity concerned with gathering and understanding evidence **after** an incident has already happened, in order to reconstruct what occurred. That "incident" could be an intrusion, a crime, or any other situation that needs to be reconstructed after the fact.

Digital forensics matters on two levels:

- **Legal / investigative:** it helps law enforcement determine whether someone was involved in a crime, and the evidence has to be solid enough to hold up to scrutiny.
- **Security improvement:** even outside a legal context, understanding exactly how a system was compromised is what lets you actually fix the underlying weakness, instead of just patching the symptom.

Digital forensics focuses on extracting evidence from devices that store information electronically — disk images, memory dumps, network traffic captures, and so on. It's a subfield of the broader discipline of Forensic Science, which covers investigation more generally and doesn't necessarily involve computer media at all (e.g., physical evidence, trace analysis).

## What forensics challenges usually look like in a CTF

In a CTF context, a "forensics" category challenge usually hands you a file or a piece of data and asks you to extract a hidden flag from it. Common formats include:

- A text or binary file that looks "normal" but has something hidden inside it
- A disk image
- A network packet capture (`.pcap` / `.pcapng`)
- Media files like images or audio (steganography — not covered in this notebook)

Regardless of the format, the core skill being tested is always the same: **figure out what you're looking at first, then pick the right tool to look inside it.** The next three chapters cover the most common general-purpose skills for that: string/file searching, disk analysis, and network traffic analysis.

## Related: Chapter 02 (file/string searching), Chapter 03 (disk forensics), Chapter 04 (network traffic analysis)

These three chapters are the actual "toolbox" this notebook builds. This chapter is just the framing — read the rest in order.

---

## Self-Test

1. What's the relationship between digital forensics and "Forensic Science" as a whole? Are the two terms interchangeable?
2. Why does forensic work "double" as a way to improve security, even in cases where the main goal is just proving guilt or innocence? Are these two goals ever in tension?

## Cheat Sheet

- **Digital forensics:** collecting and understanding electronic evidence after the fact to reconstruct events.
- **Forensic Science:** the broader field; digital forensics is the subfield specialized in electronic/computer evidence.
- **General CTF forensics workflow:** identify the format of what you're given → use the matching tools to dig progressively deeper → locate/extract the flag.
