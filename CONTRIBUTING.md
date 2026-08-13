# Contributing Guide

Thank you for your interest in contributing to this repository that contains the documentation and case studies that showcase my work as a technical partner for early‑stage startups. Contributions can be new case studies, improvements to existing pages, additional resources, or fixes.

---

## Ways to Contribute

| Type of contribution | Description |
|----------------------|-------------|
| **New case study**   | Add a markdown file under `case-studies/` describing a real project, problem, solution, and outcomes. |
| **Content improvements** | Fix typos, clarify wording, add diagrams, or update links in any `.md` file. |
| **New subject area** | Create a new folder (e.g., `machine-learning/`) with an `index.md` and supporting pages. |
| **Documentation structure** | Update `SUMMARY.md` (the GitBook table of contents) when adding/removing pages. |
| **Assets** | Add images, diagrams, or PDFs to `.gitbook/assets/` and reference them in markdown. |

---

## Getting Started

1. **Fork** the repository on GitHub.
2. **Clone** your fork locally:
   ```bash
   git clone https://github.com/milaforge/docs.git
   cd docs
   ```
3. **Create a branch** for your change:
   ```bash
   git checkout -b my-contribution
   ```
4. **Make your changes** following the guidelines below.
5. **Commit** with a clear message:
   ```bash
   git commit -m "Add case study: Optimizing CI/CD for Startup X"
   ```
6. **Push** to your fork and open a **Pull Request** against the `main` branch of this repo.

---

## Writing Guidelines

- **Markdown** – Use standard GitBook‑compatible markdown (headings, tables, code fences, callouts).
- **Front‑matter** – Every page should start with a YAML front‑matter block containing at least `title:` and optionally `description:`.
  ```yaml
  ---
  title: "Optimizing CI/CD for Startup X"
  description: "How we reduced deployment time from 30 min to 3 min."
  ---
  ```
- **Naming** – Use kebab‑case for file names (`my-new-case-study.md`).
- **Images** – Place images in `.gitbook/assets/` and reference them with relative paths:
  `![Architecture diagram](../.gitbook/assets/arch-diagram.png)`
- **Links** – Prefer relative links to other docs (`../engineering/README.md`) over absolute URLs.
- **Table of Contents** – After adding/removing pages, edit `SUMMARY.md` to reflect the new structure.

---

## Review Process

1. **Automated checks** – The CI pipeline runs markdown linting (`markdownlint`) and validates front‑matter.
2. **Human review** – At least one maintainer will review the PR for clarity, tone, and alignment with the portfolio narrative.
3. **Merge** – Once approved, the PR will be squashed and merged to `main`. The GitBook site rebuilds automatically.

---

## Code of Conduct

All contributors are expected to follow our [Code of Conduct](CODE_OF_CONDUCT.md). Please read it before participating.

---

## License

By contributing, you agree that your contributions will be licensed under the project’s [MIT License](LICENSE).

---

## Questions?

Open an issue or start a discussion in the repository – we’re happy to help!