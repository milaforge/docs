---
description: >-
  How I redesigned VENT Finance's allocation workflow from thousands of on-chain
  writes into a Merkle-proof system, cutting allocation costs by ~99% and
  reducing ops from ~48 hours to ~1 hour.
---

# From 7,000 Transactions to Zero

### Context

_VENT Finance_ was a _B2B_ digital-asset platform.

For each new client and campaign, a large community of participants had to be categorized into different _tiers_ and assigned _allocations_. Those allocations then had to be made verifiable on-chain.

The _MVP_ implementation took the simplest approach:

**store every participant's allocation directly on-chain.**

It worked.

But as campaigns grew, that decision became one of our largest operational bottlenecks.

For a campaign with **7,000 participants, we had to submit exactly 7,000 blockchain transactions**.

We had a script to automate the process, but automation did not solve the underlying problem.

The workflow was:

* <mark style="color:$danger;">expensive</mark>,
* <mark style="color:$danger;">slow</mark>,
* <mark style="color:$danger;">vulnerable to partial failures</mark>,
* <mark style="color:$danger;">difficult to supervise</mark>,
* <mark style="color:$danger;">and painful to recover when something went wrong</mark>.

We reserved approximately **48 hours** before a campaign for the allocation process alone.

***

### The first step: understand why the system worked this way

I did not start by rewriting it.

I followed the existing allocation procedure myself several times so I could understand the failure modes firsthand.

Then I traced the workflow across the rest of the system:

* smart contracts,
* Django backend,
* database schema,
* frontend allocation flows,
* deployment processes,
* and the client-onboarding team's workflow.

The initial question was:

> How can we make thousands of blockchain transactions cheaper and safer?

After reviewing the system, I realized that was the wrong question.

The application already stored the allocation information it needed off-chain.

There was no fundamental requirement for the blockchain to store thousands of individual allocation records.

What we actually needed on-chain was a way to **verify that a participant's claimed allocation belonged to the allocation set approved for that campaign**.

That changed the problem to:

> <mark style="color:$success;">Why are we writing thousands of allocations on-chain at all?</mark>

***

## Phase 1 — Batch the existing workflow

The architectural solution I had in mind would affect the frontend, backend, smart contracts, onboarding process, and security model.

It was too large to introduce as an emergency optimization.

So I separated the work into two tracks:

1. reduce the immediate operational pain;
2. redesign the allocation model properly over the longer term.

<mark style="color:$success;">For the immediate improvement</mark>, I changed the smart-contract API and allocation script to process allocations in batches instead of sending one transaction per participant.

I owned both components, so this could be introduced without forcing a redesign across the rest of the product.

For a campaign with approximately **7,000 participants**:

```
Before

~7,000 participants
       ↓
~7,000 transactions
```

became:

```
After batching

~7,000 participants
       ↓
~50 transactions
```

For a campaign with roughly **1,000 participants, we needed around 5 transactions**.

This was already a major operational improvement.

But it still treated the blockchain as the database for allocations.

The deeper problem remained.

***

## Phase 2 — Replace on-chain allocation storage with a Merkle tree

Batching solved most of the immediate operational pain without disrupting the existing workflow. For a campaign with roughly 7,000 participants, it reduced the allocation process from 7,000 transactions to around 50.

But it still treated the blockchain as the database for allocations.

In parallel with my other responsibilities, I designed and progressively rolled out a broader architectural change over the following six months: replacing thousands of on-chain allocation records with a **Merkle-tree-based verification model**.

Instead of storing every participant's allocation independently on-chain, the complete allocation dataset remained off-chain. From that dataset, we generated a Merkle tree and stored only its **32-byte Merkle root** in the smart contract.

Conceptually, the architecture changed from:

```
Participant 1 ── allocation ──> on-chain storage
Participant 2 ── allocation ──> on-chain storage
Participant 3 ── allocation ──> on-chain storage
...
Participant N ── allocation ──> on-chain storage
```

to:

```
             Allocation dataset
                    │
                    ▼
               Merkle tree
                    │
                    ▼
          32-byte Merkle root
                    │
                    ▼
               Blockchain
```

When a participant later needed to use their allocation, the application supplied:

```
allocation
+
Merkle proof
+
stored Merkle root
```

The smart contract could verify that the allocation belonged to the approved dataset without storing every allocation itself.

<mark style="color:$success;">The blockchain became the</mark> <mark style="color:$success;"></mark><mark style="color:$success;">**verification layer**</mark><mark style="color:$success;">, rather than the storage layer.</mark>

***

## The difficult part was not the Merkle tree

Generating a Merkle root was not the hard part.

The existing product had been designed around the assumption that allocations were stored directly on-chain.

Changing that assumption affected multiple systems.

I owned the feature end to end, including changes across:

* smart contracts,
* backend services,
* Django/data models,
* frontend flows,
* allocation-generation tooling,
* onboarding procedures,
* and deployment workflows.

I worked with PM, Ops, developers, and onboarding specialists to make sure the new design still supported the workflows they depended on.

Because this system controlled financial allocations, the migration also had to preserve security and correctness.

I worked with two **external security audit team (Hacken, PeckShield)** to review the redesigned flow and increase our confidence before relying on it operationally.

***

## Phase 3 — One transaction

The first version of the Merkle architecture still required us to submit the Merkle root as a transaction.

<mark style="color:$success;">That reduced the allocation workflow from thousands of transactions to one.</mark>

```
~7,000 transactions
        ↓
~50 transactions
        ↓
1 transaction
```

At that point the allocation data itself was no longer the bottleneck.

But there was still one unnecessary transaction left.

***

## Phase 4 — Zero additional allocation transactions

Every new client already required us to deploy and initialize a smart contract.

The only allocation-specific data we now needed on-chain was a single **32-byte Merkle root**.

So instead of publishing that root afterward in another transaction, we embedded it into the contract's existing initialization process.

The allocation workflow therefore required **no separate blockchain transaction at all**.

```
Client initialization
        │
        ├── campaign configuration
        ├── other required state
        └── 32-byte allocation Merkle root
```

Additional allocation transactions:

```
0
```

***

## The evolution

For a representative campaign with around 7,000 participants:

```
MVP
~7,000 allocation transactions
        │
        ▼
Batching
~50 allocation transactions
        │
        ▼
Merkle-tree redesign
1 allocation transaction
        │
        ▼
Embed Merkle root during initialization
0 additional allocation transactions
```

<mark style="color:$success;">The final optimization was</mark> not making blockchain writes incrementally cheaper.

It was <mark style="color:$success;">**removing the writes entirely**</mark>.

***

## Outcome

The redesign produced several consequences.

#### \~99% lower on-chain allocation cost

The allocation-specific blockchain cost fell by approximately **99%** compared with the original workflow.

More importantly, the cost no longer grew by requiring one state-changing transaction for every participant.

#### \~7,000 → 0 additional allocation transactions

A large campaign that previously required thousands of transactions could be initialized without any allocation-specific transaction.

#### \~48 hours → \~1 hour of operational lead time

The onboarding team no longer needed to reserve approximately two days to execute, supervise, and recover a large sequence of blockchain transactions.

I reduced the operational safety window to roughly **one hour**.

Most of that remaining time was precaution rather than transaction processing.

#### Fewer failure points

Thousands of sequential transactions created thousands of opportunities for:

* failed transactions,
* partial execution,
* retries,
* inconsistent state,
* RPC/network problems,
* and human intervention.

Removing those transactions removed most of those failure modes with them.

#### Expensive chains became practical

The original architecture made high-gas networks difficult to justify economically.

By eliminating allocation-specific transactions, the redesigned system made operating on more expensive chains such as **Ethereum** substantially more practical.

The improvement therefore did more than reduce infrastructure cost.

It <mark style="color:$success;">expanded the set of networks the product could reasonably support</mark>.

***

## What I would do differently

The original architecture was not necessarily a mistake.

For an MVP, writing allocations directly on-chain was simple, understandable, and fast to implement.

The mistake would have been treating an MVP architecture as permanent once its operating assumptions changed.

The progression was deliberate:

```
Ship the simplest working model
        ↓
Observe the real operational cost
        ↓
Introduce a low-risk tactical improvement
        ↓
Understand the deeper system boundary
        ↓
Redesign it without blocking ongoing operations
```

Batching gave us immediate relief while buying enough time to implement the larger architectural change safely.

That intermediate step mattered.

Waiting six months for the perfect solution would have left the team dealing with the original operational problem throughout that period.

***

## Takeaway

The most important optimization was not a Solidity trick or a cheaper transaction.

It was changing the system boundary.

We originally used Blockchain to both **store and verify** thousands of allocations.

After the redesign:

* the application stored the allocation dataset,
* a Merkle root committed to that dataset on-chain,
* Merkle proofs provided verifiable inclusion,
* and the smart contract performed only the verification that actually required blockchain trust.

Once we separated **what needed to be stored** from **what needed to be trusted**, thousands of transactions disappeared.

<mark style="background-color:$success;">**The best transaction optimization turned out to be not sending the transaction at all.**</mark>
