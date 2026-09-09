# HTTP Requests, Responses, Methods & Status Codes

## HTTP Overview

**HTTP (HyperText Transfer Protocol)** is the application-layer protocol used for client-server communication on the Web, following a **request/response** model. It defines request/response structure, methods, status codes, headers, and message bodies. HTTP/1.0 was specified by **RFC 1945** (RFC = "Request for Comments," the standard document format for Internet protocols).

HTTP transfers resources such as HTML, images, video, CSS, JavaScript, JSON, and PDFs. HTTP/1.x is text-based; HTTP/2 uses a binary representation instead.

## Request Structure

```http
GET /greet HTTP/1.0
Host: hello.example.com
```
```
Request Line:  METHOD  Request-URI  HTTP-Version
Headers:       metadata, one per line
(blank line)   marks the end of headers
Body:          optional data (e.g. for POST)
```

## Response Structure

```http
HTTP/1.0 200 OK
Content-Type: text/html
Content-Length: 98

<html>...</html>
```
```
Status Line:   HTTP-Version  Status-Code  Reason-Phrase
Headers:       metadata about the response
(blank line)
Body:          the actual returned data
```

## HTTP Methods

| Method | Purpose | Idempotent? |
|---|---|---|
| `GET` | Retrieve a resource | Yes |
| `HEAD` | Like GET, but no response body — just headers/metadata | Yes |
| `POST` | Submit data; may create a resource or trigger an action | **No** |
| `PUT` | Create or fully replace a resource | Yes |
| `DELETE` | Delete a resource | Yes |

An **idempotent** operation produces the same end result no matter how many times it's repeated. `GET` doesn't have to correspond to an actual file — the response can be entirely dynamically generated. The exact behavior of any method (especially `POST`) depends on the server-side application; HTTP defines the *protocol*, not the *business logic*.

## Status Codes

| Range | Class | Meaning |
|---|---|---|
| `1xx` | Informational | Request being processed |
| `2xx` | Success | Request succeeded |
| `3xx` | Redirection | Resource is located elsewhere |
| `4xx` | Client Error | Problem with the client's request |
| `5xx` | Server Error | Problem on the server |

Common codes:
```
200 OK                       Success
201 Created                   New resource created
301 Moved Permanently          Permanent redirect
302 Found                       Temporary redirect
400 Bad Request                  Invalid/incomplete request
401 Unauthorized                  Authentication required or failed
403 Forbidden                      Client lacks permission (identity known, access denied)
404 Not Found                       Resource doesn't exist
405 Method Not Allowed                Method not valid for this resource
500 Internal Server Error              Server-side error
502 Bad Gateway                          Upstream/gateway got an invalid response
503 Service Unavailable                    Overloaded / under maintenance
504 Gateway Timeout                          Upstream/gateway didn't respond in time
```
⚠️ **401 vs. 403 — a commonly confused pair:** `401` means authentication is required or has failed (the server doesn't yet know who you are, or your credentials were rejected); `403` means the server knows the request but denies access regardless (you're identified, but not permitted).

## Cheat Sheet

```
Request:   METHOD Path HTTP/Version
           Headers...
           (blank line)
           [Body]

Response:  HTTP/Version Status-Code Reason
           Headers...
           (blank line)
           [Body]

GET (retrieve) HEAD (headers only) POST (submit/act, not idempotent)
PUT (create/replace) DELETE (remove)   — all except POST are idempotent

1xx info  2xx success  3xx redirect  4xx client error  5xx server error
401 = not authenticated   403 = authenticated but forbidden
```
