# IDS Application with Python

A simple **Intrusion Detection System (IDS)** that inspects raw IP packets and alerts when traffic involves a known/monitored IP address. Builds on the raw-socket type introduced in file 01 (`SOCK_RAW`).

## Loading Monitored IPs

```python
import os

if not os.path.exists("iplist.txt"):
    print("Error: 'iplist.txt' file not found.")
    exit()

with open("iplist.txt", "r") as file:
    monitored_ips = [line.strip() for line in file.readlines()]
```
`iplist.txt` here could be the exact file produced by the web-scraping example in file 03 — scrape a threat-intel IP list, save it, then feed it straight into this IDS as the watchlist.

## Capturing Network Packets

```python
import socket

host = socket.gethostbyname(socket.gethostname())

s = socket.socket(socket.AF_INET, socket.SOCK_RAW, socket.IPPROTO_RAW)
s.bind((host, 0))
s.setsockopt(socket.IPPROTO_IP, socket.IP_HDRINCL, 1)

while True:
    packet, addr = s.recvfrom(65565)
```
`SOCK_RAW` bypasses normal transport-layer handling and hands you whole IP packets directly — this generally **requires elevated privileges** (`sudo`).

## Parsing the IPv4 Header

The first 20 bytes of a standard IPv4 packet contain the base header fields, including source/destination addresses:
```python
import struct

ip_header = packet[0:20]
iph = struct.unpack("!BBHHHBBH4s4s", ip_header)
```
`struct.unpack()` converts the raw bytes into a tuple of individual fields, according to a **format string**:

| Format char | Size | Meaning |
|---|--:|---|
| `!` | — | Network byte order (big-endian) |
| `B` | 1 byte | Unsigned 8-bit integer |
| `H` | 2 bytes | Unsigned 16-bit integer |
| `4s` | 4 bytes | 4-byte raw string |

The format string `"!BBHHHBBH4s4s"` matches the actual byte layout of an IPv4 header field-by-field.

## Extracting Header Fields

```python
version_ihl = iph[0]          # first byte: IP version + Internet Header Length (IHL) packed together
ihl = version_ihl & 0xF          # lower 4 bits = IHL
iph_length = ihl * 4               # IHL is in 32-bit WORDS, so ×4 for bytes
```
`&` masks out just the bits you want — here, the lower nibble.

## Extracting IP Addresses

```python
src_addr = socket.inet_ntoa(iph[8])
dst_addr = socket.inet_ntoa(iph[9])
```
`iph[8]`/`iph[9]` are the raw 4-byte source/destination fields (the two `4s` entries); `socket.inet_ntoa()` converts 4 raw bytes into familiar dotted-decimal notation (e.g. `192.168.1.10`).

## Detecting Monitored IPs

```python
if src_addr in monitored_ips:
    print(f"Alert: Incoming connection for blacklisted IP {src_addr}!")

if dst_addr in monitored_ips:
    print(f"Alert: Outgoing connection for blacklisted IP {dst_addr}!")
```
Checked both ways — the watchlisted address might be either the **source** (something reaching in toward you) or the **destination** (your traffic going out toward it).

## Complete Example

```python
import socket
import struct
import os

if not os.path.exists("iplist.txt"):
    print("Error: 'iplist.txt' file not found.")
    exit()

with open("iplist.txt", "r") as file:
    monitored_ips = [line.strip() for line in file.readlines()]

host = socket.gethostbyname(socket.gethostname())

s = socket.socket(socket.AF_INET, socket.SOCK_RAW, socket.IPPROTO_RAW)
s.bind((host, 0))
s.setsockopt(socket.IPPROTO_IP, socket.IP_HDRINCL, 1)

while True:
    packet, addr = s.recvfrom(65565)

    ip_header = packet[0:20]
    iph = struct.unpack("!BBHHHBBH4s4s", ip_header)

    version_ihl = iph[0]
    ihl = version_ihl & 0xF
    iph_length = ihl * 4     # calculated but unused here — a real parser would use it
                              # to know where the IP header ends and the payload begins

    src_addr = socket.inet_ntoa(iph[8])
    dst_addr = socket.inet_ntoa(iph[9])

    if src_addr in monitored_ips:
        print(f"Alert: Incoming connection for blacklisted IP {src_addr}!")

    if dst_addr in monitored_ips:
        print(f"Alert: Outgoing connection for blacklisted IP {dst_addr}!")
```

## Testing

```bash
sudo python3 ids.py
```
Add `8.8.8.8` to `iplist.txt`, then generate traffic to/from that address in another terminal to trigger an alert.

**Core idea:** the IDS doesn't need to understand an entire packet's contents — just the IPv4 header is enough to extract source/destination and compare against a watchlist.

### Resource Considerations

High-traffic systems generate huge packet volumes; processing every single one in Python with raw sockets can consume significant CPU — this toy example doesn't address performance at scale.

## Cheat Sheet

```
socket.socket(AF_INET, SOCK_RAW, IPPROTO_RAW)     raw IP packet capture (needs sudo)
s.setsockopt(IPPROTO_IP, IP_HDRINCL, 1)              include IP headers in what you send/see

struct.unpack("!BBHHHBBH4s4s", ip_header[0:20])       parse the 20-byte IPv4 header
version_ihl & 0xF                                        extract IHL (header length in 32-bit words)
socket.inet_ntoa(raw_4_bytes)                              raw bytes → dotted-decimal IP string

if ip in monitored_ips: alert          check BOTH source and destination

iplist.txt → shared input format with file 03's scraper output
```
