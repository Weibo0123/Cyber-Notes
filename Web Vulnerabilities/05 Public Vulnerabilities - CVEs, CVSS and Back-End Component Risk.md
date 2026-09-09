# Public Vulnerabilities: CVEs, CVSS & Back-End Component Risk

## What Are "Public Vulnerabilities"?

Vulnerabilities in publicly accessible web applications (or their underlying components) that are potentially remotely exploitable. Common origins: coding mistakes, running an outdated/vulnerable application version, vulnerable plugins or third-party components, server/database vulnerabilities, or plain misconfiguration.

## CVE — Common Vulnerabilities and Exposures

A **CVE** is a public identifier assigned to a specific, disclosed vulnerability. Because widely-used software is scrutinized by many independent researchers, the typical lifecycle is:
```
Discovered → Reported & assigned a CVE → Scored for severity → Patched → Published (often with a PoC/exploit)
```

### Identifying a Target's Vulnerabilities

1. Identify the **exact application and version** first — check application source, version files, its repository, or interface/version info exposed by the app itself.
2. Search for known vulnerabilities against that specific version: Google, Exploit-DB, Rapid7's DB, Vulnerability Lab.
3. Prioritize by impact — especially **Remote Code Execution (RCE)** and anything scoring roughly **8–10** on CVSS.
4. Don't stop at the main application — check **plugins/extensions** too, since they carry their own independent vulnerability histories.

## CVSS — Common Vulnerability Scoring System

A standardized way to express how severe a vulnerability is, built from three metric groups:

| Metric Group | Captures |
|---|---|
| **Base** | The vulnerability's inherent characteristics (always the same regardless of context) |
| **Temporal** | Factors that change over time (e.g. exploit maturity, availability of a patch) |
| **Environmental** | Impact specific to a particular organization's deployment |

The **Base Score** ranges `0.0`–`10.0` and is what you'll see quoted most often (e.g. on the NVD).

| CVSS v2 | Score | CVSS v3 | Score |
|---|---|---|---|
| Low | 0.0–3.9 | None | 0.0 |
| Medium | 4.0–6.9 | Low | 0.1–3.9 |
| High | 7.0–10.0 | Medium | 4.0–6.9 |
| | | High | 7.0–8.9 |
| | | Critical | 9.0–10.0 |

⚠️ Note v2 and v3 use **different bands** — a "High" in v2 (7.0–10.0) spans what v3 splits into High *and* Critical. Always check which version a quoted score refers to before comparing across sources.

The **NVD (National Vulnerability Database)** publishes Base Scores for disclosed CVEs; Temporal and Environmental adjustments are typically layered on separately, since they depend on context the NVD itself doesn't have.

## Back-End Component Vulnerabilities

Assessing "the web application" for known vulnerabilities should extend to its underlying stack, not just the application code itself:
- **Web servers** are especially significant since they're directly internet-facing — e.g. **Shellshock**, a historical Bash vulnerability, was reachable through web-facing CGI scripts and could allow remote command execution.
- Vulnerabilities in *internal* back-end servers or databases more often require some prior local/network foothold — but can still be critical once reached, enabling privilege escalation, back-end server compromise, or lateral movement to other internal systems.

> **Key idea:** check the web application, its web server, its plugins, its database, and every other back-end component it depends on — a "secure" application built on a vulnerable stack is not actually secure.

## Cheat Sheet

```
CVE   = public ID for a specific disclosed vulnerability
CVSS  = standardized severity score (Base / Temporal / Environmental)

CVSS v2: Low 0–3.9 / Medium 4–6.9 / High 7–10
CVSS v3: None 0 / Low 0.1–3.9 / Medium 4–6.9 / High 7–8.9 / Critical 9–10
(v2 and v3 bands differ — check which version before comparing scores)

Workflow: identify exact app+version → search Exploit-DB/Rapid7/Google →
          prioritize RCE + high CVSS → also check plugins & back-end stack

Web-facing components (web server) = highest exposure, check first
Internal components (DB, internal servers) = need a foothold, but still critical once reached
```
