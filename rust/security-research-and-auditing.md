---
description: A remote memory-exhaustion flaw reproduced, disclosed, and patched upstream.
---

# LibP2P

**Maturity: released library; fix shipped in libp2p-rendezvous 0.17.1.**

## Question the resource bound

Could an unauthenticated peer exhaust a rendezvous server using valid requests? I traced how discovery requests created and retained pagination cookies, then reproduced unbounded state growth.

The risk was availability: each request could add server-side state without a storage limit or eviction policy. Protocol-valid input could still exhaust memory.

## Disclose and bound the state

I reported the finding privately. The [public advisory](https://github.com/libp2p/rust-libp2p/security/advisories/GHSA-v5hw-cv9c-rpg7) credits me as reporter and records **CVE-2026-35457**, patched in **0.17.1**.

Maintainers [replaced unbounded cookie storage with a bounded LRU cache](https://github.com/libp2p/rust-libp2p/commit/8fde2dc0fae8b433f97c6cdf9ee24f59d51a359c). Eviction limits retained state, with the tradeoff that older pagination cookies may no longer be available.

The evidence establishes a disclosed vulnerability and an upstream fix. Exposure depends on running the affected rendezvous service with untrusted peers; it does not establish impact on every libp2p-based application.

[Read the technical walkthrough →](../software-engineering/finding-security-vulnerability-in-libp2p.md)

## Related contribution

I also redacted pre-shared keys from debug output to prevent accidental disclosure, merged in [PR #6490](https://github.com/libp2p/rust-libp2p/pull/6490).
