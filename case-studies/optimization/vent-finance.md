---
description: >-
  I look for costs that come from unnecessary work, oversized infrastructure, or
  inefficient system design.
---

# Vent Finance

## Cut AWS Costs by \~60%

The AWS setup had grown more expensive than necessary. Some services were oversized, and parts of the architecture were doing more work than the product required.

I reviewed the infrastructure, removed unnecessary resources, simplified parts of the architecture, and tuned the remaining services.

**Result:** monthly AWS spending was reduced by about **60%** without hurting the system's performance.

The important part was not simply using cheaper infrastructure. I reduced the amount of infrastructure the product actually needed.

## Reduced an On-Chain Cost by \~99%

A key operation on Ethereum was expensive because the workflow performed too much work through separate on-chain operations.

I redesigned the workflow around **batching and verification**, reducing the number of expensive operations needed to complete the same business process.

**Result:** the gas cost of that operation fell by about **99%**.

This was a case where the largest saving came from changing the workflow, not from finding a cheaper provider.
