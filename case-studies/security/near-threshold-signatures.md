---
description: What if the input is deliberately hostile?
---

# Near Threshold Signatures

For a cryptographic library, I did not limit the review to whether normal inputs produced the expected result. I looked for inputs and environments that could make the software crash, behave incorrectly, or expose sensitive operations.

This led to several concrete findings:

* An endianness vulnerability that affected big-endian systems.
* Inputs that could cause local or remote nodes to crash or run out of memory.
* Buffer overflow vulnerabilities.
* Potential side-channel attack surfaces in sensitive operations.

I reported or fixed these issues and also contributed verification and testing improvements.

### **Outcome**

Multiple ways of crashing or attacking the library [were removed](near-threshold-signatures.md) before they could become failures for its users.
