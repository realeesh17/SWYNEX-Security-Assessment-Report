# SWYNEX Task 4 — Final Security Assessment Report

| | |
|---|---|
| **Author** | Rakesh Babriya |
| **Assessment period** | October 2026 |
| **Assessment type** | Authorized security assessment of local training environments |
| **Classification** | Educational / Lab use |

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Scope](#2-scope)
3. [Methodology](#3-methodology)
4. [Findings Register](#4-findings-register)
5. [Detailed Findings](#5-detailed-findings)
6. [Network Security Analysis](#6-network-security-analysis)
7. [Remediation Roadmap](#7-remediation-roadmap)
8. [Evidence Index](#8-evidence-index)
9. [Limitations](#9-limitations)
10. [Conclusion](#10-conclusion)

---

## 1. Executive Summary

This report combines the results of three security exercises: **Security Fundamentals Assessment**, **Network Security Analysis** and **Web Security Review**.

The work covered two local training applications (OWASP Juice Shop and DVWA) and local network traffic. The main results are:

- **7 web findings** were documented, mostly missing or weak security headers, cookie settings and exposed directories.
- **Network analysis** (DNS, HTTP, TLS, TCP) was recorded as observations. It does not by itself show an exploitable weakness.
- Each finding has an **evidence screenshot**, a **remediation** and a **verification step**.

> **Overall picture:** the issues found are mainly configuration weaknesses. They are usually quick to fix, but they weaken the application's defence if left open.

---

## 2. Scope

| Target | Address | Type |
|--------|---------|------|
| OWASP Juice Shop | `127.0.0.1:3000` | Local training web app |
| DVWA | `127.0.0.1:8080` | Local training web app |
| Lab network | Local only | Reconnaissance and packet analysis |

**Out of scope:** all external, public and production systems.

---

## 3. Methodology

1. Prepare and verify the local lab (Kali Linux, VMware, Docker).
2. Run network reconnaissance with **Nmap** where relevant.
3. Capture and inspect traffic with **Wireshark / tshark**.
4. Review HTTP responses and security headers with **cURL** and **OWASP ZAP**.
5. Inspect directory listing and session-cookie attributes in DVWA.
6. Capture screenshots and note each observation.
7. Rate the risk, then write the remediation and verification steps.

**Status labels used in this report**

| Label | Meaning |
|-------|---------|
| **Confirmed** | Directly seen in evidence |
| **Potential Risk** | Possible impact, not fully demonstrated |
| **Observation** | Normal behaviour recorded for analysis; not a vulnerability |

---

## 4. Findings Register

> Replace each `TBC` with the rating from your original task report.

| ID | Title | Target | Severity | Status | Evidence |
|----|-------|--------|----------|--------|----------|
| F-01 | Anti-clickjacking protection | Juice Shop | TBC | TBC | `01-juice-shop-anti-clickjacking.png` |
| F-02 | Content Security Policy (CSP) | Juice Shop | TBC | TBC | `02-juice-shop-csp.png` |
| F-03 | Content-type sniffing protection | Juice Shop | TBC | TBC | `03-juice-shop-content-type-options.png` |
| F-04 | Directory listing enabled | DVWA | TBC | TBC | `08-dvwa-directory-listing.png` |
| F-05 | Missing security headers | DVWA | TBC | TBC | `09-dvwa-security-headers.png` |
| F-06 | Session cookie attributes | DVWA | TBC | TBC | `10-dvwa-cookie-attributes.png` |
| F-07 | Exposed `includes/` directory | DVWA | TBC | TBC | `11-dvwa-includes-directory.png` |

---

## 5. Detailed Findings

### F-01 — Anti-clickjacking protection (Juice Shop)

| Field | Detail |
|-------|--------|
| **Affected component** | Juice Shop HTTP responses |
| **Severity / Status** | TBC |
| **Observation** | The response was reviewed for clickjacking protection (`X-Frame-Options` or CSP `frame-ancestors`). *Add exact result from your ZAP/cURL output.* |
| **Potential impact** | A page without this protection can be loaded inside a hidden frame on another site, tricking users into clicking things they cannot see. |
| **Evidence** | `evidence/01-juice-shop-anti-clickjacking.png` |
| **Remediation** | Send `X-Frame-Options: DENY` or `Content-Security-Policy: frame-ancestors 'none'`. |
| **Verification** | Run `curl -I http://127.0.0.1:3000` and confirm the header is present. |

### F-02 — Content Security Policy (Juice Shop)

| Field | Detail |
|-------|--------|
| **Affected component** | Juice Shop HTTP responses |
| **Severity / Status** | TBC |
| **Observation** | The CSP header was checked. *State whether it was missing or weak.* |
| **Potential impact** | Without a strong CSP, injected scripts are easier to run (for example through XSS). |
| **Evidence** | `evidence/02-juice-shop-csp.png` |
| **Remediation** | Add a restrictive `Content-Security-Policy` that allows only trusted sources. |
| **Verification** | Check the header with cURL, then re-scan with ZAP. |

### F-03 — Content-type sniffing protection (Juice Shop)

| Field | Detail |
|-------|--------|
| **Affected component** | Juice Shop HTTP responses |
| **Severity / Status** | TBC |
| **Observation** | The `X-Content-Type-Options` header was checked. *State result.* |
| **Potential impact** | Browsers may guess a file's type and run content in an unsafe way. |
| **Evidence** | `evidence/03-juice-shop-content-type-options.png` |
| **Remediation** | Send `X-Content-Type-Options: nosniff`. |
| **Verification** | Confirm the header in `curl -I` output. |

### F-04 — Directory listing enabled (DVWA)

| Field | Detail |
|-------|--------|
| **Affected component** | DVWA web server |
| **Severity / Status** | TBC |
| **Observation** | A directory showed its file list instead of a normal page. |
| **Potential impact** | Attackers can see file names, structure and possibly sensitive files. |
| **Evidence** | `evidence/08-dvwa-directory-listing.png` |
| **Remediation** | Disable directory indexing (for Apache: `Options -Indexes`). |
| **Verification** | Request the directory again and confirm a 403 or 404 response. |

### F-05 — Missing security headers (DVWA)

| Field | Detail |
|-------|--------|
| **Affected component** | DVWA HTTP responses |
| **Severity / Status** | TBC |
| **Observation** | Common security headers were absent or incomplete. *List which ones.* |
| **Potential impact** | Weaker browser-side protection against clickjacking, XSS and content sniffing. |
| **Evidence** | `evidence/09-dvwa-security-headers.png` |
| **Remediation** | Add `X-Frame-Options`, `X-Content-Type-Options` and a suitable CSP. |
| **Verification** | Compare `curl -I` output before and after the change. |

### F-06 — Session cookie attributes (DVWA)

| Field | Detail |
|-------|--------|
| **Affected component** | DVWA session cookie |
| **Severity / Status** | TBC |
| **Observation** | Cookie flags were inspected. *State which of `HttpOnly`, `Secure`, `SameSite` were missing.* |
| **Potential impact** | Missing flags make the cookie easier to steal or misuse. |
| **Evidence** | `evidence/10-dvwa-cookie-attributes.png` |
| **Remediation** | Set `HttpOnly`, `Secure` (over HTTPS) and `SameSite=Lax` or `Strict`. |
| **Verification** | Inspect `Set-Cookie` in the response headers or browser dev tools. |

> ⚠️ Blur the cookie **value** in the screenshot. Only the attribute names should be visible.

### F-07 — Exposed `includes/` directory (DVWA)

| Field | Detail |
|-------|--------|
| **Affected component** | DVWA `includes/` path |
| **Severity / Status** | TBC |
| **Observation** | The `includes/` directory was reachable from the browser. |
| **Potential impact** | Internal application files may be seen, which helps an attacker plan further attacks. |
| **Evidence** | `evidence/11-dvwa-includes-directory.png` |
| **Remediation** | Block direct web access to internal directories. |
| **Verification** | Request the path again and confirm access is denied. |

---

## 6. Network Security Analysis

Traffic was analysed for four protocols. These are **observations**, not vulnerabilities, unless the evidence clearly shows a weakness.

| Protocol | What was examined | Evidence |
|----------|-------------------|----------|
| **DNS** | Queries and responses | `04-network-dns-analysis.png` |
| **HTTP** | Requests, responses and plain-text exposure | `05-network-http-analysis.png` |
| **TLS** | Handshake and encryption details | `06-network-tls-analysis.png` |
| **TCP** | Connection setup and flow | `07-network-tcp-analysis.png` |

> Empty or unusable PCAPNG files are **not** used as evidence in this report.

---

## 7. Remediation Roadmap

| Priority | Action | Fixes |
|----------|--------|-------|
| 1 | Disable directory listing and block internal paths | F-04, F-07 |
| 2 | Secure session cookie flags | F-06 |
| 3 | Add security headers (`X-Frame-Options`, `nosniff`, CSP) | F-01, F-02, F-03, F-05 |
| 4 | Re-test every fix and update this report | All |

*Adjust the order once severities are filled in.*

---

## 8. Evidence Index

| File | Used in |
|------|---------|
| `01-juice-shop-anti-clickjacking.png` | F-01 |
| `02-juice-shop-csp.png` | F-02 |
| `03-juice-shop-content-type-options.png` | F-03 |
| `04-network-dns-analysis.png` | Section 6 |
| `05-network-http-analysis.png` | Section 6 |
| `06-network-tls-analysis.png` | Section 6 |
| `07-network-tcp-analysis.png` | Section 6 |
| `08-dvwa-directory-listing.png` | F-04 |
| `09-dvwa-security-headers.png` | F-05 |
| `10-dvwa-cookie-attributes.png` | F-06 |
| `11-dvwa-includes-directory.png` | F-07 |

**Sensitive data:** credentials, session tokens and private identifiers were removed or blurred before publication.

---

## 9. Limitations

- Testing covered only the listed local training applications and the collected evidence.
- Results do not describe the security of any external or production system.
- Severity ratings depend on the original task evidence and may change with more testing.

---

## 10. Conclusion

The three exercises gave practical experience in web security review, traffic analysis, reconnaissance, evidence collection and remediation planning. This report is a single reference for the verified observations and the fixes recommended for them.

*End of Report*
