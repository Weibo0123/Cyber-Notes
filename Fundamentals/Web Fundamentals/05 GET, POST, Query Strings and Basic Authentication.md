# GET, POST, Query Strings & Basic Authentication

## GET Parameters — Query Strings

`GET` parameters travel in the URL's **query string**, after `?`:
```
/search.php?search=hello
```
Multiple parameters are joined with `&`:
```
/attempt?secret_key=gqwbqjjz&auth=kcpohage&access=ipdztkff
```
In Flask, each is read independently:
```python
flask.request.args.get("secret_key")
```

### Query Strings and the Shell — Quoting Matters

`&` has a special meaning in most Unix shells: it backgrounds the preceding command. When a URL has multiple query parameters, **quote the whole URL**:
```bash
curl 'http://example.com/gate?key1=value1&key2=value2'     # correct
curl http://example.com/gate?key1=value1&key2=value2         # WRONG — shell splits this
```
A single parameter is usually safe unquoted, but quoting is always the simple, safe default.

### Query Strings with `netcat`

With `nc`, the query string is simply part of the request line — `&` is just a literal character in that line, not a header separator:
```http
GET /attempt?secret_key=gqwbqjjz&auth=kcpohage HTTP/1.1
Host: challenge.localhost:80
```

## POST — Submitting Data

`POST` places data in the **request body** instead of the URL.

**Advantages over GET:**
- Not logged directly in the URL
- Doesn't need URL-encoding for binary data
- Can carry far more data than a URL practically allows

### Form-Encoded POST with `curl`
```bash
curl -X POST -d 'username=admin&password=admin' http://example.com/
```
- `-X POST` sets the method explicitly
- `-d`/`--data` sends the given body — commonly formatted as `application/x-www-form-urlencoded`

### JSON POST
Applications commonly accept JSON instead of form encoding:
```bash
curl -X POST \
     -d '{"search":"london"}' \
     -H 'Content-Type: application/json' \
     http://example.com/search.php
```
The server interprets the body according to the `Content-Type` header — this is *how* it knows whether to parse form-encoded pairs or JSON.

### The Core GET vs. POST Difference
```
GET  /search.php?search=london HTTP/1.1

POST /search.php HTTP/1.1
Content-Type: application/json

{"search":"london"}
```
Same logical data, different *placement*: URL query string vs. request body.

### POST with `fetch()` (JavaScript)
```javascript
fetch("/search.php", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ search: "london" })
});
```
`JSON.stringify()` converts a JS object into the JSON string used as the body.

## HTTP Basic Authentication

A server-level (not application-form-level) authentication scheme:
```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Basic realm="Access denied"
```
```bash
curl -u username:password http://example.com/
curl 'http://username:password@example.com/'
```
Credentials travel in the `Authorization` header:
```http
Authorization: Basic <base64(username:password)>
```
⚠️ **Base64 is encoding, not encryption** — `Authorization: Basic ...` is trivially reversible and offers no confidentiality by itself; it relies entirely on HTTPS for protection in transit.

## Inspecting Requests via Browser DevTools

Network tab → select a request → inspect Method, URL/path, Status, Request/Response Headers, Request body/payload (raw view shows unprocessed data). Right-click a request for:
```
Copy → Copy as cURL     (reproduce from a terminal)
Copy → Copy as Fetch     (reproduce with JS fetch())
```

## Cheat Sheet

```
GET     data in URL query string:  ?key=value&key2=value2
POST    data in request body

curl -X POST -d 'k=v&k2=v2' URL                     form-encoded POST
curl -X POST -d '{"k":"v"}' -H 'Content-Type: application/json' URL   JSON POST

Quote URLs with multiple query params: 'url?a=1&b=2'   (& backgrounds commands otherwise)

curl -u user:pass URL          HTTP Basic Auth
Authorization: Basic <base64(user:pass)>    — encoded, NOT encrypted

DevTools Network tab → Copy as cURL / Copy as Fetch → reproduce any request
```
