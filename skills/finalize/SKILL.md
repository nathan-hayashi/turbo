---
name: finalize
description: Run the post-implementation quality assurance workflow including tests, code simplification, review, and commit. Use when the user asks to "finalize implementation", "finalize changes", "wrap up implementation", "finish up", "ready to commit", "run QA workflow", or "ship it".
---

# Finalize Implementation

Post-implementation QA workflow: staging, tests, code simplification, AI review, commit, and self-improvement.

## Task Tracking

At the start, use `TaskCreate` to create a task for each phase:

1. Stage and test
2. Simplify code
3. Code review
4. Self-improve
5. Commit and PR

## Phase 1: Stage and Test

### Step 1: Stage implementation changes

Stage only the files changed during this implementation:

```bash
git add <file1> <file2> ...
```

Do not use `git add -A` or `git add .`. If a file contains both implementation changes and unrelated changes, use `git add -p <file>` to stage only the relevant hunks. Use `git status` and `git diff --cached` to verify the staging area contains exactly the intended changes.

### Step 2: Write missing tests

Determine whether new tests are needed:

1. Run `git diff --cached --name-only` to identify staged files
2. Skip this step if changes are non-testable (config, documentation, CI files, SKILL.md files, markdown) or adequate tests already cover the modified code
3. Search for existing test files covering the modified code using Glob and Grep

If tests are needed:

1. Identify the project's test framework and conventions by reading existing test files
2. Write focused unit or integration tests for the new or changed behavior
3. Run the test suite to confirm all tests pass
4. If tests fail, run the `/investigate` skill to diagnose the root cause, then apply the suggested fix and re-run tests. If investigation cannot identify a root cause after its full cycle, stop and report with investigation findings.
5. Stage the new test files

## Phase 2: Simplify Code

Run the `/simplify-code` skill. The diff command for this phase is `git diff --cached`.

## Phase 3: Code Review

### Step 1: Run code review

Run the `/review-code` skill to review uncommitted changes. After findings evaluation, proceed based on the results:

- **Zero actionable findings** — skip Steps 2 and 3 and proceed to Phase 4.
- **Actionable findings** — apply all findings. Launch a single opus agent with the full diff to apply each fix, then continue with Steps 2 and 3.

### Step 2: Simplify review fixes

Run the `/simplify-code` skill. The diff command for this phase is `git diff` (NOT `git diff --cached` — the fix agent's changes are unstaged).

### Step 3: Test and lint

1. Run the test suite to confirm nothing broke
2. If tests fail, run the `/investigate` skill to diagnose the root cause, apply the suggested fix, and re-run tests. If investigation cannot identify a root cause, stop and report with investigation findings.
3. Run the project's linter/formatter to ensure clean output

## Phase 4: Self-Improve

Run the `/self-improve` skill.

## Phase 5: Commit and PR

### Step 1: Determine intent

Detect the repository's default branch via `gh repo view --json defaultBranchRef --jq '.defaultBranchRef.name'`. Check the current branch name and whether a PR already exists for it using `gh pr view`.

Use `AskUserQuestion` to ask the user how to proceed. Present the options based on the current state:

- **On a feature branch with an existing PR** — commit, push, and update the PR
- **On a feature branch without a PR** — commit only, or commit + create a PR
- **On the default branch** — commit only, or create a feature branch + commit + create a PR
- **Abort** — leave changes staged, do not commit

### Step 2: Branch (if needed)

If the user wants a PR and the current branch is the default branch:

1. Suggest a branch name based on the changes and use `AskUserQuestion` to confirm or adjust
2. Create and switch to the new branch: `git checkout -b <branch-name>`

### Step 3: Commit

Run the `/commit-staged` skill.

### Step 4: Push and create or update PR (if requested)

- **PR exists** — push and run the `/update-pr` skill
- **New PR** — push with `-u` and run the `/create-pr` skill
- **Commit only** — end the workflow (do not push unless the user asks)

### Step 5: Resolve PR comments

Use `AskUserQuestion` to ask if the user wants to wait for automated reviewers to finish and resolve comments.

- **Skip** — end the workflow
- **Resolve comments** — run the `/resolve-pr-comments` skill

## Rules

- Never stage or commit files containing secrets (`.env`, credentials, API keys). Warn if detected.
- Do not present diffs to the user — the user reviews diffs in an external git client. Use `git diff` internally as needed.
- If a non-test step fails (simplifier, review), stop and report the failure. Do not skip ahead.
