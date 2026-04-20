<div align="center">

# File Path Traversal — Simple Case

[![Difficulty](https://img.shields.io/badge/Difficulty-Easy-brightgreen?style=for-the-badge)](#)
[![Type](https://img.shields.io/badge/Type-Path%20Traversal-blue?style=for-the-badge)](#)
[![Severity](https://img.shields.io/badge/Severity-High%20%7C%207.5-red?style=for-the-badge)](#)
[![Tool](https://img.shields.io/badge/Tool-Burp%20Suite-orange?style=for-the-badge)](#)
[![OWASP](https://img.shields.io/badge/OWASP-A05%3A2021-purple?style=for-the-badge)](#)

<img src="https://github.com/user-attachments/assets/4a8c12f1-dc80-4e66-a43f-b8f5cdcca87b" alt="Lab Overview" width="680"/>

> **The website lets users load images by name. We trick it into loading a secret system file instead — just by tweaking the filename in the URL.**

</div>

---

## Table of Contents

- [What Are We Doing?](#what-are-we-doing)
- [Walkthrough](#walkthrough)
  - [Step 1 — Generate Traffic](#step-1--generate-traffic)
  - [Step 2 — Open HTTP History](#step-2--open-http-history)
  - [Step 3 — Identify the Target Request](#step-3--identify-the-target-request)
  - [Step 4 — Confirm Behavior](#step-4--confirm-behavior)
  - [Step 5 — Send to Repeater](#step-5--send-to-repeater)
  - [Step 6 — Test Traversal Payload](#step-6--test-traversal-payload)
  - [Step 7 — Send Request](#step-7--send-request)
  - [Step 8 — Verify Exploit](#step-8--verify-exploit)
  - [Step 9 — Understand Why It Worked](#step-9--understand-why-it-worked)
- [What Damage Could This Cause?](#what-damage-could-this-cause)
- [How Do You Fix It?](#how-do-you-fix-it)
- [How Serious Is This?](#how-serious-is-this)
- [OWASP Mapping](#owasp-mapping)

---

## What Are We Doing?

Imagine a website that shows you product images. Behind the scenes, when you click on an image, the website goes to its file storage and fetches the image by name.

The problem? It trusts whatever name you give it. So instead of asking for an image, we can ask for a sensitive file — like a list of all users on the server — and the website will just hand it over.

That's Path Traversal in a nutshell.

---

## Walkthrough

### Step 1 — Generate Traffic

First, we need to get the website to make a request that loads an image, so we can see exactly how it works.

1. Open the lab in your browser
2. Make sure **Burp Suite** is open and running in the background *(this is our traffic-watching tool)*
3. Make sure **Intercept is OFF** — we don't want to pause every request, we just want to watch them
4. Click on any product image on the page

> **Goal:** Get the website to make a file request so we can see it in Burp Suite.


---

### Step 2 — Open HTTP History

Now let's look at all the requests that just happened.

Go to **`Proxy → HTTP history`** in Burp Suite. Think of this as a log — every request your browser made is listed here.

> We're just reading the log at this point. We haven't touched anything yet.

<img src="https://github.com/user-attachments/assets/03ac8ea3-1ebe-4389-b690-4211129cabbc" alt="HTTP History" width="680"/>

> **Tip:** There will be a lot of requests. Use the filter to only show image-related ones so it's easier to find what we need.

<img src="https://github.com/user-attachments/assets/00ce3115-cde1-45ce-96b3-8a8831372a42" alt="HTTP History Filters" width="680"/>

---

### Step 3 — Identify the Target Request

Scroll through the list and look for a request that looks like this:

```http
GET /image?filename=218.png HTTP/1.1
```

This is the website saying: *"Go find a file called 218.png and show it to the user."*

**What matters here:**

| Part | What It Is | Why We Care |
|---|---|---|
| `/image` | The page that serves files | This is what fetches the file |
| `filename` | The input field | We can change this to anything |
| `218.png` | The current file being fetched | We're going to swap this out |

> **This is the weak point. The website is asking us what file to load — and it trusts our answer.**

<img src="https://github.com/user-attachments/assets/dfd096f8-9c14-4c0d-8173-36dc132687d6" alt="Target Request" width="680"/>

---

### Step 4 — Confirm Behavior

Before we do anything sneaky, let's just confirm this request is actually fetching a real file.

1. Click on the request in the list
2. Look at the **Response** tab on the right
3. You should see image data

> If there's image data in the response, it means the server is genuinely reading a file from its storage and sending it back. Which also means — if we change the filename, it'll try to read *that* file instead.

<img src="https://github.com/user-attachments/assets/24b045b8-ef9a-4ca9-9eac-ca59e289a5eb" alt="Confirm Behavior" width="680"/>

---

### Step 5 — Send to Repeater

Now we want a place where we can freely edit the request and try different things without breaking anything.

1. **Right-click** the request
2. Click **Send to Repeater**
3. Click on the **Repeater** tab at the top

> Repeater is like a sandbox. We can change the request and hit Send as many times as we want — it won't affect our browser or the rest of the site.

---

### Step 6 — Test Traversal Payload

Here's where the actual trick happens.

We're going to change the filename from a real image to a path that walks *up* the folder structure on the server — and then asks for a sensitive system file.

**Before:**
```
GET /image?filename=7.jpg
```

**After:**
```
GET /image?filename=../../../etc/passwd
```

Each `../` means "go one folder up." So `../../../` from the images folder takes us all the way up to the root of the server's file system. From there, `/etc/passwd` is a standard file that lists all the user accounts on a Linux server.

<img src="https://github.com/user-attachments/assets/5d5732bb-7657-4bf3-9bb7-6a759310f77f" alt="Before Payload" width="680"/>

<img src="https://github.com/user-attachments/assets/744c7d53-9fb7-478e-8c1c-1882ce8067df" alt="After Payload" width="680"/>

---

### Step 7 — Send Request

Hit **Send** in Repeater and look at what comes back in the response.

<img src="https://github.com/user-attachments/assets/9484f1b2-8963-4dd3-9f61-9e19d31a89d4" alt="Send Request" width="680"/>

---

### Step 8 — Verify Exploit

If the server is vulnerable, instead of an image, you'll get back something like this:

```
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
...
```

This is the `/etc/passwd` file — a list of all user accounts on the server.

**How to know it worked:**
- You can see `root:x:0:0` in the response
- There are multiple lines each representing a different system user
- The server responded with `200 OK` — no error

> **It worked. We asked for an image, got a secret system file instead.**

<img src="https://github.com/user-attachments/assets/0696cc9b-09ec-4383-a7c4-07be92c8f1c0" alt="Exploit Verified" width="680"/>

---

### Step 9 — Understand Why It Worked

The server builds the file path by simply sticking our input onto the end of a base folder path:

```python
# What the server does (simplified)
file_path = "/var/www/images/" + filename_from_user
# We gave it: ../../../etc/passwd
# So it looked for: /var/www/images/../../../etc/passwd
# Which is the same as: /etc/passwd
```

<img src="https://github.com/user-attachments/assets/c0b05c73-eec7-4deb-9629-1cd3992c83ca" alt="Why It Worked" width="680"/>

**Why it failed to stop us:**
- It took our input and used it directly — no checks whatsoever
- It never stripped out the `../` parts
- It never checked whether the final path was still inside the images folder
- It just blindly fetched whatever file we pointed it at

---

<div align="center">

## Lab Completed!

</div>

---

## What Damage Could This Cause?

Once an attacker can read files freely from the server, here's what they could get their hands on:

| File | What It Contains |
|---|---|
| `/etc/passwd` | All user accounts on the server |
| `/etc/shadow` | Encrypted passwords *(if permissions allow)* |
| `config.php` / `.env` | Database usernames and passwords, secret API keys |
| `/proc/self/environ` | Environment variables — often contain secrets |
| App source code | How the app works, any hardcoded secrets |

**What an attacker could do next:**
- Use stolen database credentials to log directly into the database
- Use leaked API keys to impersonate the application
- Read the source code to find more vulnerabilities
- In a corporate environment, this could lead to the entire server being taken over

---

## How Do You Fix It?

The fix comes down to one thing: **never trust user input when dealing with files.**

| Fix | What It Means in Plain English |
|---|---|
| **Whitelist filenames** | Only allow specific known filenames — reject everything else |
| **Check the final path** | After building the path, check it still points inside the allowed folder |
| **Lock down the folder** | The app should only be allowed to read files from one specific folder, nothing else |
| **Don't use raw input** | Never paste user input directly into a file path |
| **Limit server permissions** | The web server process shouldn't have access to sensitive system files in the first place |

```python
# The safe way to do it
import os

ALLOWED_FOLDER = "/var/www/images/"
user_input = request.params["filename"]

# Build the full path and resolve any ../ tricks
full_path = os.path.realpath(os.path.join(ALLOWED_FOLDER, user_input))

# Check the final path is still inside the allowed folder
if not full_path.startswith(ALLOWED_FOLDER):
    abort(400)  # Reject it — someone's trying something sneaky

serve_file(full_path)
```

---

## How Serious Is This?

<div align="center">

### Severity: HIGH — CVSS Score: ~7.5

</div>

| Question | Answer |
|---|---|
| Does the attacker need an account? | No |
| Does the attacker need to be nearby? | No — works over the internet |
| Is it hard to pull off? | No — very simple |
| Can it expose sensitive data? | Yes — any file the server can read |
| Can it modify or delete files? | No — read only |

> **Anyone on the internet can try this. No login needed. One request is all it takes.**

---

## OWASP Mapping

OWASP is a globally recognized standard for web security. Here's where this vulnerability fits:

| Category | Reference |
|---|---|
| **Primary** | [A05:2021 — Security Misconfiguration](https://owasp.org/Top10/A05_2021-Security_Misconfiguration/) |
| Related | [A01:2021 — Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/) |
| Related | Improper Input Validation (CWE-20) |
| Vulnerability Type | [CWE-22: Path Traversal](https://cwe.mitre.org/data/definitions/22.html) |

---

<div align="center">

*Walkthrough by Ritvik Singh — for educational purposes only.*

</div>
