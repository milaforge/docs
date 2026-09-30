---
description: Roughly 10× measured realtime capacity on the same server budget.
---

# 10× Capacity at the Same Cost

**Maturity: production, following an MVP launch.**

As Revision's backend lead, I investigated intermittent failures in the mobile game's realtime messaging service. The same investigation led to a transport change with roughly 10× measured capacity on the same server budget.

## Make the failure repeatable

Errors appeared during peak traffic, but the trigger was unclear. I built a load-testing harness to simulate concurrent WebSocket traffic. It reproduced a race condition in shared-state reads and writes.

I redesigned the affected data structures and access paths. The failure stopped reproducing under the same stress conditions.

## Test the next assumption

With the correctness issue addressed, I used the harness to test whether the transport was limiting capacity. The product used only a small part of Socket.IO's functionality.

I compared it with uWebSockets.js using the same workload model and server budget. The alternative sustained approximately **10× the capacity** in that benchmark.

## Migrate on evidence

That result justified migration. I adapted the application communication layer while preserving the messaging behavior the product needed.

The result is specific to this workload and infrastructure. It does not establish a universal library comparison or a 10× increase in live users.
