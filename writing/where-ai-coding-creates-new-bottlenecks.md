---
description: >-
  AI coding tools generate code faster, but review, testing, integration, and
  release can become the new software delivery bottlenecks.
icon: file-lines
tags:
  - engineering
---

# Where AI Coding Creates New Bottlenecks

AI coding tools can make writing software much faster.

But I think there is an important distinction between **writing code faster** and **shipping useful software faster**.

They are not the same thing.

A developer can produce a feature in half the time, but that feature still needs to be understood, reviewed, tested, integrated, deployed, and eventually used by someone.

As code generation gets faster, those other parts of the system become more important.

### Coding output

> Coding output is not software delivery

Recent research data makes this difference clear:

A 2026 study of more than 500,000 GitHub developers found large increases in coding activity as developers adopted newer generations of AI coding tools.

But those gains became much smaller further down the delivery pipeline:

| Activity          | Reported cumulative effect |
| ----------------- | -------------------------: |
| Coding activity   |                      +240% |
| Software projects |                       +80% |
| Software releases |                       +30% |

The exact percentages are less important to me than the pattern.

> **Producing more code did not produce the same increase in released software.**

The researchers describe this as a weak-link problem: speeding up one part of a production system does not help as much when another part remains constrained.

### The bottleneck moves

Suppose a team previously spent significant time implementing a feature.

AI coding agents may reduce that implementation time substantially.

Now the feature reaches code review sooner.

But someone still has to determine:

* Does the change actually solve the requirement?
* Does it fit the existing architecture?
* What happens on failure paths?
* Did it introduce a security problem?
* Are the tests meaningful?
* Could it break another part of the system?

If code arrives faster than it can be reviewed, the review queue grows.

The team has increased **output**, but not necessarily **throughput**.

The same problem can happen further down the pipeline.

```
Requirement
    |
    v
Implementation   <- AI makes this much faster
    |
    v
Code review      <- bottleneck may move here
    |
    v
Testing
    |
    v
Integration
    |
    v
Deployment
    |
    v
Production
    |
    v
Useful outcome
```

Optimizing only the implementation step does not optimize the whole system.

### Code review

> Code review becomes more important

AI-generated code still has to be understood.

That matters because reviewing code is not simply checking whether it compiles.

A reviewer may need to reconstruct the reasoning behind a change, understand its interactions with the rest of the system, and decide whether the implementation is appropriate.

This creates an interesting asymmetry.

Generating 500 lines of code can take an agent seconds.

Understanding whether those 500 lines should exist can take much longer.

A separate 2026 study of an organization aggressively adopting AI coding tools found that developer throughput eventually doubled, while the amount of review work per reviewer also roughly doubled.

Automation absorbed some of that review work, but the underlying constraint did not disappear.

For me, this is one of the important consequences of AI coding tools: **verification becomes relatively more valuable as generation becomes cheaper.**

### Testing

> Testing has the same problem

AI can generate tests quickly.

But generating tests and knowing that the system is adequately tested are different problems.

For example, an agent implementing a payment workflow might generate tests for:

```
valid payment
    ->
payment accepted
```

That does not tell me whether it considered:

```
payment accepted twice
network timeout after payment succeeds
duplicate webhook
partial database failure
incorrect account
retry after ambiguous response
```

The difficult part is often identifying **what needs to be tested**, not typing the test code.

AI can help execute that work. It does not remove the need to reason about failure modes.

### Integration

> Integration becomes more important as changes become cheaper

A feature rarely exists alone.

It interacts with:

* existing APIs
* databases
* authentication
* third-party services
* deployment environments
* other developers' changes
* old assumptions embedded in the system

An AI agent may understand the code directly in front of it very well while still missing a constraint elsewhere.

This is especially important in older systems.

Writing a new implementation can be easy. Understanding why the existing implementation looks strange can be much harder.

Sometimes that strange code represents years of accumulated business rules, production failures, compatibility requirements, or workarounds.

Replacing it quickly is not necessarily progress.

### Release&#x20;

> Release processes can become the next constraint

Even reviewed and tested code still has to reach production.

If deployment requires manual coordination, fragile release procedures, or several approval steps, faster implementation only causes changes to arrive at that boundary faster.

So when AI increases development speed, I would look at the entire path:

```
idea
  ->
implementation
  ->
review
  ->
test
  ->
integration
  ->
deployment
  ->
production
  ->
user outcome
```

Then I would ask:

> Where does work spend most of its time waiting?

That is probably a more useful question than:

> How much code are developers producing?

### What to measure

> What I would measure instead of code volume

Metrics such as lines of code, commits, and pull requests can all increase without users receiving more value.

I would rather look at measurements closer to the complete delivery system:

* time from starting a change to production
* time waiting for code review
* deployment frequency
* failed deployment or change rate
* recovery time after failures
* time from an idea to something a user can actually use

These measurements are not perfect either.

But they are harder to improve simply by generating more code.

### What this means

> What this means for a small company or startup

For a small team, I do not think the lesson is to use AI less.

The opposite may be true.

AI coding tools can make a small engineering team much more capable.

But faster implementation should change where engineering effort goes.

If implementation becomes cheaper, I would spend more attention on:

1.  **Defining the right thing to build**

    Generating the wrong feature faster has little value.
2.  **Keeping changes small**

    Smaller changes are easier to understand, review, test, and reverse.
3.  **Automating deterministic checks**

    Formatting, static analysis, type checking, tests, dependency checks, and other repeatable verification should happen automatically.
4.  **Improving integration tests**

    Unit tests can tell me individual pieces work. Integration tests help tell me whether the system still works when those pieces interact.
5.  **Making releases boring**

    A reliable CI/CD pipeline lets increased development speed reach production instead of stopping at a manual deployment process.
6.  **Keeping humans focused on judgment**

    Architecture, trade-offs, product intent, security assumptions, and unusual failure modes are where human attention remains especially useful.

### Constraint changes

> AI changes the constraint, not the need for engineering

I see that AI coding tools are effective.

AI has become effective enough at generating software that constraints elsewhere in software development are becoming easier to see.

Before AI, implementation itself consumed a large amount of engineering time.

As that cost falls, review, integration, testing, release processes, and engineering judgment account for a larger share of the remaining work.

The bottleneck moves.

And once it moves, optimizing the old bottleneck produces diminishing returns.

That is why I would not judge an AI-assisted engineering team primarily by how much more code it produces.

I would ask a more useful question:

> **Are we getting reliable software into users' hands faster?**

If the answer is no, generating even more code is probably not the first problem I would solve.

### Sources

* Mert Demirer, Leon Musolff, Liyuan Yang — _Writing Code vs. Shipping Code: Productivity Effects Across Generations of AI Coding Tools_. NBER Working Paper 35275, revised September 2026. DOI: `10.3386/w35275`.
* Leon Musolff / Knowledge at Wharton — _AI Is Producing More Software. Why Isn’t It Being Used?_
* Hao He et al. — _AI Writes Faster Than Humans Can Review: A Longitudinal Study of an Enterprise 2x Mandate_. arXiv:2607.01904.
