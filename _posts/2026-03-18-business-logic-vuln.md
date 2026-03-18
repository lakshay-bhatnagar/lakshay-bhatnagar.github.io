---
title: Excessive trust in client-side controls - Business Logic Vulnerability
date: 2026-03-18 00:00:00 +0800
categories: [Business Logic Vulnerabilities]
tags: [Business Logic, Apprentice, Client-Side Controls]
---

## Lab Overview

This lab demonstrates a fundamental **Business Logic Vulnerability** where the application assumes that data sent from the client (the browser) remains untampered. 

The application fails to perform server-side validation on the price of an item during the "Add to Cart" process, allowing a user to dictate the cost of their own purchase.

**Lab URL:** [https://portswigger.net/web-security/logic-flaws/examples/lab-logic-flaws-excessive-trust-in-client-side-controls](https://portswigger.net/web-security/logic-flaws/examples/lab-logic-flaws-excessive-trust-in-client-side-controls)

**Goal:** Purchase the "Lightweight l33t leather jacket" for a price lower than its listed value ($1337.00) using the limited $100 store credit provided.

---

## Vulnerability Explanation

The core issue lies in **Excessive Trust in Client-Side Controls**. When a user adds an item to their cart, the web application includes the price of the item as a parameter in the HTTP request. 

Instead of the server checking its own database to verify the price of `productId=1`, it blindly trusts the `price` parameter sent by the user's browser. An attacker can intercept this request and modify the price to any arbitrary value.

---

## My Approach

### Step 1 – Initial Exploration and Login
I logged in using the provided credentials:
* **Username:** `wiener`
* **Password:** `peter`

I noticed the target item, the **"Lightweight l33t leather jacket"**, is listed at **$1337.00**. Checking my account balance, I only have **$100.00**, which is insufficient for a legitimate purchase.

### Step 2 – Testing Basic Input Validation
Before jumping into Intercept, I tried a simple client-side test. I attempted to change the quantity to a negative number (e.g., `-1`) in the UI. The application blocked this, prompting that the value must be greater than 0. This suggests there is some validation, but it might only be on the surface.

### Step 3 – Intercepting the "Add to Cart" Request
I turned on **Burp Suite Intercept** and clicked "Add to cart" for the leather jacket. I examined the POST request sent to `/cart`:

```http
POST /cart HTTP/1.1
Host: [lab-id].web-security-academy.net
...

productId=1&redir=PRODUCT&quantity=1&price=133700
```

### Step 4 – Identifying the Flaw
Looking at the parameters, I saw `price=133700` (represented in cents). This is a major red flag. The price should never be passed from the client; the server should already know the price associated with `productId=1`.

### Step 5 – Manipulating the Price
I modified the `price` parameter in the intercepted request to a much lower value:

`price=10`

I then forwarded the request to the server.

### Step 6 – Verifying and Checkout
I navigated to my cart. The "Lightweight l33t leather jacket" was now listed with a total price of **$0.10**. 

Since this was well within my $100.00 credit limit, I proceeded to **Place Order**. The transaction was successful, and the lab was solved.

---

## Intuition

The "logic" of a secure application should be: 
> *Client says: "I want Item A" -> Server checks database for Item A's price -> Server calculates total.*

In this vulnerable application, the logic was:
> *Client says: "I want Item A and it costs $0.10" -> Server says: "Okay, if you say so!"*

Whenever you see sensitive data like **prices, discount rates, or user roles** being passed as editable parameters in a request, it is an immediate candidate for tampering.

---

## Tools Used

- **Burp Suite Professional/Community** (Intercept and Repeater)
- **Built-in Browser**

---

## Vulnerability Category

**Business Logic Vulnerabilities**

This specifically falls under:
- **Trusting Client-Side Input**

---

## Key Takeaway

Never trust the client. All security-critical data, especially financial values, must be defined and validated **exclusively on the server side**. The client should only provide the identifiers (like a Product ID), and the server should fetch the associated "source of truth" from its own database.

## Impact

The impact is direct financial loss for the business. An attacker can purchase any inventory for a price of their choosing, potentially draining the store's stock for pennies.
