---
title: User-ID controlled by request parameter with password disclosure
date: 2026-03-10 00:00:00 +0800
category: [Broken Access Control]
tags: [Broken Access Control, Apprentice]
---

## Lab Overview

This lab demonstrates a **Broken Access Control vulnerability** where the application determines which user's account page to display based on a **user-controlled request parameter**.

Because the application does not properly verify whether the logged-in user is authorized to access the requested account, an attacker can modify the request parameter to access another user's account information, including their password.

Lab URL:  
https://portswigger.net/web-security/access-control/lab-user-id-controlled-by-request-parameter-with-password-disclosure

Goal:  
Access the administrator account page and obtain the administrator's password.

---

## Vulnerability Explanation

The application uses a request parameter such as: 

?id=wiener

to determine which user's account details should be displayed.

Since this parameter is **fully controllable by the client**, an attacker can modify it to access another user's account:

?id=administrator

Because the server **does not validate whether the logged-in user is authorized to view this account**, it results in an **Insecure Direct Object Reference (IDOR)** vulnerability.

In this lab, the application even exposes the **administrator's password**, which allows the attacker to log in as the administrator and solve the lab.

---

## My Approach

### Step 1 – Login with the provided credentials

Login using the credentials provided by the lab:

username: wiener
password: peter

After logging in, navigate to **My Account**.

The URL looks like:

/my-account?id=wiener

---

### Step 2 – Identify the user-controlled parameter

Observe that the account being displayed is determined by the `id` parameter.

/my-account?id=wiener

This indicates the application may be vulnerable to **parameter tampering**.

---

### Step 3 – Modify the request parameter

Change the value of the `id` parameter to `administrator`:

/my-account?id=administrator

---

### Step 4 – Access the administrator account

The application now displays the **administrator account page**, including the **administrator password**.

Copy the password shown on the page.

---

### Step 5 – Login as administrator

Navigate to the login page and log in using:

username: administrator
password: <retrieved_password>

Once logged in successfully, the lab is solved.

---

## Intuition

Whenever an application uses **user identifiers in URL parameters**, it is important to test whether they can be modified.

If the server does not enforce proper authorization checks, attackers may gain access to sensitive user data.

This vulnerability occurs because the server trusts **client-controlled input** to determine which user data should be displayed.

---

## Tools Used

- Burp Suite (for intercepting and modifying requests)
- Browser developer tools

---

## Vulnerability Category

**Broken Access Control**

More specifically, this vulnerability is an example of:

- **Insecure Direct Object Reference (IDOR)**

This type of vulnerability is part of the **OWASP Top 10 – Broken Access Control** category.

---

## Key Takeaway

Applications must **never rely solely on client-provided parameters** to enforce access control.

Proper server-side authorization checks must always be implemented to ensure users can only access resources that belong to them.

## Impact

An attacker can access sensitive information belonging to other users, including credentials.  
This can lead to account takeover and privilege escalation.