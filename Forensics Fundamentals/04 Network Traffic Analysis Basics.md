# 04 Network Traffic Analysis Basics

## When you need packet analysis

Network / packet analysis is about reconstructing what happened on a network by examining captured packets. Challenges in this category usually hand you a packet capture file (commonly `.pcap` / `.pcapng`), which needs to be opened in a GUI tool called **Wireshark** — command-line string-searching tricks aren't much help here, because packets are structured and layered, and a GUI makes it much easier to expand and inspect each layer visually.

### Installing Wireshark

Download the version matching your OS from `https://www.wireshark.org/` — installation is fairly standard. If you're on a device that only runs a Linux container/environment (e.g. some Chromebooks in "Linux mode"), you'll need to enable that support first, then install via a package manager like `apt-get`.

## Strategy: filter first, then hunt for anomalies

Even with a tiny capture file, it's worth building the habit of **filtering before eyeballing everything** — real-world captures often have thousands or tens of thousands of packets, and manually clicking through each one doesn't scale. The earlier you build this filtering instinct, the less overwhelming a huge capture will feel later.

### Step 1: Rule out obviously irrelevant protocols

**ARP** (Address Resolution Protocol) packets, for example, just map IP addresses to hardware (MAC) addresses — nearly every capture has some, and they're almost never where a flag lives. In Wireshark's filter bar, type:

```
!arp
```

to hide all ARP packets and look at what's left. The same idea applies to any other protocol that's clearly irrelevant to the scenario at hand.

### Step 2: Recognize and skip the handshake

If the traffic involves TCP, the first few packets are very likely the **three-way handshake**, which you can usually recognize and skip past, since handshake packets typically don't carry application data themselves:

1. Client sends `SYN` (synchronize)
2. Server responds `SYN, ACK` (synchronize + acknowledge)
3. Client responds `ACK` (acknowledge)

Real application data transfer happens after the handshake completes.

### Step 3: Look for packets with the PSH flag, or an odd protocol label

Once handshake packets are out of the way, pay special attention to packets with the **PSH** flag set — PSH signals that this packet contains data meant to be pushed up to the application immediately, which is often exactly the kind of packet that carries something interesting.

Also: **an unusual value in the Protocol column is itself a strong clue.** If nearly every packet shows a common protocol (TCP/UDP/HTTP, etc.) but one or two show up as some obscure protocol name, that's usually not because the traffic is actually "speaking" that protocol — it's because that packet happens to use the **port number** conventionally associated with that protocol, and Wireshark is guessing based on the port. Anything that stands out as an outlier is worth clicking into.

### A general mindset

Not every challenge has one single, isolated "flag packet." Sometimes the target data is split across **multiple packets'** payloads and needs to be reassembled in order. The point of filtering and anomaly-hunting isn't a guarantee that the very next click reveals the answer — it's about narrowing "thousands of packets" down to "a handful worth examining by hand."

## Network layering: why layers exist

Modern networks are designed in layers, where each layer has one narrow responsibility. This design has real benefits:

- Changes within one layer have minimal impact on the others (e.g. switching from WiFi to a wired connection doesn't require the application layer to care at all).
- Application developers don't need to worry about exactly how a user is connected to the internet — they can just trust that the lower layers will get the data there.

Here are the five layers, top to bottom (this is the commonly used TCP/IP four/five-layer simplified model — it has fewer layers than the fuller seven-layer OSI model, missing the session and presentation layers, but it's more than enough for day-to-day forensics work):

1. **Application layer:** handles data transfer between applications. **HTTP**, for instance, belongs here and is commonly used to fetch web pages. In Wireshark, a packet's application-layer contents are often just labeled "Data" or by protocol name, and expanding that view shows the actual business content.
2. **Transport layer:** lets multiple applications on the same host each maintain independent connections (distinguished by **port number**), and optionally guarantees reliable delivery. Two protocols dominate this layer:
   - **TCP** (⚠️ correctly expands to **Transmission Control Protocol** — not "Transport Control Protocol," which was a common typo in the source material and has been corrected here): provides reliable transport — anything lost in transit gets resent, guaranteeing complete delivery. Application-layer protocols like HTTP that need everything intact are built on top of TCP, since you want an entire web page to load correctly.
   - **UDP** (User Datagram Protocol): doesn't guarantee reliability — if something's lost, it's just gone, with no retransmission — but it's faster as a result. This fits scenarios like voice calls: a tiny dropped chunk of audio is usually something the listener can mentally fill in, but if a retransmission delayed that chunk instead, it would arrive out of order and confuse the conversation more than the original gap would have.
3. **Network layer:** assigns each device an **IP address** and routes data across multiple routers to its destination. IP addresses provide network-wide reachability; the same device typically gets a different IP address depending on which network it's connected to.
4. **Data link layer:** handles communication between directly connected devices. Typical protocols here are **Ethernet** (wired) and **WiFi**. This layer uses **MAC addresses** — physical addresses burned into network hardware at manufacture time, unlike IP addresses, which change depending on the network you're on.
5. **Physical layer:** handles the actual electrical (or optical, or radio) signals on the wire/air that represent raw bits. This is the lowest, most hardware-adjacent layer.

The point of understanding these five layers is that you can inspect a packet layer by layer — knowing which layer a protocol name or port lives in, versus which layer address information lives in — lets you reason about exactly which field is worth being suspicious of, instead of treating the whole packet as one indivisible black box.

## Related: Chapter 03 (Disk Forensics) deals with "static" data (a disk image); this chapter deals with "in-flight" data (packets moving across a network). The shared approach is the same: understand the layered structure first, then locate exactly where the useful data lives.

---

## Self-Test

1. Why is it recommended to filter out ARP packets and the TCP handshake before looking at anything else, rather than scanning every packet from the start?
2. If a packet's Protocol column shows an unusual protocol name in Wireshark, does that mean the traffic is genuinely speaking that protocol? What's the more likely explanation?
3. What does TCP actually stand for? Why does that make it a better fit for web pages than UDP, while UDP is a better fit for voice calls?

## Cheat Sheet

**Wireshark filtering/triage order:** rule out ARP (`!arp`) → skip the TCP three-way handshake (SYN → SYN,ACK → ACK) → focus on packets with the PSH flag or an unusual Protocol value → remember a flag can be split across multiple packets.

**Five-layer network model (top to bottom):**

| Layer | Key job | Typical protocol/concept |
|---|---|---|
| Application | Data transfer between applications | HTTP |
| Transport | Ports distinguish connections; optional reliability | TCP (Transmission Control Protocol — reliable), UDP (unreliable but fast) |
| Network | Addressing and routing across the internet | IP address |
| Data link | Communication between directly connected devices | Ethernet, WiFi, MAC address |
| Physical | The raw electrical/optical/radio signal itself | — |
