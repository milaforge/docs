---
description: Actual evidence of my security works.
---

# Security Research & Auditing

### **1. Published vulnerability disclosure**

`CVE-2026-35457`

* External security advisory
* CVSS 8.2 High
* Protocol-level resource exhaustion
* Unbounded Protocol State → Remote Memory Exhaustion
* Reported to [rust-libp2p](https://github.com/libp2p/rust-libp2p) and resolved in [commit 8fde2dc0f](https://github.com/libp2p/rust-libp2p/commit/8fde2dc0fae8b433f97c6cdf9ee24f59d51a359c#diff-9a3aa3d836c3c67c69003d45425b9cadee0b4bad805ebbfe2c9039755d01fd63R44-R395)

> Discovered an unauthenticated resource-exhaustion vulnerability in the libp2p rendezvous protocol caused by unbounded pagination cookie storage.

\[[Read Github Advisory](https://github.com/libp2p/rust-libp2p/security/advisories/GHSA-v5hw-cv9c-rpg7)]

***

### 2. NEAR threshold-signatures security research

_**Waitpoint Flood DoS**_

I produced a reproducible proof of concept showing that an attacker could inject fake waitpoints and fill the message buffer.

This required me to:

> Protocol-level analysis → identify attack surface → [construct exploit](https://github.com/near/threshold-signatures/pull/186) → reproduce vulnerability → [communicate finding](https://github.com/near/threshold-signatures/issues/185)

***

### 3. NEAR cryptographic security work

**Timing Attack -&#x20;**_**Constant-Time Cryptography**_

I identified `variable-time` operations in cryptographic code and [addressed them](https://github.com/near/threshold-signatures/pull/80) using `constant-time` comparisons/checks.

This is a side-channel risk reduction at implementation leve&#x6C;**.**

***

### **4. Other security/robustness work**

**Malformed `AppId` Crash**

A [malformed input](https://github.com/near/threshold-signatures/issues/194) could crash a local MPC node.

This is adversarial input analysis and [robustness testing](https://github.com/near/threshold-signatures/pull/183).

***

***
