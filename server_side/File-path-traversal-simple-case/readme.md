<div align="center">

# File Path Traversal — Simple Case

[![Difficulty](https://img.shields.io/badge/Difficulty-Easy-brightgreen?style=for-the-badge)](#)
[![Type](https://img.shields.io/badge/Type-Path%20Traversal-blue?style=for-the-badge)](#)
[![Severity](https://img.shields.io/badge/Severity-High%20%7C%207.5-red?style=for-the-badge)](#)
[![Tool](https://img.shields.io/badge/Tool-Burp%20Suite-orange?style=for-the-badge)](#)
[![OWASP](https://img.shields.io/badge/OWASP-A05%3A2021-purple?style=for-the-badge)](#)

<img src="https://github.com/user-attachments/assets/4a8c12f1-dc80-4e66-a43f-b8f5cdcca87b" alt="Lab Overview" width="680"/>

> **Exploit a vulnerable `filename` parameter to escape the intended directory and read sensitive server files using `../` traversal sequences.**

</div>

---

## Table of Contents

- [Objective](#-objective)
- [Walkthrough](#-walkthrough)
- [Step 1 — Generate Traffic](#step-1--generate-traffic)
- [Step 2 — Open HTTP History](#step-2--open-http-history)
- [Step 3 — Identify the Target Request](#step-3--identify-the-target-request)
- [Step 4 — Confirm Behavior](#step-4--confirm-behavior)
- [Step 5 — Send to Repeater](#step-5--send-to-repeater)
- [Step 6 — Test Traversal Payload](#step-6--test-traversal-payload)
- [Step 7 — Send Request](#step-7--send-request)
- [Step 8 — Verify Exploit](#step-8--verify-exploit)
- [Step 9 — Understand Why It Worked](#step-9--understand-why-it-worked)
- [Impact](#-impact)
- [Mitigation](#-mitigation)
- [Severity & CVSS](#-severity--cvss)
- [OWASP Mapping](#-owasp-mapping)

---

## Objective

Identify a vulnerable file parameter and exploit **directory traversal** (`../`) to access sensitive files outside the intended directory.

---

## Walkthrough

### Step 1 — Generate Traffic

1. Open the lab in your browser
2. Ensure **Burp Suite Proxy** is running
3. Set **Intercept OFF** *(we're using HTTP history, not live intercept)*
4. Click on any product image to trigger a file-loading request

> **Goal:** Force the application to make file-related HTTP requests that Burp will capture.

<img src="https://github.com/user-attachments/assets/4a8c12f1-dc80-4e66-a43f-b8f5cdcca87b" alt="Traffic Generation" width="680"/>

---

### Step 2 — Open HTTP History

Navigate to **`Proxy HTTP history`** in Burp Suite to view all captured requests.

> You are now in the **passive recon phase** — analyzing traffic without touching the server directly.

<img src="https://github.com/user-attachments/assets/03ac8ea3-1ebe-4389-b690-4211129cabbc" alt="HTTP History" width="680"/>

> **Tip:** Use the **filter bar** to narrow down results and find image-related requests faster.

<img src="https://github.com/user-attachments/assets/00ce3115-cde1-45ce-96b3-8a8831372a42" alt="HTTP History Filters" width="680"/>

---

### Step 3 — Identify the Target Request

Look for a request like:

```http
GET /image?filename=218.png HTTP/1.1
```

**What to focus on:**

| Field | Value | Why It Matters |
|---|---|---|
| Endpoint | `/image` | Handles file serving |
| Parameter | `filename` | Directly controls which file is read |
| Value | `218.png` | User-supplied — potential injection point |

> **This is your attack surface.**

<img src="https://github.com/user-attachments/assets/dfd096f8-9c14-4c0d-8173-36dc132687d6" alt="Target Request" width="680"/>

---

### Step 4 — Confirm Behavior

1. Click the request in HTTP history
2. Open the **Response** tab
3. Confirm you see rendered image data

> This proves the `filename` parameter is **directly used** to read files from the server's filesystem — no abstraction layer in between.

<img src="https://github.com/user-attachments/assets/24b045b8-ef9a-4ca9-9eac-ca59e289a5eb" alt="Confirm Behavior" width="680"/>

---

### Step 5 — Send to Repeater

1. **Right-click** the request
2. Select **Send to Repeater**
3. Navigate to the **Repeater** tab

> Repeater lets you modify and resend requests freely without disrupting your browser session.

---

### Step 6 — Test Traversal Payload

Modify the `filename` value to traverse up the directory tree:

**Before:**
```
GET /image?filename=7.jpg
```

**After:**
```
GET /image?filename=../../../etc/passwd
```

> The `../` sequences move up one directory each. Three levels up from a typical web root lands you at the filesystem root `/`.

<img src="https://github.com/user-attachments/assets/5d5732bb-7657-4bf3-9bb7-6a759310f77f" alt="Before Payload" width="680"/>

<img src="https://github.com/user-attachments/assets/744c7d53-9fb7-478e-8c1c-1882ce8067df" alt="After Payload" width="680"/>

---

### Step 7 — Send Request

Click **Send** in Repeater and carefully examine the response body.

<img src="https://github.com/user-attachments/assets/9484f1b2-8963-4dd3-9f61-9e19d31a89d4" alt="Send Request" width="680"/>

---

### Step 8 — Verify Exploit

A successful exploit will return the contents of `/etc/passwd`:

```
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
...
```

**Indicators of success:**
- `root:x:0:0` visible in response
- Multiple system user entries listed
- HTTP `200 OK` status

> **Path traversal confirmed. Sensitive file read successfully.**

<img src="https://github.com/user-attachments/assets/0696cc9b-09ec-4383-a7c4-07be92c8f1c0" alt="Exploit Verified" width="680"/>

---

### Step 9 — Understand Why It Worked

The vulnerability exists because the server constructs a file path like this:

```python
# Vulnerable pseudocode
file_path = BASE_DIR + request.params["filename"]
# /var/www/images/ + ../../../etc/passwd
# = /etc/passwd (attacker wins)
```

<img src="https://github.com/user-attachments/assets/c0b05c73-eec7-4deb-9629-1cd3992c83ca" alt="Why It Worked" width="680"/>

**Root causes:**
- User input used **directly** in filesystem operations
- No stripping or blocking of `../` sequences
- No restriction to a base directory
- No canonicalization of the resolved path

---

<div align="center">

## Lab Completed!

</div>

---

## Impact

An attacker can read **any file the web server process has access to**, including:

| File | Contents |
|---|---|
| `/etc/passwd` | System user accounts |
| `/etc/shadow` | Hashed passwords *(if readable)* |
| `config.php` / `.env` | Database credentials, API keys |
| `/proc/self/environ` | Environment variables, secrets |
| App source code | Business logic, hardcoded secrets |

**Attack chains this enables:**
- Credential theft unauthorized access to databases / admin panels
- Internal recon understand server layout for deeper exploitation
- Chaining with RCE (e.g., via log poisoning or SSRF)
- In enterprise environments full server compromise

---

## Mitigation

| Fix | Implementation |
|---|---|
| **Whitelist validation** | Only allow known safe filenames or extensions |
| **Canonicalize paths** | Resolve the full path, then verify it starts with the allowed base directory |
| **Restrict to base directory** | Never serve files outside `/var/www/images/` (or equivalent) |
| **Avoid raw user input** | Never concatenate user input directly into file paths |
| **Least privilege** | Run the web server as a user with minimal filesystem access |

```python
# Secure pseudocode example
import os

BASE_DIR = "/var/www/images/"
requested = request.params["filename"]
safe_path = os.path.realpath(os.path.join(BASE_DIR, requested))

if not safe_path.startswith(BASE_DIR):
abort(400) # Reject traversal attempts

serve_file(safe_path)
```

---

## Severity & CVSS

<div align="center">

### Severity: HIGH — CVSS Score: ~7.5

</div>

| Metric | Value |
|---|---|
| Attack Vector | Network |
| Attack Complexity | Low |
| Privileges Required | None |
| User Interaction | None |
| Confidentiality Impact | **High** |
| Integrity Impact | None |
| Availability Impact | None |

> **No authentication needed. Exploitable remotely. High data exposure. Low complexity.**

---

## OWASP Mapping

| Category | Reference |
|---|---|
| **Primary** | [A05:2021 — Security Misconfiguration](https://owasp.org/Top10/A05_2021-Security_Misconfiguration/) |
| Related | [A01:2021 — Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/) |
| Related | Improper Input Validation (CWE-20) |
| CVE Pattern | [CWE-22: Path Traversal](https://cwe.mitre.org/data/definitions/22.html) |

---

<div align="center">

*Walkthrough by Ritvik Singh — for educational purposes only.*

</div>
