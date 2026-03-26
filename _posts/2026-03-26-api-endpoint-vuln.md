---
title: Finding and Exploiting an Unused API Endpoint - Business Logic Vulnerability
date: 2026-03-26 00:00:00 +0800
categories: [Business Logic Vulnerabilities, API Security]
tags: [API Security, Business Logic, Practitioner, Burp Suite]
---

## Lab Overview

This lab demonstrates a **Business Logic Vulnerability** arising from an **exposed and unused API endpoint** that allows unauthorized modification of sensitive data.

The application exposes an internal API endpoint intended for administrative or backend use, but fails to properly restrict access to it. By interacting directly with this endpoint, an attacker can manipulate product pricing.

**Lab URL:** https://portswigger.net/web-security/api-testing/lab-exploiting-unused-api-endpoint

**Goal:** Manipulate the price of the **"Lightweight l33t leather jacket"** and purchase it for free.

---

## Vulnerability Explanation

The core issue lies in **Improper Access Control on API Endpoints**.

The application exposes an endpoint:

`/api/products/1/price`

This endpoint allows updating the product price using the `PATCH` method. However, it is:

- Not used by the frontend
- Not protected by proper authorization checks
- Accessible by any authenticated user

This allows attackers to directly modify the product price.

---

## My Approach

### Step 1 – Exploring the Application
I started by browsing the application and identifying potential API endpoints.

While exploring, I discovered the endpoint:

`/api/products/1/price`

This looked interesting as it directly referenced product pricing.

---

### Step 2 – Testing the Endpoint in Repeater
I sent the request to **Burp Suite Repeater** to analyze its behavior.

Initially, I tested different HTTP methods.

---

### Step 3 – Discovering Allowed Methods
I changed the request method to `OPTIONS` to check supported methods.

```http
HTTP/2 405 Method Not Allowed
Allow: GET, PATCH
````

This revealed that the endpoint supports:

* `GET`
* `PATCH`

This was a strong indicator that the endpoint might allow modification.

---

### Step 4 – Switching to PATCH Method

I modified the request to use the `PATCH` method.

```http
HTTP/2 400 Bad Request

{"error":"Only 'application/json' Content-Type is supported"}
```

This indicated that the endpoint expects JSON input.

---

### Step 5 – Adding Content-Type Header

I added:

```
Content-Type: application/json
```

Now the response changed:

```http
HTTP/2 400 Bad Request

{"error":"'price' parameter missing in body"}
```

This clearly showed that the API expects a `price` parameter.

---

### Step 6 – Modifying the Price

I crafted the final request:

```http
PATCH /api/products/1/price HTTP/2
Host: [lab-id].web-security-academy.net
Content-Type: application/json

{
  "price": 0
}
```

---

### Step 7 – Successful Exploit

The server responded with:

```http
HTTP/2 200 OK

{"price":"$0.00"}
```

This confirmed that the price was successfully updated to **$0.00**.

---

### Step 8 – Completing the Lab

I navigated back to the product page and added the jacket to the cart.

Since the price was now **$0.00**, I was able to place the order successfully and solve the lab.

---

## Intuition

The secure logic should be:

> *Only authorized users (e.g., admins) can modify product prices via protected backend systems.*

However, the vulnerable logic was:

> *Any user who discovers the API endpoint can directly modify the price.*

This highlights a common issue:

* Hidden endpoints ≠ Secure endpoints

---

## Tools Used

* **Burp Suite (Repeater)**
* **Browser Developer Tools**

---

## Vulnerability Category

**Business Logic Vulnerabilities**

This specifically falls under:

* **Broken Access Control**
* **Exposed/Unused API Endpoint**
* **Improper Authorization**

---

## Key Takeaway

APIs must never rely on obscurity for security.

Even if an endpoint is not used in the frontend:

* It must be properly authenticated
* It must enforce strict authorization checks

Always assume attackers will discover hidden endpoints.

---

## Impact

The impact is severe:

* Attackers can manipulate product prices
* Leads to direct financial loss
* Can be automated to exploit multiple products

In real-world scenarios, this could allow attackers to purchase expensive items for free or near-zero cost.
