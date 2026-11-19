---
name: git-workflow
description: Git hygiene and release workflow covering Conventional Commits, commitlint/husky, semantic-release, clean branching, and safe PR/rebase practices.
when_to_use: "When setting up commit conventions, preparing PRs, resolving merge conflicts, configuring husky/commitlint, or managing release branches."
allowed-tools: Read, Write, Edit, Glob, Grep, Bash
version: 1.0.0
---

# Git Workflow — Branching, Commits & Release Hygiene

> Small, atomic commits and predictable branches make code review fast and rollbacks painless.

## Core Standards

1. **Conventional Commits**:
   - Format: `<type>(<scope>): <imperative summary>` (e.g., `feat(auth): add refresh token rotation`, `fix(cli): handle missing manifest`).
   - Allowed types: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`, `revert`.
   - Enforce with `commitlint` + `husky` (`commit-msg`) and `lint-staged` (`pre-commit`).
2. **Branching & PR Hygiene**:
   - Never commit major changes directly to `main`; work on focused `feat/*`, `fix/*`, or `dev` branches.
   - Keep PRs small and single-purpose; avoid mixing formatting sweeps with logic changes.
   - Never force-push (`git push --force`) to shared branches (`main`, `dev`); use `--force-with-lease` only on personal feature branches when rebasing.
3. **Clean Repository State**:
   - Never commit `.env`, credentials, `node_modules/`, build artifacts, or OS clutter (`.DS_Store`).
   - Review `git diff --staged` before committing to catch stray debug logs or unintended edits.

## Verification Checklist

- Commit messages follow Conventional Commits (`<type>(<scope>): <subject>`).
- `git status` shows no untracked secrets or build artifacts.
- All tests and linters pass before pushing.
