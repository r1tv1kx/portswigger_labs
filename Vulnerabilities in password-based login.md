<div align="center">

# Lab 1: Vulnerabilities in Password-Based Login

[![Difficulty](https://img.shields.io/badge/Difficulty-Easy-brightgreen?style=for-the-badge)](#)
[![Type](https://img.shields.io/badge/Type-Brute%20Force-blue?style=for-the-badge)](#)
[![Severity](https://img.shields.io/badge/Severity-High-red?style=for-the-badge)](#)
[![Tool](https://img.shields.io/badge/Tool-Burp%20Suite-orange?style=for-the-badge)](#)
[![OWASP](https://img.shields.io/badge/OWASP-A07%3A2021-purple?style=for-the-badge)](#)

<img src="https://github.com/r1tv1kx/portswigger_labs/blob/e26abe68bb50b7e871c6081d5e9d8ec455c85e08/images/Screenshot%202025-05-14%20113905.png" alt="Lab Overview" width="680"/>

> **The website has a login page that doesn't protect itself against repeated login attempts. We're going to use a list of common usernames and passwords to automatically try every combination until we find the right one.**

</div>

---

## Table of Contents

- [What Are We Doing?](#what-are-we-doing)
- [What We Start With](#what-we-start-with)
- [Walkthrough](#walkthrough)
  - [Step 1 — Open the Login Page](#step-1--open-the-login-page)
  - [Step 2 — Turn on Burp Suite Intercept](#step-2--turn-on-burp-suite-intercept)
  - [Step 3 — Submit a Fake Login](#step-3--submit-a-fake-login)
  - [Step 4 — Observe the Captured Request](#step-4--observe-the-captured-request)
  - [Step 5 — Send to Intruder](#step-5--send-to-intruder)
  - [Step 6 — Set Up the Attack](#step-6--set-up-the-attack)
  - [Step 7 — Start the Attack](#step-7--start-the-attack)
  - [Step 8 — Find the Correct Username](#step-8--find-the-correct-username)
  - [Step 9 — Repeat for Password](#step-9--repeat-for-password)
- [What Damage Could This Cause?](#what-damage-could-this-cause)
- [How Do You Fix It?](#how-do-you-fix-it)
- [How Serious Is This?](#how-serious-is-this)
- [OWASP Mapping](#owasp-mapping)

---

## What Are We Doing?

Think of a website's login page like a door with a combination lock. If the lock doesn't stop you from trying over and over again, you can just keep guessing until you get the right combination.

That's exactly what this lab is about. The website lets you try as many username and password combinations as you want without locking you out. We'll use Burp Suite to automate the guessing — trying hundreds of combinations in seconds — until we find real credentials.

This is called a **brute-force attack**.

---

## What We Start With

The lab gives us three things:

- A website with a login page that is vulnerable
- A list of possible usernames to try
- A list of possible passwords to try

Our job is to figure out which username and password combination actually works.

---

## Walkthrough

### Step 1 — Open the Login Page

Click on **"My account"** on the website to open the login page.

<img src="https://github.com/r1tv1kx/portswigger_labs/blob/9bdfa56452e1374194260f0ad1a8d9945750933e/images/2025-05-14_12-15.png" alt="Login Page" width="680"/>

---

### Step 2 — Turn on Burp Suite Intercept

Open **Burp Suite** and go to **`Proxy → Intercept`**, then turn **Intercept ON**.

> When Intercept is ON, Burp Suite sits between your browser and the website — it catches every request before it goes out, so we can read and modify it. Think of it as a middleman who reads your letters before sending them.

---

### Step 3 — Submit a Fake Login

Go back to the browser. Type in any random username and password — it doesn't matter what, we're just getting the request to show up in Burp Suite.

Hit **Enter** or click the login button.

> The page will seem to hang. That's normal — Burp Suite has caught the request and is holding it, waiting for us.

---

### Step 4 — Observe the Captured Request

Switch back to Burp Suite. You'll see the login request has been caught.

<img src="https://github.com/r1tv1kx/portswigger_labs/blob/f70ca2531c61a38ca5779edec6ab065c6944d4f7/images/2025-05-14_12-30.png" alt="Captured Request" width="680"/>

Look at the bottom of the request — you'll see something like:

```
username=wronguser&password=wrongpassword
```

This is the data your browser was about to send to the server. The `username` and `password` fields are right there in plain sight. These are what we're going to manipulate.

---

### Step 5 — Send to Intruder

Right-click anywhere on the request and click **Send to Intruder**.

<img src="https://github.com/r1tv1kx/portswigger_labs/blob/d00be2f97523b31e9829b50f878b000dc364956b/images/Screenshot%202025-05-14%20124002.png" alt="Send to Intruder" width="680"/>

> **What is Intruder?** It's Burp Suite's automated attack tool. Instead of manually sending one request at a time, Intruder lets you fire off hundreds of requests automatically — swapping in a different value each time. It's the part that does the actual brute-forcing.

---

### Step 6 — Set Up the Attack

Click on the **Intruder** tab. You'll see the request with some parts highlighted.

<img src="https://github.com/r1tv1kx/portswigger_labs/blob/5119d304017c247ed23816845a82c8abeb532d7b/images/dsdfsd0.png" alt="Intruder Setup" width="680"/>

Do the following in order:

1. **Set the attack type to "Sniper"** — This tries one value at a time in one position. Good for testing usernames first, then passwords separately.

2. **Click "Clear"** — This removes any positions Burp Suite auto-highlighted. We want to set our own.

3. **Highlight the username value** (the `incorrect_username` part) and click **Add** — This tells Intruder "this is the part we want to swap out."

4. **Go to the Payloads tab** — Paste in every username from the candidate usernames list. These are the values Intruder will try one by one.

---

### Step 7 — Start the Attack

Click **Start Attack**.

Burp Suite will now automatically send the login request hundreds of times, swapping in a different username each time. You don't have to do anything — just watch the results come in.

---

### Step 8 — Find the Correct Username

A results window will open showing every attempt.

<img src="https://github.com/r1tv1kx/portswigger_labs/blob/e1e525582e085181f2b86c73e6f5b775770b4f2a/images/username.png" alt="Attack Results" width="680"/>

Look at the **Length** column. Most responses will be the same length — that's the "wrong username" error page. One response will have a different length — that's the server reacting differently, which means that username exists.

> **Why does length matter?** When the server says "wrong username", it sends back the same error message every time — same length. But when the username is correct, even if the password is wrong, the server's response changes slightly — different length. That difference is how we spot the valid username.

Write down the correct username. Now repeat the whole process for the password.

---

### Step 9 — Repeat for Password

Go back to Intruder. This time:

- Set the **position** to the password field instead of the username field
- Use the **correct username** you just found
- Paste in the **candidate passwords list** as your payloads
- Click **Start Attack**

Look for the response with a different length — or better yet, look for a `302` status code, which means the server redirected you after a successful login.

That's your password. Log in with both and the lab is done.

---

<div align="center">

## Lab Completed!

</div>

---

## What Damage Could This Cause?

If a real website had this weakness, an attacker could:

| What They Could Do | How Bad Is It? |
|---|---|
| Log in as any user on the platform | Full account takeover |
| Access private messages, photos, personal data | Privacy violation |
| Make purchases or transfers using someone else's account | Financial damage |
| Log in as an admin and take over the entire site | Complete compromise |

The scary part is this attack is fully automated. It doesn't take skill — just a wordlist and a tool like Burp Suite.

---

## How Do You Fix It?

The root problem is simple: the website lets you try as many times as you want. Here's how to fix that:

| Fix | What It Means in Plain English |
|---|---|
| **Lock the account after failed attempts** | After 5 wrong tries, lock the account for 10 minutes |
| **Add CAPTCHA** | Make the user prove they're human before trying again |
| **Rate limiting** | Slow down or block requests that come in too fast |
| **Multi-factor authentication** | Even if someone guesses the password, they still need a second code from your phone |
| **Use strong, unique passwords** | Common passwords are on wordlists — uncommon ones aren't |

---

## How Serious Is This?

<div align="center">

### Severity: HIGH

</div>

| Question | Answer |
|---|---|
| Does the attacker need an account? | No |
| Does it work over the internet? | Yes |
| Is it hard to pull off? | No — fully automated with free tools |
| Can it expose private data? | Yes — full account access |
| Does it leave obvious traces? | Sometimes — lots of failed logins in the logs |

> **Anyone can do this with a free tool and a wordlist downloaded from the internet. No technical expertise required.**

---

## OWASP Mapping

| Category | Reference |
|---|---|
| **Primary** | [A07:2021 — Identification and Authentication Failures](https://owasp.org/Top10/A07_2021-Identification_and_Authentication_Failures/) |
| Related | [A01:2021 — Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/) |
| Vulnerability Type | [CWE-307: Improper Restriction of Excessive Authentication Attempts](https://cwe.mitre.org/data/definitions/307.html) |

---

<div align="center">

*Walkthrough by Ritvik Singh — for educational purposes only.*

</div>
