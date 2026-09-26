---
description: >-
  Diagnosing a concurrency bug and replacing Socket.IO with uWebSockets.js to
  achieve ~10× higher realtime capacity.
---

# 10× Capacity at the Same Cost

**How investigating an intermittent production failure led me to build a reproducible load-testing harness, fix a concurrency bug, question our WebSocket stack, and increase backend communication capacity by roughly 10× without increasing server cost.**

### Context

I joined REVISION leading the backend for a high-traffic mobile gaming platform.

One part of the _Node.js_ backend handled real-time, bidirectional communication for features such as:

* private chat,
* group chat,
* and other basic in-game messaging.

The _MVP_ had been built with **Socket.IO**.

It was a reasonable choice early on: mature, convenient, and fast to ship.

But after joining the team, I noticed an intermittent production error appearing in the logs during peak traffic.

There was no obvious deterministic trigger.

Most of the time, the system behaved normally.

As concurrent usage increased, some customers started experiencing failures.

***

## First problem: reproduce the production failure

Looking at individual errors was not enough.

The strongest correlation I could find was **concurrency**: the problem appeared as the number of simultaneous users increased.

So instead of continuing to debug production incidents one by one, I built a simulation and load-testing harness that could create controlled concurrent WebSocket traffic.

That gave me something I did not previously have:

> <mark style="color:$success;">a repeatable way to make the production failure happen on demand.</mark>

Once the system was under controlled load, the issue stopped being mysterious.

It was a **race condition**.

The problem was in how shared state was structured, accessed, and written when multiple operations happened concurrently.

I redesigned the affected data structures and changed the associated read/write paths.

After the fix, the failure no longer reproduced under the same stress conditions.

***

## The debugging tool became a scaling tool

At that point, the immediate production issue was solved.

But the test harness had given me something more valuable: a way to measure the real capacity of our realtime backend.

That raised another question.

_Socket.IO_ offered significantly more functionality out of the box, than our gaming use case required.

Our communication model was comparatively simple:

```
client
  ↕
persistent bidirectional connection
  ↕
Node.js backend
```

We mainly needed efficient connection handling and message delivery.

So I asked:

> How much capacity are we giving up for abstractions and features we are not using?

Instead of guessing, I used the same load-testing harness to measure it.

***

## Benchmarking the transport layer

I evaluated [**uWebSockets.js**](https://github.com/uNetworking/uWebSockets) as a lower-overhead alternative.

Before considering a migration, I first checked that the project was established enough for us to reasonably depend on and then tested it against our actual communication pattern.

The important part was keeping the comparison meaningful.

I used the same workload model and server budget and compared how much concurrent communication the backend could sustain before reaching its practical capacity.

The difference was large.

Under my benchmark workload, the uWebSockets.js-based implementation sustained approximately **10× the capacity** of the existing Socket.IO implementation on the same infrastructure budget.

```
Same server resources
Same application use case
Comparable traffic pattern

Socket.IO
████

uWebSockets.js
████████████████████████████████████████
                         ~10× capacity
```

The result was large enough that this was no longer a micro-optimization.

It changed the economics of scaling the realtime backend.

***

## Migrating the realtime layer

A benchmark alone was not enough reason to change production infrastructure.

_Socket.IO_ was already integrated into the application, so replacing it meant changing a working communication layer rather than introducing a new component in isolation.

I migrated the realtime backend from _Socket.IO_ to **uWebSockets.js**, adapting the application-level communication code to the lower-level transport.

The goal was not to reproduce every _Socket.IO_ capability.

It was to preserve the behavior our product actually depended on while removing unnecessary overhead.

After the migration, the backend could support roughly **10× the realtime workload without increasing hosting cost**.

***

## Outcome

The work produced two separate improvements.

### 1. The peak-time production failure disappeared

The load harness exposed a concurrency race that had previously appeared only intermittently in production.

Fixing the underlying data-access design removed that failure mode under the workloads where I had reproduced it.

### 2. Realtime capacity increased by roughly 10×

The same investigation gave me a repeatable benchmark for evaluating the communication stack.

Migrating from Socket.IO to uWebSockets.js increased measured capacity by approximately **10× on the same server budget**.

That meant scaling the realtime system did not require a corresponding increase in infrastructure cost.

***

## The more important result

The most valuable part of the work was not choosing a faster WebSocket library.

It was the sequence of decisions that made that choice defensible.

```
Intermittent production failure
          │
          ▼
Correlate failure with concurrency
          │
          ▼
Build reproducible load harness
          │
          ▼
Expose and fix race condition
          │
          ▼
Keep the harness as a benchmark
          │
          ▼
Question transport overhead
          │
          ▼
Compare alternatives under the same workload
          │
          ▼
Migrate only after measuring the difference
          │
          ▼
~10× capacity at roughly the same server cost
```

Without the test harness, replacing _Socket.IO_ would have been an architectural opinion.

With it, the decision became measurable.

***

## Takeaway

The production bug and the scaling problem initially looked unrelated.

They were connected by the same missing capability: **we could not reliably reproduce the system under realistic concurrency**.

Once I built that capability, I could use it first to diagnose correctness and then to test architecture.

The largest performance improvement did not come from tuning individual functions.

It came from measuring the system well enough to discover that one of its foundational abstractions was significantly more expensive than our use case required.
