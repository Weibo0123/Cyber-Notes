# Injection Vulnerabilities: HTML Injection, XSS, SQLi & Command Injection

All four share the same root pattern: **user-controlled input ends up interpreted as code/commands by something that trusts it too much.** They differ in *what* interprets it.

```
HTML Injection   → the browser's HTML parser
XSS               → the browser's JavaScript engine
SQL Injection      → the database's query engine
Command Injection   → the operating system's shell
```

## HTML Injection

Occurs when unfiltered user input is displayed directly on a page and the browser parses it as HTML — because HTML rendering doesn't distinguish "text I should display" from "markup I should execute," anything inserted without sanitization is fair game to be treated as real markup.

```javascript
document.getElementById("output").innerHTML = "Your name is " + input;
```
Because `innerHTML` interprets its argument as HTML, attacker-supplied `input` becomes real page markup — e.g. injecting a `<style>` block to deface the page, or a fake login form to phish credentials.

**Impact:** page defacement, unwanted content/ads, fake UI elements, and — critically — it's often the *stepping stone* to XSS if script-related tags/attributes also make it through.

**Prevention:** validate on both front end and back end; sanitize before rendering; if HTML rendering isn't actually needed, treat input as plain text (e.g. `textContent` instead of `innerHTML`).

## Cross-Site Scripting (XSS)

The same class of bug, but the payload is **JavaScript**, not just markup — meaning the impact goes from "the page looks wrong" to "arbitrary code runs in the victim's browser session."

| Type | Where it happens |
|---|---|
| **Reflected** | Input is processed and immediately echoed back in the response (e.g. search results, error messages) |
| **Stored** | Input is saved server-side and executed whenever that stored data is later displayed (e.g. a comment, a post) |
| **DOM-based** | Client-side JavaScript itself writes untrusted input directly into the DOM |

```html
#"><img src=/ onerror=alert(document.cookie)>
```
This breaks out of the surrounding attribute/tag context and adds an `<img>` whose `onerror` handler fires (since `src=/` fails to load), running arbitrary JS — here, reading `document.cookie`.

**Impact:** access to whatever data JavaScript can reach on that page (including cookies, if not protected — see the `HttpOnly` flag), performing actions *as* the victim, modifying the page shown to them, pivoting to attack other users.

## SQL Injection (SQLi)

User input flows unescaped into a SQL query string:
```php
$searchInput = $_POST['findUser'];
$query = "select * from users where name like '%$searchInput%'";
```
If `$searchInput` isn't validated/escaped, an attacker can supply SQL syntax that changes the query's actual logic — not just its intended data value.

**Impact:** bypass authentication, extract arbitrary database contents, modify data, and in severe cases pivot to compromising the underlying database server itself.

## Command Injection

User input flows unescaped into an **operating system command**:
```
<user input> → OS command → unintended command execution
```
Usually caused by insufficient sanitization/filtering/validation before input reaches a shell command. If successful, an attacker can run arbitrary commands with whatever privileges the vulnerable process has.

## Comparison Table

| Vulnerability | Injection target | Typical worst-case impact |
|---|---|---|
| HTML Injection | HTML content | Defacement, phishing UI, XSS stepping stone |
| XSS | JavaScript execution | Full client-side compromise of the victim's session |
| SQL Injection | SQL queries | Database read/write, auth bypass, server compromise |
| Command Injection | OS commands | Arbitrary command execution on the server |

## General Prevention (applies across all four)

- **Sanitize** — strip/neutralize special or non-standard characters before storing or rendering input.
- **Validate** — check that input actually matches the expected format (e.g. an email field should look like an email).
- Apply this to **both input and output**, not just one side.
- Prefer safe APIs by construction (parameterized queries for SQL, `textContent` over `innerHTML`, avoiding shell string concatenation for OS commands) over trying to sanitize every dangerous character by hand.

## Cheat Sheet

```
HTML Injection      → browser parses injected HTML       (innerHTML w/ raw input)
XSS                    → browser executes injected JS      (Reflected / Stored / DOM)
SQL Injection            → DB executes injected SQL           (string-built queries)
Command Injection          → OS executes injected commands      (shell string-building)

Shared cause: untrusted input reaches an interpreter that treats it as code, not data
Shared fix:    sanitize + validate, BOTH input and output; prefer safe APIs over hand-filtering
```
