---
icon: crab
---

# Rust

### NEAR Threshold Signatures

> Contributions to a production threshold-signature implementation, spanning cryptographic performance, protocol correctness, Rust type design, testing, and security.

**Performance optimization**

* Batch Lagrange coefficient computation — [#50](https://github.com/near/threshold-signatures/pull/50)
  * \~5× faster coefficient computation / \~80× faster batch inversion

**Protocol correctness**

* More specific initialization errors — [#52](https://github.com/near/threshold-signatures/pull/52)
* `AppId` type-safety and `Arc` optimization — [#64](https://github.com/near/threshold-signatures/pull/64)

**Systems & Rust**

* Safer threshold calculation — [#132](https://github.com/near/threshold-signatures/pull/132)

**Testing & Engineering Quality**

* Protocol edge cases — [#85](https://github.com/near/threshold-signatures/pull/85)
* Debug-error handling — [#79](https://github.com/near/threshold-signatures/pull/79)
* Automated Rust checks — [#70](https://github.com/near/threshold-signatures/pull/70)
* `cargo-deny` checks — [#212](https://github.com/near/threshold-signatures/pull/212)

**Security**

[#id-3.-near-cryptographic-security-work](../security-engineering/security-research-and-auditing.md#id-3.-near-cryptographic-security-work "mention")
