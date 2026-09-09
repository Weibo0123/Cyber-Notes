# URLs & URL Encoding

## URL Structure

A **URL (Uniform Resource Locator)** specifies how and where to access a resource:

```
scheme://user:password@host:port/path?query#fragment
```
Example:
```
http://user:password@tryhackme.com:80/view-room?id=1#task3
```

| Component | Example | Meaning |
|---|---|---|
| Scheme | `http`, `https`, `ftp` | Protocol used to access the resource |
| User info | `admin:password@` | Optional credentials (rarely used directly today) |
| Host | `example.com` | Domain name or IP address of the server |
| Port | `:80` | Network port; HTTP defaults to `80`, HTTPS to `443` |
| Path | `/view-room` | Location of the requested resource |
| Query string | `?id=1` | Parameters sent to the path |
| Fragment | `#task3` | Client-side-only location/info within the page |

Only the **scheme and host** are fundamentally required to identify an HTTP resource — everything else can be defaulted or omitted.

Because default ports are well known, they're usually omitted from the URL you type or see.

## Path, Query, and Fragment

```
/cat.gif?width=256&height=256#2
```
```
Path:      /cat.gif
Query:     width=256&height=256
Fragment:  2
```
- **Query** — usually `key=value` pairs joined by `&`.
- **Fragment** — handled entirely by the client/browser and is **not sent to the server** as part of the HTTP request. (Useful to remember: a server-side log will never show what came after `#`.)

## URL / Percent Encoding

HTTP request lines use spaces as delimiters (`METHOD URI HTTP-Version`), so characters with special meaning in a URL or with no valid literal representation must be **percent-encoded**:
```
%HH     (HH = hex byte value of the character)
```
```
Space → %20        # → %23        / → %2F        ? → %3F        A → %41
! → %21   " → %22   $ → %24   % → %25   & → %26   ' → %27   ( → %28   ) → %29
```
Process: `character → byte/ASCII value → hexadecimal → %HH`.

⚠️ Spaces are sometimes represented as `+` instead of `%20` in specific contexts (classically, form-urlencoded query strings) — the two aren't universally interchangeable, so don't assume one always works where the other is expected.

```bash
curl 'http://example.com/hello%20world'
```
`curl` does **not** automatically encode URLs for you — you must encode special characters yourself. Python's `requests` library, by contrast, automatically encodes appropriate values (e.g. dictionary values passed as `params=`).

## Cheat Sheet

```
scheme://user:pass@host:port/path?query#fragment

Required at minimum: scheme + host
Fragment  → client-side only, never sent to the server
Query      → key=value&key=value, sent to the server

Percent-encoding: % + two hex digits of the byte value
  space→%20   #→%23   /→%2F   ?→%3F   &→%26

curl        → does NOT auto-encode URLs; encode manually
requests    → CAN auto-encode values you pass in (e.g. params=)
```
