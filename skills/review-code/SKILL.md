---
name: review-code
description: "Full code review pipeline: launches `/review-correctness`, `/peer-review`, `/review-security`, and `/review-api-usage` in parallel, evaluates findings, and returns actionable results. Use when the user asks to \"review my code\", \"full code review\", \"review my changes\", or wants a comprehensive code review."
---

# Review Code

Run AI code review and evaluate findings.

## Step 1: Determine the Diff

Determine the appropriate diff command (e.g. `git diff --cached`, `git diff main...HEAD`) based on the current git state. If a specific diff command was provided, use that. Otherwise, default to diffing against the repository's default branch (detect via `gh repo view --json defaultBranchRef --jq '.defaultBranchRef.name'`).

## Step 2: Run Four Reviews in Parallel

Launch all four reviews in a single message so they run concurrently. The diff command from Step 1 determines what each reviewer analyzes.

### Review A: Correctness Review

Launch an agent (`model: "opus"`, do not set `run_in_background`) that runs the `/review-correctness` skill with the diff command from Step 1.

### Review B: Peer Review

Launch an agent (`model: "opus"`, do not set `run_in_background`) that runs the `/peer-review` skill with the diff command from Step 1.

### Review C: Security Review

Launch an agent (`model: "opus"`, do not set `run_in_background`) that runs the `/review-security` skill with the diff command from Step 1.

### Review D: API Usage Review

Launch an agent (`model: "opus"`, do not set `run_in_background`) that runs the `/review-api-usage` skill with the diff command from Step 1.

## Step 3: Evaluate Findings

Run the `/evaluate-findings` skill.

If zero actionable findings survive evaluation, report that the code looks clean and stop.

## Rules

- If any reviewer is unavailable or returns malformed output, proceed with findings from the remaining reviewers.
- Present findings in file order to minimize context switching.
