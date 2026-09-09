# HTTPS & DNS

## HTTPS

**HTTPS** is HTTP transmitted over **TLS (Transport Layer Security)**. Plain HTTP sends data as cleartext; HTTPS encrypts the communication so intercepted traffic can't normally be read directly, and helps verify the client is talking to the intended server.

```
HTTP  → port 80    →  http://
HTTPS → port 443    →  https://
```

### Flow
```
HTTP request → 301/302 redirect → HTTPS connection → TLS handshake → encrypted HTTP
```
The **TLS handshake** negotiates cryptographic parameters and verifies the server's **certificate** before any encrypted application data flows. After the handshake, ordinary HTTP requests/responses travel inside the encrypted tunnel.

If certificate verification fails, well-behaved clients (like `curl`) refuse to proceed by default:
```bash
curl https://example.com          # normal — verifies the cert
curl -k https://example.com        # -k/--insecure: proceed anyway (disables verification)
```
⚠️ `-k` should only be used deliberately (e.g. a lab environment with a self-signed cert) — it removes the very protection HTTPS is meant to provide.

### HTTPS and DNS
HTTPS encrypts the HTTP *payload*, but the **DNS lookup** that resolves a hostname to an IP can still be unencrypted depending on configuration — meaning "which site did you visit" can leak even over an HTTPS connection. Encrypted DNS or a VPN can mitigate this specific gap.

## DNS — Domain Name System

DNS maps human-readable domain names to IP addresses:
```
tryhackme.com → 104.26.10.229
```

### Domain Hierarchy
```
Subdomain . Second-Level Domain . TLD
   admin  .    tryhackme         . com
```
| Part | Notes |
|---|---|
| TLD | Rightmost label; **gTLD** (`.com`, `.org`, `.edu`) or **ccTLD** (`.ca`, `.uk`) |
| Second-Level Domain | Immediately left of the TLD; max 63 chars, `a-z 0-9 -`, can't start/end with `-` or contain `--` |
| Subdomain | Left of the second-level domain; same naming rules; multiple levels allowed (`jupiter.servers.tryhackme.com`) |

Full domain name limit: **253 characters**.

### DNS Record Types
| Record | Maps to |
|---|---|
| `A` | IPv4 address |
| `AAAA` | IPv6 address |
| `CNAME` | Another domain name (needs a further lookup to resolve to an IP) |
| `MX` | Mail servers responsible for the domain, with a priority order |
| `TXT` | Arbitrary text — SPF (authorized senders), DMARC (email auth policy), domain-ownership verification |

### DNS Resolution Process
```
1. Local DNS cache        → answer already known? use it.
2. Recursive DNS Server     → usually ISP-provided; has its own cache; else queries onward
3. Root DNS Server           → points to the right TLD server
4. TLD Server                  → points to the Authoritative DNS Server for the domain
5. Authoritative DNS Server      → holds the actual records, returns the answer
```
The Recursive server then caches the result before returning it to the client.

A **Nameserver** is any DNS server providing DNS info for a domain (a domain can have several, for redundancy); the **Authoritative** one specifically holds that domain's real records.

### TTL (Time To Live)
How long (in seconds) a DNS answer may stay cached before it must be re-queried. Shorter TTL = fresher data but more lookups; longer TTL = less lookup traffic but slower propagation of changes.

## Cheat Sheet

```
HTTPS = HTTP + TLS      port 443, https://
curl -k                   skip certificate verification (use deliberately, not by default)
HTTPS encrypts payload — DNS lookups can still leak which site you visited

DNS: domain name → IP address
A → IPv4   AAAA → IPv6   CNAME → another domain   MX → mail servers   TXT → arbitrary text

Resolution order: local cache → recursive resolver → root → TLD → authoritative
TTL = how long a DNS answer can be cached (seconds)
```
