# File Path Traversal — Simple Case (Walkthrough)

<p align="center">
  <img src="https://github.com/user-attachments/assets/4a8c12f1-dc80-4e66-a43f-b8f5cdcca87b" alt="Traffic Generation" width="600"/>
</p>

---

## Objective

Identify a vulnerable file parameter and exploit directory traversal (`../`) to access sensitive files outside the intended directory.

---

## Steps

### Step 1: Generate Traffic

1. Open the lab in your browser
2. Make sure **Burp Proxy** is running
3. Keep **Intercept OFF** (important for HTTP history method)
4. Click on any product image or functionality that loads an image

> **Goal:** Force the application to send file-related requests



---

### Step 2: Open HTTP History

Navigate to:

```
Proxy → HTTP history
```

You will see multiple requests captured here.

> **You are now in the passive recon phase — analyzing captured traffic.**

![HTTP History](https://github.com/user-attachments/assets/03ac8ea3-1ebe-4389-b690-4211129cabbc)

> 💡 **Tip:** To speed up finding the image request, apply filters in the HTTP history panel.

![HTTP History Filters](https://github.com/user-attachments/assets/00ce3115-cde1-45ce-96b3-8a8831372a42)

---

### Step 3: Identify the Target Request

Look for a request like:

```http
GET /image?filename=218.png HTTP/1.1
```

**Focus points:**

| Field | Value |
|---|---|
| Parameter name | `filename` |
| File type | `.png` |
| Endpoint | `/image` |

> **This is your attack surface.**

![Target Request](https://github.com/user-attachments/assets/dfd096f8-9c14-4c0d-8173-36dc132687d6)

---

### Step 4: Confirm Behavior

1. Click the request
2. Check the **Response** tab

You should see image data or a rendered image.

> **This confirms the parameter is actively used to fetch files from the filesystem.**

![Confirm Behavior](https://github.com/user-attachments/assets/24b045b8-ef9a-4ca9-9eac-ca59e289a5eb)

---

### Step 5: Send to Repeater

1. Right-click the request
2. Click **Send to Repeater**
3. Switch to the **Repeater** tab

> **Now you can safely modify and test payloads without affecting the browser session.**

---

### Step 6: Test Traversal Payload

Modify the `filename` parameter:

**Before:**
```
filename=7.jpg
```

**After:**
```
filename=../../../etc/passwd
```

Full request becomes:

```http
GET /image?filename=../../../etc/passwd HTTP/1.1
```

![Payload Before](https://github.com/user-attachments/assets/5d5732bb-7657-4bf3-9bb7-6a759310f77f)

After replacing it should look like this:

![Payload After](https://github.com/user-attachments/assets/744c7d53-9fb7-478e-8c1c-1882ce8067df)

---

### Step 7: Send Request

Click **Send** in Repeater and observe the response carefully.

![Send Request](https://github.com/user-attachments/assets/9484f1b2-8963-4dd3-9f61-9e19d31a89d4)

---

### Step 8: Verify Exploit

If the application is vulnerable, the response will contain content like:

```
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
...
```

**Key indicators of success:**
- `root:x:0:0` is present
- Multiple system users are listed

> ✅ **This confirms successful path traversal.**

![Exploit Verified](https://github.com/user-attachments/assets/0696cc9b-09ec-4383-a7c4-07be92c8f1c0)

---

### Step 9: Understand Why It Worked

- The application **directly used user input** in the file path construction
- **No validation or sanitization** was applied to the `filename` parameter
- The `../` sequences were not stripped, allowing **directory escape**

![Why It Worked](https://github.com/user-attachments/assets/c0b05c73-eec7-4deb-9629-1cd3992c83ca)

---

## ✅ Lab Completed!

---

## Impact

**An attacker can access sensitive files from the server filesystem, including:**

- System files (e.g., `/etc/passwd`, `/etc/shadow`)
- Application configuration files
- Credentials or API keys stored on disk

**This can lead to:**
- Privilege escalation
- Further exploitation of discovered credentials
- Full system compromise

---

## Mitigation

| Recommendation | Details |
|---|---|
| Input validation | Use whitelist-based validation — only allow expected filenames/extensions |
| Restrict file access | Limit file reads to a predefined base directory |
| Canonical path validation | Resolve the full path and verify it starts with the allowed base directory |
| Avoid raw user input in filesystem calls | Never pass unsanitized user input directly to file system operations |

---

## Conclusion

- The application is **vulnerable to path traversal**
- Caused by **improper handling of user-supplied input** in the `filename` parameter
- Allows attackers to **access sensitive files** outside intended directories
- Directly compromises **system confidentiality**

---

## Severity Assessment

**Severity: 🔴 High**

| Factor | Detail |
|---|---|
| Direct file access | Sensitive server files exposed |
| Authentication required | None |
| Credential exposure risk | High |

### CVSS Score (Approximate)

| Metric | Value |
|---|---|
| Attack Vector | Network (AV:N) |
| Attack Complexity | Low (AC:L) |
| Privileges Required | None (PR:N) |
| User Interaction | None (UI:N) |
| Scope | Unchanged (S:U) |
| Confidentiality Impact | High (C:H) |
| Integrity Impact | None (I:N) |
| Availability Impact | None (A:N) |
| **Score** | **~7.5 (High)** |

---

## OWASP Mapping

**Primary:** [A05: Security Misconfiguration](https://owasp.org/Top10/A05_2021-Security_Misconfiguration/) — OWASP Top 10 2021

**Also related to:**
- Improper Input Validation
- Broken Access Control (A01)

---

## Real-World Impact

**Files an attacker may read:**
- `/etc/passwd` — system user enumeration
- `/etc/shadow` — hashed passwords (if permissions allow)
- Application config files — may contain database credentials, API keys

**Attack chains this enables:**
- Credential leakage → unauthorized access
- Internal reconnaissance → network mapping
- Chaining with other vulnerabilities (e.g., Remote Code Execution via log poisoning)
- In enterprise environments: potential **full server compromise**
