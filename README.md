# PortSwigger Web Security Academy - Walkthroughs

A structured collection of walkthroughs for PortSwigger Web Security Academy labs, organized by vulnerability category.

---

## About

This repository contains detailed walkthroughs for labs from the [PortSwigger Web Security Academy](https://portswigger.net/web-security). Each walkthrough documents the methodology, steps, and key concepts behind solving the lab. The goal is to serve as a personal reference and a resource for others learning web application security.

---

## Completed Labs

| Category | Lab | Difficulty |
|---|---|---|
| Authentication | [Vulnerabilities in Password-Based Login](server_side/authentication/README.md) | Easy |
| Path Traversal | [File Path Traversal — Simple Case](server_side/path-traversal/README.md) | Easy |

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

Each lab folder contains a markdown file with:
- Lab title and difficulty level
- Objective
- Step-by-step walkthrough
- Key concepts and takeaways
- Relevant tools and payloads used

---

## Planned Categories

More labs will be added over time, organized under `server_side/` and `client_side/` by vulnerability class:

| Server Side | Client Side |
|---|---|
| SQL Injection | Cross-Site Scripting (XSS) |
| Command Injection | CSRF |
| Business Logic | CORS |
| Information Disclosure | Clickjacking |
| Access Control | DOM-Based Vulnerabilities |
| File Upload | WebSockets |
| Race Conditions | PostMessage |
| SSRF | |
| XXE | |
| NoSQL Injection | |

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
