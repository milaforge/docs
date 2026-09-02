---
description: Threshold signature schemes for the NEAR MPC system.
---

# Near Protocol

> Contributions to a production threshold-signature implementation, spanning cryptographic performance, protocol correctness, Rust type design, testing, and security.

## General

**Performance optimization**

* Batch Lagrange coefficient computation — [#50](https://github.com/near/threshold-signatures/pull/50)
  * \~5× faster coefficient computation / \~80× faster batch inversion

**Protocol correctness**

* More specific initialization errors — [#52](https://github.com/near/threshold-signatures/pull/52)
* `AppId` type-safety and `Arc` optimization — [#64](https://github.com/near/threshold-signatures/pull/64)

**Testing & Engineering Quality**

* Safer threshold calculation — [#132](https://github.com/near/threshold-signatures/pull/132)
* Protocol edge cases — [#85](https://github.com/near/threshold-signatures/pull/85)
* Debug-error handling — [#79](https://github.com/near/threshold-signatures/pull/79)
* Automated Rust checks — [#70](https://github.com/near/threshold-signatures/pull/70)
* `cargo-deny` checks — [#212](https://github.com/near/threshold-signatures/pull/212)

## Security

_**Waitpoint Flood DoS**_

I produced a reproducible proof of concept showing that an attacker could inject fake waitpoints and fill the message buffer.

This required me to:

> Protocol-level analysis → identify attack surface → [construct exploit](https://github.com/near/threshold-signatures/pull/186) → reproduce vulnerability → [communicate finding](https://github.com/near/threshold-signatures/issues/185)

***

**Timing Attack -&#x20;**_**Constant-Time Cryptography**_

I identified `variable-time` operations in cryptographic code and [addressed them](https://github.com/near/threshold-signatures/pull/80) using `constant-time` comparisons/checks.

This is a side-channel risk reduction at implementation leve&#x6C;**.**

***

**Malformed `AppId` Crash**

A [malformed input](https://github.com/near/threshold-signatures/issues/194) could crash a local MPC node.

This is adversarial input analysis and [robustness testing](https://github.com/near/threshold-signatures/pull/183).
