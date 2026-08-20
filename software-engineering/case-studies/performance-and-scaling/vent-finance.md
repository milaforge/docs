---
description: Reducing the cost of an on-chain operation
---

# Vent finance

VENT Finance depended on Ethereum for financial operations. One important operation was becoming expensive because the workflow required multiple on-chain steps.

Instead of only trying to make the existing implementation cheaper, I looked at whether the workflow itself could require fewer expensive operations.

I redesigned the process around **batching and verification**, reducing the gas cost of that operation by roughly **99%**.

The problem was therefore solved at the workflow level rather than by simply accepting the existing transaction cost.
