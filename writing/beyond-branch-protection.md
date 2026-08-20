---
description: >-
  Branch protection handles global merge rules. Pull request policies enforce
  requirements based on what changes in a pull request.
tags:
  - engineering
---

# Beyond Branch Protection

GitHub branch protection is good at enforcing **global rules**:

* Pull requests are required.
* Two approvals are required.
* CI must pass.
* Code owners must review.

But engineering teams often have rules that depend on **what changed**.

For example:

> If authentication code changes, require two approvals.

> If a GitHub Actions workflow changes, require security review.

> If the public API changes, require a changelog.

These aren't global branch rules. They are **change-specific policies**.

### Three different problems

It helps to separate them:

| Problem                                    | Mechanism                    |
| ------------------------------------------ | ---------------------------- |
| Can this branch be merged?                 | Branch protection / rulesets |
| Who owns this code?                        | CODEOWNERS                   |
| What should happen because of this change? | Policy                       |

The third category is easy to leave as documentation or team convention.

That's where things become fragile.

### From conventions to executable policy

Instead of documenting:

> "Authentication changes should receive additional review."

the repository can express it as policy:

```yaml
policies:
  - id: auth-needs-two-approvals
    when:
      changed: ['src/auth/**']
    require:
      approval_count_at_least: 2
```

Now the rule is:

* explicit
* version-controlled
* reviewable
* automatically enforced

The important idea isn't YAML. It's that **repository-specific engineering rules become executable**.

### The boundary

This doesn't replace GitHub's existing controls.

Use branch protection for branch-level invariants.

Use _CODEOWNERS_ for ownership.

Use _CI_ for software checks.

Use security tooling for security findings.

Use change-aware policy when the requirement depends on **what a pull request changes**.

***

### **See the implementation**

I explored this approach in GitHub [Pull Request Policy: Enforcing Conditional Repository Rules](../software-engineering/case-studies/automation/policy-as-code-for-pull-requests.md) — a GitHub Action for defining change-aware pull request policies as code.
