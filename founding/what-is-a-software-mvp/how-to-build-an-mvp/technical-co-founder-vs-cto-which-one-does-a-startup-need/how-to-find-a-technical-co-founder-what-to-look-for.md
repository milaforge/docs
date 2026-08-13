---
description: >-
  A practical guide to finding a technical co-founder or startup CTO, with a
  focus on ownership, technical judgment, architecture, security, and
  decision-making.
tags:
  - technical-founder
---

# How to Find a Technical Co-Founder: What to Look For

If you have an idea and want to build the first version, test whether people actually want it, or turn an early prototype into a real product, you may reach a point where you need someone to own the technical side with you. That is where a technical co-founder comes into play. The challenge is not finding someone who can code, but finding someone who can take ownership of the technology, make decisions under uncertainty, and build alongside you.

Finding a technical co-founder is not primarily a hiring problem.

You are not looking for someone who can write code. You are looking for someone who can take ownership of an uncertain technical problem, make good decisions without constant direction, and stay accountable for the consequences.

That changes what you should look for.

### What to look for in a technical co-founder

#### 1. Start with the problem, not the technology

Before looking for a technical co-founder, define what you actually need them to own.

For example:

* building the first version of the product
* designing the architecture
* making security decisions
* dealing with infrastructure and reliability
* turning an unclear product idea into a technical plan
* eventually building and leading an engineering team

These require different strengths.

If you simply search for "a senior developer," you are likely to optimize for the wrong thing.

A technical co-founder should eventually be able to say:

> "This is my problem. I will figure out what needs to be done."

That is a much higher bar than being good at implementation.

#### 2. Look for ownership, not just technical ability

The most important distinction I would make is between **someone who completes tasks** and **someone who owns outcomes**.

A strong engineer might ask:

> "What should I implement?"

A strong technical co-founder is more likely to ask:

> "What are we trying to achieve, what constraints matter, and what is the simplest way to get there?"

That difference becomes especially important when there is no specification.

A co-founder will encounter incomplete requirements, changing priorities, technical debt, security concerns, operational failures, and decisions where there is no obviously correct answer.

You need someone who can operate in that environment without waiting for instructions.

#### 3. Evaluate technical judgment, not technology vocabulary

A technical co-founder does not need to know every framework.

They need to make good decisions about:

* what should be built
* what should not be built
* where complexity is justified
* where simplicity is safer
* when to use an existing solution
* when to build something yourself
* what can fail
* what happens when it fails

For example, security is not simply knowing how to use encryption or authentication libraries.

It is understanding questions such as:

* Who is allowed to access this resource?
* What happens if this credential is compromised?
* Should this component have this permission at all?
* What is the blast radius of this failure?
* Where should the trust boundary be?
* Can this operation be rate-limited?
* What happens when an external dependency behaves unexpectedly?

These decisions are architectural decisions, not merely implementation details.

[My own security work](../../../../security-engineering/security-research-and-auditing.md#id-2.-near-threshold-signatures-security-research) has increasingly followed this kind of reasoning: start from the resource, identify who should be allowed to access it, minimize privileges, and design controls around the actual failure modes rather than assuming the happy path.

#### 4. Look at what someone does when there is no obvious answer

One of the best ways to evaluate a potential co-founder is to look at their existing work.

Not their résumé.

Their work.

Open-source contributions, technical projects, architecture decisions, incident reports, research, code reviews, or difficult bugs can reveal much more.

For example, I have contributed to the NEAR threshold-signatures project. The interesting signal is not simply that code was contributed to an established repository. It is the process around **understanding an unfamiliar system**, **reviewing assumptions**, **identifying problems**, **discussing changes**, and **taking responsibility** for a technically sensitive area.

That is much closer to the work of a technical co-founder than completing a coding exercise.

Look for evidence that someone has previously:

* entered an unfamiliar codebase
* understood a system before changing it
* challenged an assumption
* found a security or reliability problem
* dealt with disagreement
* improved an existing implementation
* taken responsibility beyond their assigned task

These are stronger signals than knowing a particular programming language.

#### 5. See how they think about failure

A technical co-founder should naturally think about failure modes.

Ask questions such as:

**"What could go wrong with this architecture?"**

Then pay attention to the reasoning.

A weak answer usually focuses on implementation problems.

A stronger answer considers:

* security failures
* data loss
* dependency failures
* scaling limits
* operational failures
* incorrect assumptions
* abuse cases
* recovery
* observability
* the consequences of partial failure

This is one reason I consider security and architecture closely related.

Good engineering is not only about making the normal path work. It is about understanding what happens when the assumptions stop being true.

#### 6. Test whether they can make decisions under uncertainty

Early-stage companies rarely provide complete information.

A useful technical co-founder should be comfortable saying:

> "We don't know yet. Here is what I would test first."

That is better than pretending to know.

Give a candidate an ambiguous problem.

Do not give them the architecture.

Do not tell them which technology to use.

Instead, explain the business objective and constraints.

Then observe:

1. What questions do they ask?
2. What assumptions do they identify?
3. What risks do they notice?
4. What do they deliberately leave out?
5. How do they decide what to investigate?
6. Can they explain trade-offs clearly?

You are evaluating their **decision-making process**.

#### 7. Work together before making them a co-founder

A conversation can tell you whether you get along.

It cannot tell you whether you can build a company together.

Before making a long-term commitment, work on something real together.

It does not need to be a six-month project.

A small project can reveal:

* how they communicate
* how they handle disagreement
* whether they finish things
* how they respond to changing requirements
* whether they over-engineer
* whether they ask for help appropriately
* whether they take ownership when something breaks

The important part is that the project contains enough ambiguity to expose how both of you operate.

#### 8. Pay attention to what they take ownership of without being asked

This is one of the strongest signals.

Suppose a project has an authentication problem.

One person fixes the immediate bug.

Another person asks:

> Why did this happen?

Then they investigate the surrounding architecture, identify related risks, improve the design, add appropriate tests or controls, and document the decision.

The second person is demonstrating a different level of ownership.

The same applies outside security.

If the deployment is unreliable, do they just restart it?

Or do they investigate why deployments are unreliable and improve the system?

If the API is slow, do they optimize the slow endpoint?

Or do they determine whether the underlying architecture is creating the problem?

{% hint style="success" %}
A co-founder should naturally move toward the underlying problem.
{% endhint %}

#### 9. Do not confuse confidence with leadership

A technical co-founder does not need to be the loudest person in the room.

In fact, excessive certainty can be a warning sign.

Good technical judgment often sounds like:

> "I think this is the right approach, but these are the assumptions I am making."

Or:

> "I would not build this yet because we don't have evidence that we need it."

Or:

> "I was wrong about this. Here is what changed my mind."

{% hint style="success" %}
You want someone who can change their mind when the evidence changes.
{% endhint %}

That matters more than projecting technical authority.

#### 10. Look for a builder who can eventually think beyond code

At the beginning, the technical co-founder may write most of the code.

That is normal.

But the role eventually expands.

They may need to think about:

* hiring
* engineering culture
* architecture
* security
* infrastructure
* technical strategy
* vendors and dependencies
* technical debt
* product trade-offs
* communicating technical constraints to non-technical people

The key question is not whether they already know how to do all of these things.

It is whether they demonstrate **the judgment and ownership required to learn them**.

### Where to find a technical co-founder

The best places are usually environments where you can observe someone doing difficult work.

For example:

* open-source projects
* technical communities
* developer meetups
* hackathons
* research projects
* previous professional relationships
* founder communities
* people you have built something with before

Cold outreach can work, but it gives you much less evidence.

If possible, find people whose work you can inspect before you approach them.

A GitHub repository, technical article, open-source contribution, security research, or shipped product gives you something concrete to discuss.

### A simple technical co-founder evaluation

I would reduce the evaluation to five questions:

#### 1. Can they own a problem?

Not just execute a task, but take responsibility for the outcome.

#### 2. Can they make decisions without perfect information?

Early-stage companies require constant decisions with incomplete data.

#### 3. Do they understand consequences?

Good technical decisions consider security, reliability, operational cost, complexity, and future maintenance.

#### 4. Can you disagree productively?

You will disagree.

The question is whether disagreement improves the decision or damages the relationship.

#### 5. Would you trust them with a problem you cannot personally solve?

This is probably the most important question.

A technical co-founder should reduce the amount of uncertainty you carry, not create another dependency that you have to manage.

### The standard I would use

I would not ask:

> "Is this person a great developer?"

I would ask:

> "If I give this person an important, ambiguous problem and leave them alone for a week, what happens?"

Do they wait for instructions?

Do they produce code without understanding the problem?

Do they identify the important questions?

Do they find the risks?

Do they make reasonable trade-offs?

Do they come back with a working solution and a clear explanation of what they learned?

That is much closer to the job.

{% hint style="info" %}
A technical co-founder is ultimately an owner of technical uncertainty.
{% endhint %}

The strongest candidates are not necessarily the people who know the most technologies.

They are the people who **consistently turn ambiguous problems** into understood problems, understood problems into decisions, and decisions **into working systems**.

***

### Technical Co-Founder vs. CTO for a Startup

A technical co-founder and a startup CTO can overlap, but they are not the same role.

A **technical co-founder** is an owner of the company as well as its technical direction. They usually accept founder-level uncertainty, equity risk, and responsibility for building the technical organization.

A **CTO for a startup** is responsible for technical strategy and execution, but does not necessarily have founder-level ownership.

In an early-stage startup, one person may effectively perform both roles. The distinction becomes more important as the company grows.

{% hint style="info" %}
If you are still searching for product-market fit and need someone to take fundamental ownership of the technology, **you may be looking for a technical co-founder**.
{% endhint %}

If the company already exists and you need someone to lead engineering, architecture, hiring, security, and technical strategy, you may be looking for a startup CTO.

***

If you're looking for someone who thinks this way about architecture, security, and technical ownership, [my engineering](https://app.gitbook.com/s/1pCM7Li88yDwlAqdPL23/engineering) and [security work](https://app.gitbook.com/s/1pCM7Li88yDwlAqdPL23/security-engineering) provides concrete examples.
