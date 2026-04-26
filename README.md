# PortSwigger Web Security Academy - Walkthroughs

A structured collection of walkthroughs for PortSwigger Web Security Academy labs, organized by vulnerability category.

---

## About

This repository contains detailed walkthroughs for labs from the [PortSwigger Web Security Academy](https://portswigger.net/web-security). Each walkthrough documents the methodology, steps, and key concepts behind solving the lab. The goal is to serve as a personal reference and a resource for others learning web application security.

---

## Repository Structure

```
portswigger-labs/
├── server-side/
│   ├── sql-injection/
│   ├── authentication/
│   ├── path-traversal/
│   ├── command-injection/
│   ├── business-logic/
│   ├── information-disclosure/
│   ├── access-control/
│   ├── file-upload/
│   ├── race-conditions/
│   ├── ssrf/
│   ├── xxe/
│   └── nosql-injection/
│
└── client-side/
    ├── xss/
    ├── csrf/
    ├── cors/
    ├── clickjacking/
    ├── dom-based/
    ├── websockets/
    └── postmessage/
```

Each lab folder contains a markdown file with:
- Lab title and difficulty level
- Objective
- Step-by-step walkthrough
- Key concepts and takeaways
- Relevant tools and payloads used

---

## Categories

### Server Side

| Category | Description |
|---|---|
| SQL Injection | Exploiting SQL queries to extract data, bypass authentication, and perform blind attacks |
| Authentication | Flaws in login mechanisms, brute force, MFA bypasses |
| Path Traversal | Reading arbitrary files via directory traversal sequences |
| Command Injection | Injecting OS commands through vulnerable application inputs |
| Business Logic | Exploiting flawed assumptions in application workflows |
| Information Disclosure | Unintended exposure of sensitive data and debug information |
| Access Control | Broken authorization, IDOR, and privilege escalation |
| File Upload | Bypassing restrictions to upload and execute malicious files |
| Race Conditions | Exploiting timing windows in concurrent request handling |
| SSRF | Forging server-side requests to internal or external systems |
| XXE | XML external entity injection to read files or trigger SSRF |
| NoSQL Injection | Attacking NoSQL databases through injection techniques |

### Client Side

| Category | Description |
|---|---|
| Cross-Site Scripting (XSS) | Reflected, stored, and DOM-based XSS attacks |
| CSRF | Forging cross-site requests on behalf of authenticated users |
| CORS | Exploiting misconfigured cross-origin resource sharing policies |
| Clickjacking | UI redressing attacks to trick users into unintended actions |
| DOM-Based Vulnerabilities | Sink and source analysis for client-side exploitation |
| WebSockets | Attacking real-time WebSocket communications |
| PostMessage | Exploiting insecure cross-document messaging |

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
