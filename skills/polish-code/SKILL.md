---
name: polish-code
description: "Stage, write tests, simplify, review, test, and re-run itself until stable. Use when the user asks to \"polish code\", \"refine code\", \"iterate on code quality\", \"simplify and review loop\", \"clean up, test, and review loop\", or \"run the polish loop\"."
---

# Polish Code

Stage, write tests, simplify, review, test, and re-run until stable.

## Step 1: Stage

Run the `/stage` skill.

## Step 2: Write Missing Tests

Run the `/write-tests` skill. The diff command is `git diff --cached`.

Stage all changes made in this step before continuing.

## Step 3: Simplify

Run the `/simplify-code` skill. The diff command is `git diff --cached`.

Stage all changes made in this step before continuing.

## Step 4: Review and Fix

Run the `/review-code` skill. The diff command is `git diff --cached`.

Always run this step even if Step 3 made no changes. Apply each actionable finding from the evaluated results directly, skipping false positives.

Stage all changes made in this step before continuing.

## Step 5: Test

Run the project's test suite to confirm nothing broke. If tests fail, run the `/investigate` skill to diagnose the root cause, apply the suggested fix, and re-run tests. If investigation cannot identify a root cause, stop and report with investigation findings.

Stage all changes made in this step before continuing.

## Step 6: Lint

Run the project's formatter first, then the linter. Fix any lint errors or warnings that the formatter did not resolve. If the project has a combined format+lint script, use that.

Stage all changes made in this step before continuing.

## Step 7: Re-run if Changed

If Steps 3-6 produced any changes during this run, re-run Steps 3-6 scoped to only the files modified in the previous iteration. Use `git diff --cached -- <file1> <file2> ...` as the diff command for `/simplify-code` and `/review-code`. Cap at 3 total iterations to prevent runaway loops.

## Rules

- Every step must run. No change is "self-evidently correct" or "mechanical" enough to skip review. Simplify findings do not substitute for review. Passing tests do not substitute for lint. Each step catches different issues.
