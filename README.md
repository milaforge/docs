---
description: From uncertain software ideas to production, with technical risks tested early.
icon: shapes
layout:
  width: default
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
  metadata:
    visible: true
  tags:
    visible: true
  actions:
    visible: true
  anchors:
    visible: true
---

# Build

Milaforge helps teams turn uncertain software ideas into production-ready products through hands-on engineering. I build in small, useful steps, test key assumptions, and make decisions that account for correctness, security, and reliability.

[Talk about your project →](build/contact.md)

## Build

### BugDasht — security workflows in customer use

**Production · Lead backend engineer**

Companies needed a way to run bug-bounty programs; the workflows were still undefined. I shaped the scope with the founder and early users, then built the backend and operating foundation around permissions, payments, and auditable records. The platform reached customer use in about eight months and continued operating beyond launch.

[Read the case study →](case-studies/built-from-zero/bugdasht.md)

## Improve

### Revision — more realtime capacity on the same budget

**Production · Lead backend engineer**

Peak-time failures had no reliable trigger. I built a load harness, reproduced a race condition, and redesigned the affected state access. With that failure resolved under the same test load, I used the harness to compare transports. A roughly 10× capacity gain in the benchmark justified migrating the realtime backend.

[Read the case study →](case-studies/improved/10-capacity-at-the-same-cost.md)

## Secure

### rust-libp2p — a memory-exhaustion flaw disclosed and patched

**Released library · Security research**

Could valid network requests exhaust server memory? I reproduced unbounded pagination-state growth and reported it privately. The finding led to a public advisory; maintainers bounded cookie storage in the patched release.

[Read the case study →](rust/security-research-and-auditing.md)

[All case studies →](case-studies/README.md)
