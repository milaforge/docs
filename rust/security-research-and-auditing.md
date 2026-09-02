---
description: The Rust Implementation of the libp2p networking stack.
---

# LibP2P

### **Published vulnerability disclosure**

**`CVE-2026-35457`**

* External security advisory
* CVSS 8.2 High
* Protocol-level resource exhaustion
* Unbounded Protocol State → Remote Memory Exhaustion
* Reported to [rust-libp2p](https://github.com/libp2p/rust-libp2p) and resolved in [commit 8fde2dc0f](https://github.com/libp2p/rust-libp2p/commit/8fde2dc0fae8b433f97c6cdf9ee24f59d51a359c#diff-9a3aa3d836c3c67c69003d45425b9cadee0b4bad805ebbfe2c9039755d01fd63R44-R395)

> Discovered an unauthenticated resource-exhaustion vulnerability in the libp2p rendezvous protocol caused by unbounded pagination cookie storage.

\[[Read Github Advisory](https://github.com/libp2p/rust-libp2p/security/advisories/GHSA-v5hw-cv9c-rpg7)]
