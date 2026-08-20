---
description: >-
  Understand what it takes to move an MVP to production, including architecture,
  security, testing, deployment, monitoring, reliability, and maintainability.
tags:
  - technical-founder
---

# MVP to Production: What Changes After Your MVP Works

An [**MVP**](../) proves that a product can work.

**Production software** needs to keep working when real users, real data, failures, and continuous changes are introduced.

Moving from MVP to production is therefore not simply about adding more features. It means reviewing the shortcuts taken during development and making the system sufficiently secure, reliable, maintainable, and operable for its intended use.

### MVP vs. Production

An MVP is usually optimized for **speed and learning**.

At this stage, it can be reasonable to:

* keep the architecture simple
* manually perform some operations
* rely on manual testing
* use temporary solutions
* postpone some automation
* accept technical debt
* optimize for learning rather than scale

These decisions are not necessarily mistakes. An MVP has a different purpose.

The problem occurs when those same shortcuts remain in place after the product begins to handle real users and business-critical data.

Production readiness means identifying which shortcuts have become risks and addressing them according to the actual requirements of the product.

### What Changes?

#### 1. Architecture

The system should have a structure that can be understood, maintained, and extended without constantly breaking existing functionality.

This may involve reviewing:

* Database design
* Application structure
* API boundaries
* Data flows
* Error handling
* External integrations
* Performance bottlenecks
* Dependency choices

The goal is **not** to build a complicated architecture.

A small SaaS does not automatically need microservices, Kubernetes, or a distributed architecture.

The goal is to build an architecture appropriate for the product's expected users, data, failure modes, and future changes.

#### 2. Security

An MVP may have basic security controls. Production software needs deliberate protection of users, data, and infrastructure.

Typical areas include:

* Authentication and authorization
* Access controls and least privilege
* Input validation
* Secrets and credentials
* Rate limiting
* Dependency and vulnerability management
* Data protection
* Secure configuration
* Logging and security monitoring

Security should be considered part of the system's design rather than something added after an incident.

#### 3. Testing

An MVP can rely heavily on manual testing.

As the product grows, this becomes increasingly risky because a change in one part of the system can break another.

Production readiness does not mean achieving an arbitrary percentage of test coverage.

The more useful question is:

> **Can we change critical parts of the product without being afraid of breaking them?**

This usually requires automated tests around important business logic, integrations, and user journeys.

#### 4. Deployment

Production needs a repeatable way to release new versions.

This usually means establishing:

* Separate environments where appropriate
* Automated builds and deployments
* Database migration procedures
* Configuration management
* Secret management
* Deployment validation
* Rollback or recovery procedures

A deployment should become a controlled and repeatable process rather than a sequence of manual steps that only one person understands.

#### 5. Monitoring and Reliability

When real users depend on the product, discovering that something is broken is not enough.

You need to know:

* **What broke?**
* **Who is affected?**
* **How severe is it?**
* **Why did it happen?**
* **How can it be recovered?**

Depending on the product, this can include:

* Application and infrastructure monitoring
* Error tracking
* Logs
* Performance metrics
* Alerts
* Backups
* Health checks
* Recovery procedures

Production engineering is partly about preparing for the situations where the system does **not** behave as expected.

#### 6. Maintainability

The MVP was built to learn quickly.

Production software also needs to be understandable and changeable over time.

That means:

* Reducing unnecessary technical debt
* Removing fragile workarounds
* Keeping dependencies manageable
* Documenting important technical decisions
* Establishing clear code ownership
* Keeping development and deployment processes understandable

The objective is not perfect code.

The objective is a system that another engineer can safely understand and modify.

### What If the MVP Was Built With AI?

AI-assisted development can make it dramatically faster to turn an idea into a working application.

That is useful for MVP development.

But generating code that works is different from taking responsibility for a production system.

AI-generated or AI-assisted code can contain the same problems as manually written code, including:

* Inconsistent architecture
* Duplicated logic
* Missing error handling
* Weak security assumptions
* Unnecessary dependencies
* Fragile integrations
* Insufficient tests
* Unclear data flows
* Difficult-to-maintain abstractions

The important distinction is not:

> **AI code is bad and human code is good.**

That is too simplistic.

The distinction is:

> **Generating a working application and engineering a system that can be operated safely are different activities.**

An engineer needs to understand what the system does, identify its assumptions and failure modes, validate its security boundaries, establish appropriate tests and observability, and make deliberate decisions about how it should evolve.

AI can assist with much of this work.

It does not remove the need for someone to **own the engineering decisions and their consequences**.

### Do You Need to Rebuild the MVP?

Usually, **no**.

A working MVP contains valuable information about the product and its users. Throwing it away simply because it was built quickly can discard useful work.

A better approach is to identify which parts of the existing system create unacceptable risk and improve them.

That may involve:

* Refactoring specific components
* Replacing fragile implementations
* Improving the database design
* Introducing automated tests
* Hardening authentication and authorization
* Improving deployment
* Adding monitoring
* Replacing unsuitable dependencies

Sometimes the existing architecture genuinely cannot support the product's requirements. In that case, significant parts—or occasionally all—of the system may need to be redesigned.

The decision should be based on **technical constraints and business requirements**, not on the assumption that production software must be completely rebuilt.

### Is Your MVP Ready for Production?

There is no universal checklist that makes every application production-ready.

A small internal tool and a financial application have very different requirements.

However, these questions are useful starting points:

* Can we deploy the application reliably?
* Can we recover from a failed deployment?
* What happens when an external service becomes unavailable?
* Can users access data they should not be able to access?
* Are critical user journeys tested?
* Do we know when something breaks?
* Can we determine why an incident occurred?
* Can we restore important data?
* Are secrets and credentials handled securely?
* Can another engineer understand the codebase?
* Can we safely modify critical functionality?
* Is someone clearly accountable for the technical system?

If several of these questions do not have clear answers, the MVP probably needs engineering work before it becomes a dependable production system.

### Who Should Handle This Work?

The required role depends on the complexity and stage of the product.

A **full-stack software engineer** can often take a straightforward MVP toward production and own its implementation, deployment, and ongoing technical work.

A **founding engineer** may be appropriate when the product needs a technical owner who works closely with the founders and takes substantial responsibility for the system.

A **technical co-founder or CTO** becomes more relevant when the company needs someone to own broader technical strategy, architecture, infrastructure, security, hiring, and long-term technical decisions.

The title matters less than the responsibility.

Someone experienced needs to be accountable for making the technical system **secure, reliable, maintainable, and appropriate for the business**.

### The Practical Goal

Moving from MVP to production means changing the question from:

> **"Does it work?"**

to:

> **"Can real users depend on it?"**

That shift is what turns an MVP into production software.

If you already have an MVP and are unsure what needs to change before real users depend on it, [get in touch](../../../welcome/contact.md) to discuss the technical next steps.
