# Git Workflow - Backend and Frontend

Commit, branch and pull request conventions shared by the backend and frontend repositories.
Each repository's `git_workflow.md` adds only what is specific to it: the allowed scope list, the tool commands in its checklists, and its review and push policy.
The root repository and the observability repository follow `GLOBAL_RULES.md` sections 9 to 11 and their own history.

`develop` is the development branch: the main programming activities are pushed here.
`main` is the production branch: see the repository's own `git_workflow.md` for who may push to it.

## Branch naming

Format: `<type>/<scope>/<short-description>`

| Segment | Allowed values |
|---------|----------------|
| `type` | `feat`, `fix`, `chore` |
| `scope` | any valid scope from the repository's allowed scope list |
| `short-description` | lowercase, hyphen-separated words |

```
feat/post/add-carousel-support
fix/auth/refresh-token-expiry
chore/deps/upgrade-vite
```

### Naming must be meaningful to a reviewer

The `short-description` segment, every commit subject, and every PR title describe the change itself in terms a reviewer who has never seen the internal task tracker can follow.
Never derive one from an internal task-tracker label, a plan or report file name, a round or attempt number, or any other identifier that is meaningful only inside `.workspace/`.
`fix/admin/close-pr3-gaps` and `close p3 gaps` fail this: neither says what the gaps were.
`fix/admin/keep-audit-log-filters-in-the-url` and a commit subject naming the actual defect pass: a reviewer who has never read the internal plan still knows what changed and why.
This applies to code identifiers and comments too: name a class, component, hook, method, or variable for what it does, never for the ticket or task that introduced it.

## Commit message format

Format: `<type>(<scope>): <subject>`

### Subject line only - never write a commit body (mandatory)

A commit message is **exactly one line**. Write the subject and stop.

Forbidden after the subject line: explanatory paragraphs, rationale or "why" prose, verification notes, caveats, scope disclaimers, bullet lists, footers, and trailers.
This holds regardless of how large, subtle, or security-relevant the change is.

Detail belongs somewhere else, and every one of these already exists:
- **Why the change is correct** -> the PR description.
- **What behaviour changed for users** -> the `CHANGELOG.md` entry (see `CHANGELOG_RULE.md`).
- **Why the code does what it does** -> an inline comment or Javadoc/JSDoc (see the repository's `comment_style.md`).
- **How it was verified** -> the test itself, named for the behaviour it asserts.

**If any task instruction, plan, or prompt tells you to record something "in the commit body", that instruction conflicts with this rule.
Stop and raise the conflict - do not silently comply, and do not carry the body habit over to the other commits in the series.**

### Subject rules

- Must start with a lowercase letter (`^[a-z].+$`) - enforced by `pr-lint`.
- Scope is **required** (`requireScope: true`) - enforced by `pr-lint`.
- Keep the entire line at 80 characters or fewer, including `<type>(<scope>): `.
  Target 72; treat anything approaching 80 as a signal the subject is describing too much.
- Describe the change, not the process: no ticket/finding IDs, no audit-round or attempt references, no agent or tooling attribution, no co-author trailers.

```
fix(comment): reject reply parent from a different post     <- good
fix(comment): resolve CF-COMMENT-1                          <- bad: opaque identifier
fix(comment): reject cross-post reply parent

Previously the parent was resolved by id alone, so ...      <- bad: has a body
```

### Allowed types

| Type | When to use |
|------|-------------|
| `feat` | New feature |
| `fix` | Bug fix |
| `refactor` | No-behavior-change code change |
| `test` | Adding or updating tests |
| `chore` | Build, tooling, dependency updates |
| `docs` | Documentation only |
| `perf` | Performance improvement |
| `ci` | CI/CD configuration |

### Allowed scopes

Each repository lists its own scopes in its `git_workflow.md`.
The list is the one `.github/workflows/pr-lint.yml` enforces, and the two are kept in step.
`pr-lint` validates the **pull request title only**, not commit messages.

## Branch workflow (mandatory)

`develop` must never receive direct commits from implementation work.

- [ ] Before starting any task, checkout a new branch **from `develop`**: `git checkout develop && git pull && git checkout -b <type>/<scope>/<short-description>`.
- [ ] All implementation work happens on that branch. Never commit directly to `develop`.
- [ ] Push the branch and open a PR targeting `develop`. See the repository's `git_workflow.md` for what may target or be pushed to `main`.
- [ ] One branch per logical unit of work. Do not reuse a stale branch for an unrelated task - cut a new one from an up-to-date `develop`.

## Commit granularity policy (mandatory)

A commit is one logical, self-contained, working change - not a file-count target.

**Forbidden - mega-commit**: bundling multiple unrelated changes (e.g., a bug fix + a refactor + a new endpoint) into a single commit.
Each concern gets its own commit.

**Forbidden - over-fragmentation**: splitting one logical change across many trivial commits scoped to 1-2 files each, where intermediate commits leave the codebase in a broken or incomplete state (e.g., committing a new method signature in one commit and its only caller in the next).
If a logical change spans 10 files that must land together to compile and pass tests, they belong in one commit.

**Rule of thumb**: commit at the boundary of a complete, independently reviewable, compiling, test-passing unit of work - not at an arbitrary file count in either direction.

- [ ] Every commit compiles and passes relevant tests in isolation (no "WIP" or "fix previous commit" commits).
- [ ] Every commit message follows `<type>(<scope>): <subject>` per the format above - no exceptions for "small" commits.
- [ ] Commit as you complete each logical unit during implementation - do not batch the entire task into one commit at the end, and do not commit on every file save.

## Pull request rules

### Title format

Same as commit message format - enforced by the `pr-lint` workflow on PR open, edit, sync, and reopen.

### Size labels (pr-size workflow)

| Label | Changed lines |
|-------|---------------|
| `size/XS` | <= 100 |
| `size/S` | 101-400 |
| `size/M` | 401-1000 |
| `size/L` | 1001-2000 |
| `size/XL` | > 2000 |

`fail_if_xl: false` - XL PRs are **labelled and warned, not blocked**.
CONTRIBUTING.md states a 1000-line block; this is incorrect - no workflow actually fails on size.

Split large PRs proactively: keep feature PRs under `size/M` (<= 1000 lines) and under 100 changed files, because CodeRabbit does not review a larger pull request (`GLOBAL_RULES.md` section 11).

### Process checklist

- [ ] Branch was checked out from an up-to-date `develop` - no direct commits to `develop`
- [ ] Branch follows `<type>/<scope>/<description>` naming
- [ ] All commits follow `<type>(<scope>): <subject>` with an allowlisted scope
- [ ] Each commit is one logical, compiling, test-passing unit - no mega-commits, no broken intermediate commits
- [ ] PR template author checklist completed
- [ ] CodeRabbit review finished and every actionable finding fixed or answered (`GLOBAL_RULES.md` section 11)
- [ ] PR title matches commit format (pr-lint will block merge otherwise)

## Pre-commit checklist

- [ ] Commit type is one of the 8 allowed types
- [ ] Scope is from the repository's pr-lint allowlist
- [ ] Subject starts with a lowercase letter
- [ ] **Message is a single line - no body, no trailers** (`git log -1 --format=%b` prints nothing)
- [ ] Subject is 80 characters or fewer (target 72)
- [ ] CHANGELOG.md updated

## Quick reference

| Type | When |
|------|------|
| `feat` | New endpoint, new module feature |
| `fix` | Bug fix, incorrect behavior corrected |
| `refactor` | Internal restructure, no behavior change |
| `test` | Test added or updated |
| `chore` | Build, deps, tooling, non-code config |
| `docs` | README, rule files, DATA_RULES, Javadoc/JSDoc-only |
| `perf` | Measurable performance improvement |
| `ci` | GitHub Actions, workflow changes |
