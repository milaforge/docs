---
description: >-
  What a trust boundary is in threat modeling, including trust vs privilege,
  internal services, queues, and security architecture,where to find one in a
  software system, and why it matters for security.
icon: memo
tags:
  - architecture
  - security
---

# Trust Boundaries

I used to think of a **trust boundary** as a simple line between trusted and untrusted parts of a system. That definition is useful, but I found it too limited when looking at real systems.

What I have learned is that a trust boundary is better understood as **a point where the security assumptions about data, identity, or authority change**.

That makes the concept much more useful when I am trying to understand the security architecture of an unfamiliar system.

### Example

Imagine an API receiving traffic from the internet:

<pre><code>                    UNTRUSTED
                        |
                     Internet
                        |
                        | HTTP request
                        v
              =====================
<strong>                 TRUST BOUNDARY
</strong>              =====================
                        |
                        v
                       API
</code></pre>

The important part is not the line itself.

The important question is:

> What does the API need to establish before it can safely use this request?

The request may contain an authenticated identity, but that does not mean everything in the request should be trusted.

For example, knowing that a request comes from Alice does not mean Alice is allowed to access every resource or perform every operation.

This helped me separate three things that I used to mixed together:

```
Authentication  → Who is this?
Authorization   → What can they do?
Trust           → What assumptions can I safely make?
```

They are related, but they are not the same thing.

### Trust is not binary

One of the more useful things I learned is that "trusted" and "untrusted" are often too simple.

Consider:

<pre><code>Internet
   |
   | untrusted input
   v
========================
<strong>    TRUST BOUNDARY
</strong>========================
   |
   v
Public API (will authenticate)
   |
   | authenticated service request
   v
========================
<strong>    TRUST BOUNDARY
</strong>========================
   |
   v
Internal Service (will authorize)
</code></pre>

The internal service might trust the API's identity.

But it might still need to validate the request data.

So the real assumption could be:

> "I trust that this request came from the API, but I do not necessarily trust everything the API sends."

That is a much more accurate way for me to think about trust.

### "Internal" does not mean "trusted"

This is one of the assumptions I now try to question.

A system might look like this:

<pre><code>Internet
   |
   v
Public API
   |
   | internal request
   v
========================
    TRUST BOUNDARY
========================
   |
   v
<strong>Internal Service
</strong></code></pre>

Both services may be inside the same private network but that does not tell whether the internal service should trust the API's requests.

The internal service might know:

> "This request came from our API."

But that leaves several questions unanswered:

* Was the user authenticated correctly?
* Was the user authorized to perform this operation?
* Did the API validate the input?
* Can the API itself be compromised?
* Is the API allowed to request **this particular operation**?
* Is the resource being accessed actually allowed for this user?

I want to know what assumptions the internal service actually makes.

> **I need to know exactly what the internal service is trusting.**

It might trust:

> "This request came from our API."

But not:

> "Everything in this request is authorized and safe."

If it assumes that the API has already performed authorization, then compromising the API may also compromise that assumption.

> The network location is not enough to define the trust model.

### Queues made the concept clearer for me

Queues are another example where I found the simple "internal = trusted" model misleading.

For example:

```
User
 |
 v
API
 |
 | job
 v
Queue
 |
 | job
 v
========================
    TRUST BOUNDARY
========================
 |
 v
Worker
 |
 | privileged credentials
 v
Production Storage
```

The worker may have permissions that the user does not have.

The job, however, may contain information that ultimately came from the user.

That means I cannot assume:

> "The worker can trust the job because it came from an internal queue."

I need to consider the whole path:

```
user-controlled input
        |
        v
       API
        |
        v
      Queue
        |
        v
 privileged Worker
        |
        v
production resource
```

The interesting security question is what prevents low-trust input from controlling a high-privilege operation.

### Trust boundary vs privilege boundary

I also found it useful to separate **trust boundaries** from **privilege boundaries**.

A trust boundary is about changing security assumptions.

A privilege boundary is about changing authority.

For example:

<pre><code>User
 |
 | request
 v
API
 |
 | service credentials
 v
========================
   PRIVILEGE BOUNDARY
========================
 |
 v
Worker
 |
 | cloud credentials
 v
<strong>Production Storage
</strong></code></pre>

The worker may have much greater authority than the user.

So I want to understand not only:

> "Does the worker trust this request?"

but also:

> <mark style="color:$danger;">"What can the worker do if this request is malicious?"</mark>

These two questions often lead to different findings.

### A service can trust identity but not authority

Considering service-to-service authentication:

```
API
 |
 | mTLS / service identity
 v
========================
    TRUST BOUNDARY
========================
 |
 v
Worker
```

The worker may know that the request really came from the API.

That still does not answer:

* Is the API allowed to request this operation?
* Is the user allowed to perform it?
* Is the requested resource valid?
* Are the parameters safe?

This was an important distinction for me because authentication can create a false sense that the entire request is now trusted.

### Third-party services are another boundary

I also treat external services as separate trust domains rather than extensions of my own application.

For example:

```
My Application
      |
      | API request
      v
========================
    TRUST BOUNDARY
========================
      |
      v
Third-Party Service
```

The interesting part is not only what data I send out.

I also need to consider what comes back:

```
Third-Party Service
      |
      | response
      v
========================
    TRUST BOUNDARY
========================
      |
      v
My Application
      |
      v
Sensitive Operation
```

A response from an external system should not automatically become trusted input just because it came from an API I use.

### The same idea applies to privileged tools

I find this particularly relevant when thinking about systems where an agent or service can call powerful tools:

```
User / External Content
          |
          | untrusted content
          v
========================
    TRUST BOUNDARY
========================
          |
          v
        Agent
          |
          | tool call
          v
========================
  PRIVILEGE BOUNDARY
========================
          |
          v
Privileged Operation
```

The important question becomes:

> Can untrusted content influence a privileged action?

That is a more useful security question than simply asking whether the components are "internal."

### How I recover trust boundaries in an unfamiliar system

When I am looking at a system I did not build, I do not expect the trust model to be documented clearly.

I usually reconstruct it from the data flows.

I start with things such as:

```
HTTP endpoints
Webhooks
Message consumers
File uploads
External APIs
Authentication middleware
Service credentials
Cloud roles
Database permissions
```

Then I follow important data flows through the system.

For each transition, I try to understand:

```
Who controls the input?
        |
        v
Who receives it?
        |
        v
What does the receiver assume?
        |
        v
What authority does it have?
        |
        v
What happens if the assumption is wrong?
```

This gives me a much better security picture than simply looking at which services communicate with each other.

### The question that helps me find the boundary

The most useful question I have found is:

> **What would have to be true for this component to safely accept this input?**

Then I look for who establishes that property.

For example:

```
API
 |
 | job
 v
Queue
 |
 | job
 v
Worker

What must be true?

- The producer is legitimate.
- The job structure is valid.
- The requested operation is allowed.
- The target resource is allowed.
- The worker's privileges are appropriate.
```

Each assumption can become a security boundary worth examining.

### What I now look for

When I look at an architecture from a security perspective, I no longer stop at:

> "What talks to what?"

I also look for:

```
Where does untrusted data enter?
             |
             v
Where does trust change?
             |
             v
Where does privilege increase?
             |
             v
Where can that input affect a sensitive operation?
```

That gives me a much more useful mental model of the system.

A normal architecture describes **connections**.

A threat model also describes **assumptions, trust boundaries, privileges, and what could happen when those assumptions fail**.

That is the part of trust boundaries I found most useful when learning how to recover the security architecture of an unfamiliar system.
