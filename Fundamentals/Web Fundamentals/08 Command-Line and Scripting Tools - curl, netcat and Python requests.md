# Command-Line & Scripting Tools: `curl`, `netcat` & Python `requests`

Three ways to send HTTP requests without a browser — from most convenient to most "raw":
```
curl (purpose-built HTTP client) → requests (programmatic) → netcat (manual, raw TCP)
```

## `curl`

By default, `curl` makes a `GET` request and prints the response body:
```bash
curl http://example.com/
```

| Option | Meaning |
|---|---|
| `-v` / `--verbose` | Show full request+response detail, including headers |
| `-i` / `--include` | Include response headers in the output, along with the body |
| `-I` | Send a `HEAD` request; show only response headers |
| `-H 'Name: value'` | Add/override a request header (repeatable) |
| `-A 'agent'` | Shortcut for setting `User-Agent` |
| `-X METHOD` | Explicitly set the HTTP method (`POST`, `PUT`, `DELETE`, `HEAD`, ...) |
| `-d 'data'` | Send request body data (commonly form-encoded POST) |
| `-u user:pass` | HTTP Basic Authentication |
| `-L` / `--location` | Follow redirects |
| `-c file` / `-b file` | Save / send cookies |
| `-o file` | Write the response body to a file |
| `-O` | Save using the remote filename |
| `-s` / `--silent` | Suppress progress meter and non-error output |
| `-k` / `--insecure` | Skip TLS certificate verification |

⚠️ *Correction:* one of the source files listed `-i`'s long form as `--show-headers` — the actual long option is **`--include`**. `-I` (capital, a separate flag) sends a `HEAD` request and shows headers only, which is easy to confuse with `-i`.

**Verbose output symbols** (`curl -v`):
```
>   data sent BY the client (request line/headers)
<   data received FROM the server (status line/headers)
```

**Documentation:**
```bash
curl -h                # commonly used options
curl --help all         # full option list
curl --help http          # HTTP-specific options
man curl                    # full manual
```

## `netcat` (`nc`) — Raw TCP, Manual HTTP

`netcat` opens a raw TCP connection; since HTTP runs over TCP, you can type a request by hand:
```bash
nc example.com 80
```
```http
GET / HTTP/1.1
Host: example.com

```
⚠️ The **blank line after the headers is mandatory** — it's what tells the server the headers are finished (and, for a bodyless request, that the whole request is complete).

`netcat` has none of `curl`'s conveniences: no automatic redirect following, no automatic cookie handling, no automatic anything — you read the raw response and manually construct whatever comes next. This makes it a good tool for *understanding* HTTP at the byte level, precisely because nothing is done for you.

`netcat` isn't HTTP-specific — it can talk to any TCP-based service.

## Python `requests`

```python
import requests

r = requests.get("http://example.com")
r = requests.post("http://example.com", data={"key": "value"})

r.text                 # response body, as decoded text
r.headers               # response headers (dict-like; iterable with .items())
requests.get(url, headers={"User-Agent": "Connor"})
requests.get(url, headers={"Host": "alf.nu:80"})   # override Host too — it's just a header
```
`requests` is convenient because it handles connection details, redirect-following (by default), and can auto-encode query parameters — none of which `curl` or `netcat` do for you automatically.

## Choosing the Right Tool

| Need | Best tool |
|---|---|
| Quick manual request/inspection | `curl` |
| Precise control over raw bytes on the wire | `netcat` |
| Scripted, repeated, or logic-driven requests | Python `requests` |

## Cheat Sheet

```
curl URL                   GET, print body
curl -v URL                  full request/response detail
curl -i URL                    headers + body   (-I = HEAD, headers only)
curl -H 'K: V' URL               custom header
curl -X POST -d 'k=v' URL         POST with data
curl -L URL                         follow redirects
curl -c/-b file URL                   save/send cookies
curl -u user:pass URL                   Basic Auth
curl -k URL                               skip TLS verification

nc host port                 raw TCP; type the HTTP request manually
                              (blank line after headers is REQUIRED)

requests.get(url) / .post(url, data=...)
r.text / r.headers
requests.get(url, headers={...})
requests.Session()             persists cookies automatically
```
