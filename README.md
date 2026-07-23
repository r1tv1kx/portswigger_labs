<div align="center">

# PortSwigger Web Security Academy — Walkthroughs

**A structured, step-by-step reference for solving PortSwigger Web Security Academy labs.**

[![Labs Completed](https://img.shields.io/badge/Labs%20Completed-2-brightgreen?style=for-the-badge)](#completed-labs)
[![Academy](https://img.shields.io/badge/PortSwigger-Web%20Security%20Academy-orange?style=for-the-badge)](https://portswigger.net/web-security)
[![Purpose](https://img.shields.io/badge/Purpose-Educational-blue?style=for-the-badge)](#disclaimer)

</div>

---

## About

This repository documents my progress through the [PortSwigger Web Security Academy](https://portswigger.net/web-security). Each lab gets its own walkthrough covering the methodology, the exact steps taken in Burp Suite, why the vulnerability exists, and how it would be fixed in a real application — not just "click here to solve it."

It exists as a personal reference and, hopefully, a useful resource for anyone else working through the same labs.

## Table of Contents

- [Completed Labs](#completed-labs)
- [What Each Walkthrough Covers](#what-each-walkthrough-covers)
- [Repository Structure](#repository-structure)
- [Roadmap](#roadmap)
- [Tools Used](#tools-used)
- [Disclaimer](#disclaimer)
- [Resources](#resources)

---

## Completed Labs

| # | Category | Lab | Difficulty |
|---|---|---|---|
| 1 | Authentication | [Vulnerabilities in Password-Based Login](server_side/authentication/README.md) | 🟢 Easy |
| 2 | Path Traversal | [File Path Traversal — Simple Case](server_side/path-traversal/README.md) | 🟢 Easy |

---

## What Each Walkthrough Covers

Every lab writeup follows the same format, so you can jump straight to the part you need:

- **Objective** — what the lab is asking you to do, in plain language
- **Walkthrough** — numbered steps with screenshots, from opening the lab to confirming the exploit
- **What Damage Could This Cause?** — realistic impact if this showed up in production
- **How Do You Fix It?** — the actual remediation, with code where relevant
- **OWASP Mapping** — where the vulnerability lands in the OWASP Top 10 / CWE

---

## Repository Structure

```
portswigger_labs/
├── server_side/
│   ├── authentication/
│   │   └── README.md
│   └── path-traversal/
│       └── README.md
│
└── images/
    └── ...screenshots referenced by the walkthroughs above
```

As more labs are completed, they'll be added under `server_side/` or `client_side/`, grouped by vulnerability class.

---

## Roadmap

Categories from the Academy still to be documented:

**Server-side**
- [ ] SQL Injection
- [ ] Command Injection
- [ ] Business Logic
- [ ] Information Disclosure
- [ ] Access Control
- [ ] File Upload
- [ ] Race Conditions
- [ ] SSRF
- [ ] XXE
- [ ] NoSQL Injection

**Client-side**
- [ ] Cross-Site Scripting (XSS)
- [ ] CSRF
- [ ] CORS
- [ ] Clickjacking
- [ ] DOM-Based Vulnerabilities
- [ ] WebSockets
- [ ] PostMessage

---

## Tools Used

- Burp Suite (Community / Pro)
- Browser Developer Tools
- curl
- Python (for custom scripts where applicable)

---

## Disclaimer

These walkthroughs are intended strictly for educational purposes. All labs are performed on PortSwigger's own controlled lab environment. Do not apply these techniques against any systems without explicit authorization.

---

## Resources

- [PortSwigger Web Security Academy](https://portswigger.net/web-security)
- [Burp Suite Documentation](https://portswigger.net/burp/documentation)
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)

---

<div align="center">

*Maintained by [Ritvik Singh](https://github.com/r1tv1kx) — for educational purposes only.*

</div>
