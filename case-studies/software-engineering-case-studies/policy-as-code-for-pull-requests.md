---
description: >-
  A policy-as-code GitHub Action for enforcing engineering and security rules
  based on what a pull request changes.
---

# Policy as Code for Pull Requests

### The problem

GitHub can enforce broad pull-request requirements: required approvals, passing checks, branch protection, and code ownership.

But many engineering rules are **conditional**.

For example:

* changes to `src/auth/**` require two approvals
* changes to `.github/workflows/**` require security review
* public API changes require a changelog
* certain files require a specific label
* a pull request must satisfy a particular title or description format

These rules depend on the **content and context of the pull request**, not simply whether a pull request exists.

GitHub's built-in controls don't provide a general-purpose way to express these conditions. That gap led me to build **Pull Request Policy**.

The important distinction is that this is not another security scanner. It is a mechanism for turning **repository-specific engineering rules into executable policy**.

***

### The approach

Policies are defined in YAML and committed alongside the repository:

```yaml
policies:
  - id: auth-needs-two-approvals
    when:
      changed: ['src/auth/**']
    require:
      approval_count_at_least: 2
    message: 'Auth changes require at least 2 approvals.'

  - id: workflow-needs-security-review
    when:
      changed: ['.github/workflows/**']
    require:
      has_label: ['security-review']
    message: 'Workflow changes require security review.'
```

The policy becomes part of the repository rather than an undocumented convention or an externally managed configuration.

***

### Why build a policy engine?

A naïve implementation could have put every rule directly into a GitHub Action.

That would work initially, but the Action would gradually become a collection of special cases:

```
if auth changed → ...
if workflow changed → ...
if API changed → ...
if label missing → ...
```

Instead, I separated the system into:

```
GitHub Action
      │
      ▼
Facts Provider
      │
      ▼
Policy Engine
   ┌──┴───────┐
   ▼          ▼
Predicates  Combinators
      │
      ▼
   Reporter
```

The Action handles the imperative environment: GitHub, configuration loading, and reporting.

The policy engine evaluates facts using pure functions.

This keeps the core logic deterministic and testable, while allowing the policy language to grow independently of GitHub's runtime concerns.

***

### The policy model

The engine separates **when a policy applies** from **what it requires**.

For example:

```yaml
when:  
    changed: ['src/auth/**']
require:  
    approval_count_at_least: 2
```

The current implementation provides predicates for changed files, file existence, PR title and body, labels, approval counts, and file contents. Predicates can also be composed with `all`, `any`, and `not`.

This makes the system extensible without creating a separate implementation for every repository rule.

***

### Design constraints

#### No external service

The policy runs as a normal GitHub Action. There is no webhook server, database, bot, or SaaS dependency.

#### Minimal repository access

The system only reads repository files when a policy explicitly requires that information.

#### Pure policy evaluation

The core engine has no side effects. GitHub-specific orchestration stays outside the policy evaluation layer.

#### Safe defaults

A missing configuration does not unexpectedly break a build; the implementation uses an advisory fallback.

***

### Where it fits

Pull Request Policy is complementary to existing GitHub controls:

| Tool                    | Primary purpose                  |
| ----------------------- | -------------------------------- |
| Branch protection       | Global merge requirements        |
| CODEOWNERS              | Ownership and review routing     |
| Security scanners       | Finding security issues          |
| GitHub Actions          | Executing automation             |
| **Pull Request Policy** | **Conditional repository rules** |

The project therefore sits at the intersection of **developer workflow, engineering governance, and secure software development**.

***

### What I found interesting

The interesting engineering problem wasn't writing another GitHub Action.

It was deciding **where policy belongs in the software delivery system**.

A repository already contains:

* source code
* configuration
* tests
* workflows
* ownership information
* documentation

It can also contain its **engineering constraints**.

Once those constraints become executable, they can be versioned, reviewed, tested, and changed alongside the code they govern.

That is the broader idea behind the project: **policy as code at the pull-request boundary**.

***

### Project

**Pull Request Policy**

> Open-source GitHub Action for defining and enforcing conditional pull-request policies in YAML.

<p align="center"><a href="https://github.com/milaforge/pull-request-policy">View the project on Github</a>.</p>

{% embed url="https://github.com/milaforge/pull-request-policy" %}

***

### Background: Pull Request Policies

[beyond-branch-protection.md](../../think/beyond-branch-protection.md "mention") — the problem and reasoning behind this project.
