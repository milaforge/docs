---
description: >-
  How I think about preventing security vulnerabilities through better software
  design, with practical examples of least privilege, rate limiting,
  whitelisting, and zero trust.
icon: memo
tags:
  - security
  - architecture
---

# Secure by Design: Before You Build

### Start Before Auth

In 2026, IBM X-Force reported that **56% of the vulnerabilities it tracked in 2025 could be exploited without authentication**.

That made me think about where security decisions actually begin.

If an attacker does not need an account, authentication cannot be the first line of defense.

So when I design a feature, I try to start with a different question:

> **What should this system allow in the first place?**

That is how I understand **secure by design**.

### Limit Resources

Suppose I am building an API that triggers an expensive computation.

My first question isn't:

> "Should the user be authenticated?"

I ask:

> **"How much compute should one request be allowed to consume?"**

The answer might be a fixed limit, a queue, a restricted set of operations, or a maximum execution time.

The important decision is the boundary around the capability.

<mark style="color:$success;">Even if the endpoint is public, an attacker should not automatically get unlimited access to my infrastructure</mark>.

### Rate Limit

Consider an API that sends emails or generates reports.

Authentication tells me who is making the request.

It doesn't tell me how many times they should be allowed to make it.

So I think about the operation itself:

> **How much of this operation should one client be able to consume?**

A rate limit, quota, or similar constraint makes that boundary explicit.

I don't want security to depend on every caller being trustworthy.

### Whitelisting

#### Whitelist What the System Can Reach

Suppose my application needs to connect to external services.

I could allow it to connect anywhere and try to detect dangerous destinations.

I prefer to ask:

> **What destinations does this application actually need?**

If the answer is five services, there is little reason to give it access to five million.

A whitelist turns that decision into a boundary:

> These destinations are allowed. Everything else is not.

This is often simpler than trying to recognize every possible bad destination.

### Least Privilege

Suppose a service only needs to read customer data.

I don't want to give it permission to delete customers simply because that permission might be convenient later.

I start with the smallest capability it needs.

That's the idea behind **least privilege**:

> **Give something only the access required to perform its job.**

If the service is compromised, its permissions become a limit on the damage it can cause.

### Verify Access

**Don't Trust by Default**

I also try not to treat something as trustworthy simply because it is "inside" my system.

A request coming from an internal service, network, or previously trusted component can still be wrong or compromised.

This is the thinking behind **zero trust**:

> **Verify the access that is actually needed instead of trusting based on where the request came from.**

The question becomes:

> Who or what is making this request, what are they trying to do, and should they be allowed to do it?

### The Common Pattern

These examples look different:

* limiting compute resources
* rate limiting APIs
* whitelisting destinations
* applying least privilege
* using zero-trust access

But I see the same pattern in all of them.

<mark style="color:$success;">**I reduce the capability before I need to defend the capability.**</mark>

I don't rely entirely on authentication to make a dangerous operation safe.

I don't give a service broad permissions and hope nothing goes wrong.

I don't expose unlimited resources and then try to detect abuse.

I <mark style="color:$success;">define the boundary first</mark>.

### Secure by Design

This is what secure by design means to me.

It is not a security feature that I add after the application works.

It is a design constraint I consider while deciding:

* what the system exposes
* what users and services can access
* how much they can consume
* where they can connect
* what happens when they behave unexpectedly

The practical question I keep coming back to is:

> **What capabilities am I creating, who gets them, and how much power should they have?**

I want those answers to exist **before the code does**.

That is where I think secure by design starts.
