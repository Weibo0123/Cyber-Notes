# Redirects, Cookies & State

## HTTP Redirects

A server tells the client a resource has moved using a `3xx` status code plus a `Location` header:
```http
HTTP/1.1 302 FOUND
Location: /new-path
```
The client is then expected to issue a new request to that path.

```bash
curl -L <URL>      # -L / --location: follow redirects automatically, including chains of several
```
With `netcat`, redirects are **not** followed automatically — you must read the `Location` header yourself and make the follow-up request. Python's `requests` **follows redirects by default**:
```python
r = requests.get(url)                          # follows automatically
r = requests.get(url, allow_redirects=False)    # get the raw redirect response instead
```

## HTTP Is Stateless — Cookies Provide State

Each HTTP request is independent at the protocol level — the server has no built-in memory that three requests came from the same user. **Cookies** are the mechanism used to bolt state onto this stateless protocol:
```http
Set-Cookie: session=abc123      (Server → Client, stored by the browser)
Cookie: session=abc123           (Client → Server, sent on later requests)
```
Common uses: login sessions, preferences, "have you visited before" tracking. Authentication cookies typically hold a **token**, not a plaintext password.

## Two Cookie State Models

**Direct state** — the cookie *is* the data:
```
Cookie: user=Connor
```
⚠️ The client controls the HTTP request — if the server blindly trusts a client-supplied identity value like this, that's a security flaw. Nothing stops someone from sending `Cookie: user=admin` directly.

**Session ID** (the common, safer design) — the cookie is just a random lookup key; the server holds the real state:
```
Set-Cookie: session=8f3a...d91
```
```
Session ID     User
8f3a...d91     Connor
a71c...42e     Alice
```
The session identifier must be sufficiently random/unpredictable, or an attacker could guess or brute-force a valid session.

**Core security principle:** the client controls the HTTP request. A server must not assume requests only come from a normal browser following the intended UI flow — authentication and authorization must always be validated server-side, not inferred from whatever the client happens to send.

## Working with Cookies from Tools

**`curl`:**
```bash
curl -c cookies.txt <URL>            # -c/--cookie-jar: SAVE cookies received to a file
curl -b cookies.txt <URL>             # -b/--cookie: SEND cookies from a file (or inline)
curl -b 'name=value' <URL>             # send one cookie directly
curl -c cookies.txt -b cookies.txt <URL> # do both: send existing + save any new ones
```

**Python `requests`:**
```python
s = requests.Session()
r = s.get("http://127.0.0.1/")   # cookie received & stored automatically
r = s.get("http://127.0.0.1/")    # same session, cookie sent automatically
```
`requests.Session()` persists cookies across calls; without it, each `requests.get()` is independent and cookies aren't carried forward. `s.cookies` lets you inspect what's stored.

**`netcat` — fully manual:**
```
1. Send the first request
2. Read the Set-Cookie header in the response
3. Copy that value into a Cookie header yourself
4. Send it in the next request
```
Unlike `curl`/`requests`, `nc` has no automatic cookie handling whatsoever.

## Cheat Sheet

```
3xx + Location header = redirect
curl -L                  follow redirects (curl)
requests.get()             follows by default; allow_redirects=False to stop
nc                           never auto-follows — read Location & repeat yourself

Set-Cookie: (response)   →  server sets a cookie
Cookie: (request)          →  client sends it back

Direct state cookie   → cookie IS the data (client-controlled — don't trust blindly)
Session ID cookie      → cookie is a random key; real state lives server-side

curl -c file    save cookies      curl -b file   send cookies
requests.Session()                 auto-manages cookies across calls
nc                                    fully manual cookie handling
```
