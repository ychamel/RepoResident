# Contributing to RepoResident

Thank you for improving RepoResident. Contributions should keep the project small, portable, and
understandable without requiring a service, package manager, or specific coding agent.

## Good contributions

- Correct instructions that doesn't produce unreliable or unsafe agent behavior.
- Improve a workflow without adding unnecessary context.
- Add compatibility guidance supported by direct testing.
- Clarify documentation for new users.
- Reduce repeated instructions while preserving behavior.
- Add reproducible evidence for efficiency or quality claims.

Large new capabilities should begin with an issue describing the problem and intended scope.

## Development process

1. Fork the repository and create a focused branch.
2. Read `CLAUDE.md` and the relevant workflow before editing.
3. Keep the change limited to one clear purpose.
4. Preserve the repository's file budgets and layered-context design.
5. Validate all relative links and examples you changed.
6. Open a pull request explaining the problem, the chosen approach, and how it was verified.

## Validation

This repository contains Markdown instructions rather than a runtime application. Before opening a
pull request, run:

```bash
git diff --check
git status --short
```

Also verify:

- Referenced files and workflows exist.
- Instructions do not conflict with `CLAUDE.md`.
- A new workflow has a clear entry condition and completion checklist.
- Quantitative claims include a reproducible method or are labeled as observed results.
- Public documentation uses direct, conventional technical language.

## Pull requests

Keep pull requests reviewable. Include:

- The user or agent problem being solved.
- The files and behavior affected.
- Evidence used to validate the change.
- Any compatibility or migration concern.

Do not combine documentation cleanup, workflow redesign, and unrelated template changes in one pull
request.

## License

By contributing, you agree that your contribution will be licensed under the repository's
[MIT License](LICENSE).

