# Broken Authentication, Access Control, File Upload & Sensitive Data Exposure

## Broken Authentication vs. Broken Access Control

These are often mentioned together but ask different questions:

| | Question | Example |
|---|---|---|
| **Broken Authentication** | *Can I prove I'm someone I'm not, or skip proving it at all?* | Logging in without valid credentials; escalating from normal user to admin |
| **Broken Access Control** | *Once I'm authenticated (as whoever), can I reach things I shouldn't?* | A normal, correctly-authenticated user reaching an admin panel |

⚠️ Don't conflate the two: an application can authenticate you perfectly correctly and *still* have broken access control if it fails to check whether *this particular authenticated user* should be allowed to do *this particular thing*.

## Malicious File Upload

Happens when an application doesn't properly validate what's actually being uploaded.
```
shell.php.jpg
```
A classic bypass pattern: a **double extension** designed to slip past a naive check (e.g. one that only looks at the final characters, or only blocks a single exact extension) while still being executed as PHP by a misconfigured server.

**Impact:** uploading and then triggering execution of attacker-controlled code on the server — often one of the most direct paths to full compromise, since it's not just reading/leaking data but running arbitrary logic server-side.

## Command Injection (Cross-Reference)

Covered in depth in file 02 (Injection Vulnerabilities) — included there because it shares the same "untrusted input reaches an interpreter" root cause as SQLi/XSS/HTML Injection, rather than the access-control theme of this file.

## Sensitive Data Exposure

Occurs when sensitive information is accessible to end users in **cleartext**, often sitting quietly in front-end-visible material:
```
HTML source code
JavaScript files
HTML comments
Hidden links/directories
Exposed user information
Debugging parameters
```
⚠️ **Front-end source ≠ back-end source.** Front-end code (HTML/CSS/client-side JS) is delivered to and readable by anyone visiting the page; back-end source normally never leaves the server. Sensitive data exposure specifically concerns things leaking through that *first*, client-visible layer.

### Viewing Page Source
```
Right-click → View Page Source
Ctrl+U
A web proxy (e.g. Burp Suite)
```
⚠️ **Disabling right-click does not prevent viewing source.** It's a purely cosmetic client-side restriction — the actual HTML/JS was already sent to the browser regardless, and can be retrieved through keyboard shortcuts, dev tools, or simply re-requesting the page with `curl`.

### Example
```html
<!-- TODO: remove test credentials test:test -->
```
Developer comments are an easy, common leak vector for test credentials, staging URLs, hidden functionality, or debug flags — and "just for testing" credentials are frequently still valid in production.

### Prevention
- Ship only what the front end actually needs — nothing extra.
- Remove leftover comments and hidden links before deployment.
- Review all client-side code pre-release.
- Classify data, and deliberately control what's allowed to reach the client at all.
- Review JavaScript specifically for embedded secrets.
- Consider minification/obfuscation to raise the bar (not eliminate the risk) for casual discovery.

## Cheat Sheet

```
Broken Authentication  → can you fake/skip proving WHO you are?
Broken Access Control    → once authenticated, can you reach things you SHOULDN'T?
                             (these are independent failure modes — check both)

Malicious File Upload  → weak file-type validation; watch for double extensions (shell.php.jpg)

Sensitive Data Exposure  → secrets sitting in cleartext CLIENT-VISIBLE material:
  HTML/JS source, comments, hidden links, debug params
  View Page Source (Ctrl+U) / proxy — right-click-disable does NOT stop this
  Front-end source is always visible; back-end source normally is not
```
