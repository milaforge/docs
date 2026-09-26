---
description: >-
  How I diagnosed an MVP reliability problem and changed failure behavior from
  application crashes to controlled recovery. From 65% to 92% Crash-Free
  Sessions.
---

# From Crashes to Reliable Gameplay

### Context

When I joined REVISION, the team had recently launched the MVP of its mobile gaming product.

One problem became visible very quickly: when something unexpected happened during a game session, the application would often terminate completely.

For the user, a recoverable error and a catastrophic failure looked the same:

**the game closed.**

The telemetry confirmed that this was not an occasional edge case.

Google's crash reporting dashboard showed only about **65% crash-free sessions**.

In other words, roughly <mark style="color:$danger;">one in three sessions was experiencing a crash.</mark>

***

### Finding the failure pattern

I collected and reviewed the available crash logs to understand what was actually failing.

The recurring pattern was not one isolated defect.

The codebase had largely been written around the **happy path**.

When an operation succeeded, everything worked as expected. But many failure conditions had no explicit behavior defined for them.

Unexpected exceptions could therefore propagate until the application crashed.

That shifted the problem from:

> Which individual bugs should I fix?

to:

> <mark style="color:$success;">How should the application behave when something goes wrong?</mark>

That distinction mattered.

Fixing individual crashes would address known failures.

Defining failure behavior would also make the system more resilient to failures we had not seen yet.

***

## Redesigning failure behavior

I refactored the affected code paths so failures were handled according to their context rather than allowing exceptions to terminate the session.

Depending on the operation, the application could now:

#### Retry

If the failure was temporary and the operation was safe to repeat, retry it instead of immediately failing.

```
operation
   │
   ├── success → continue
   │
   └── transient failure
              │
              ▼
             retry
```

#### Ignore safely

Some failures did not justify interrupting the user's session.

When an operation was non-critical and safe to skip, the application could record the failure and continue.

#### Stop gracefully

If continuing would produce an invalid or unsafe state, the application stopped the affected process deliberately.

But instead of crashing, it informed the user with a clear message.

```
Before

unexpected condition
        ↓
unhandled exception
        ↓
application crash
```

became:

```
After

unexpected condition
        ↓
classify failure
        │
        ├── retry when recoverable
        ├── ignore when non-critical
        └── stop safely + inform the user
```

The goal was not to hide errors.

It was to make each failure mode **intentional**.

***

## Outcome

After the refactoring, Google's crash reporting showed crash-free sessions increasing from approximately:

**65% → up to 92%**

That is a **27 percentage-point improvement** in crash-free sessions.

Viewed from the opposite direction, the observed crash rate fell from roughly:

**35% → 8%**

or about a **77% reduction in sessions experiencing crashes**.

More importantly, many failures that previously terminated the application now became controlled states the application could recover from or explain to the user.

***

## What changed

The technical changes were relatively straightforward individually:

* handle expected exceptions explicitly,
* retry transient failures where safe,
* tolerate non-critical failures,
* prevent exceptions from propagating unnecessarily,
* stop invalid workflows deliberately,
* give users actionable failure messages.

The larger change was architectural thinking.

The original MVP mostly answered:

> What should happen when everything works?

The refactored system also answered:

> What should happen when each dependency or assumption fails?

For a production system, both questions are part of the feature.

***

## Takeaway

Reliability is not just about preventing errors.

Errors are inevitable.

The important engineering decision is **what the system does when they occur**.

At Revision, treating failure paths as first-class behavior helped move crash-free sessions from roughly **65% to as high as 92%**.

The lesson I carried forward was simple:

**The happy path defines whether a feature works. The failure paths define whether users can trust it.**
