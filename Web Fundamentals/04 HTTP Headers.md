# HTTP Headers

Headers are `name: value` pairs that carry **metadata** about an HTTP message — separate from the actual data (the body).

## Header Categories

| Category | Direction | Purpose |
|---|---|---|
| General | Both | Describe the message itself |
| Entity/Content | Both | Describe the body's content |
| Request | Client → Server | Info about the client/request |
| Response | Server → Client | Info about the response |
| Security | Server → Client | Tell the browser which policies to enforce |

### General Headers
```
Date         date/time the message was generated
Connection    keep-alive (stay open) or close
```

### Entity / Content Headers
```
Content-Type       type of the body, e.g. text/html, application/json
Content-Length      size of the body, in bytes
Content-Encoding     compression used, e.g. gzip
Boundary              separator for multipart content
```
(`Media-Type` is the older/general term; in modern HTTP this is represented by `Content-Type`.)

### Request Headers (Client → Server)
```
Host           which hostname/virtual host the client wants — REQUIRED for HTTP/1.1
User-Agent      identifies the client software
Referer          the page that linked to this request
Accept            media types the client can accept
Cookie             stored cookies sent back to the server
Authorization       credentials or tokens
```

### Response Headers (Server → Client)
```
Server            identifies the server software
Set-Cookie        tells the client to store a cookie
WWW-Authenticate    authentication scheme the server requires
Location             URL/path for a redirect
Cache-Control          how long the browser should cache the response
```

### Security Headers (Response)
```
Content-Security-Policy      restricts what resources can load/execute
Strict-Transport-Security     force HTTPS instead of HTTP
Referrer-Policy                 controls what's sent in the Referer header
```

## The `Host` Header

`Host` tells the server *which* website/virtual host to serve, since one physical server can host many domains:
```http
GET / HTTP/1.1
Host: pwn.college
```
It is strictly a **request** header, never a response header.

## Custom Headers

Applications can define their own headers, conventionally prefixed `X-`:
```python
response.headers["X-Flag"] = value    # Flask example
```
These are just ordinary headers from HTTP's perspective — the `X-` prefix is a naming convention, not special protocol behavior.

## Setting/Viewing Headers

**With `curl`:**
```bash
curl -I <URL>                          # HEAD request → response headers only
curl -i <URL>                           # headers AND body
curl -v <URL>                            # full request+response detail
curl -H 'Header: value' <URL>             # add/override a header
curl -H 'Host: www.example.com' <URL>      # e.g. override the Host header
curl -A 'Mozilla/5.0' <URL>                  # shortcut specifically for User-Agent
```
`-H` can be repeated for multiple headers.

**With Python `requests`:**
```python
import requests
r = requests.get(url, headers={"Host": "alf.nu:80"})
```

**With Browser DevTools:** Network tab → select a request → Headers (shows request headers, response headers, cookies; "Raw" view shows the original HTTP-format text).

## Cheat Sheet

```
curl -I    HEAD only, response headers
curl -i     headers + body
curl -v      full verbose exchange
curl -H 'Name: value'   set/override a header
curl -A 'agent'           shortcut for User-Agent

Host          → request only, which virtual host to serve
User-Agent      → request, client identity
Cookie/Set-Cookie → request/response pair for state
Location          → response, redirect target
Content-Type       → both, format of the body
X-*                  → convention for custom/app-specific headers
```
