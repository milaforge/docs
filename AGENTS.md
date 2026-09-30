<!-- gitbook-agent-instructions:start -->

## GitBook Documentation Editing

This repository contains documentation synced with GitBook via Git Sync.

Before editing GitBook-synced Markdown, YAML, or asset files, make sure the GitBook skill is available and up to date in your local agent environment. Prefer installing or updating it with:

```bash
npx skills add gitbookio/gitbook-skills
```

This command may add or update local agent skill files. Use them only as local agent instructions; do not commit those installed skill files or any tool-generated agent configuration unless the user explicitly asks for it.

If `npx` is unavailable, load the skill from:

https://gitbook.com/docs/skill.md

When making changes, preserve GitBook sync metadata such as frontmatter, `SUMMARY.md`, `docs.yaml`, `.gitbook/`, and asset links unless the requested edit explicitly requires changing them.

<!-- gitbook-agent-instructions:end -->

## Portfolio Writing

Position the portfolio around end-to-end delivery: taking an unclear product or technical need through definition, implementation, and production. Lead showcases with the strongest complete evidence of that arc, and label prototypes, alpha, beta, and production work accurately. Lead homepage project examples, case-study summaries, and page descriptions with a succinct business outcome as evidence of that responsibility. Support the outcome with project decisions, technical details, and measurements in the page content that follows. Keep claims within what the available evidence supports.

### Less is more

Every word must help convey meaning. Prefer short, concrete prose; remove repetition, filler, and claims that the evidence cannot support.

Keep the main README to one featured case study per group: Build, Improve, Secure. Each linked case study should explain the uncertainty, what was built or changed, the risks that shaped implementation, the evidence, and the decision that followed. Label actual maturity and distinguish measured results from inference. State evidence gaps instead of inventing a validation or decision history.
