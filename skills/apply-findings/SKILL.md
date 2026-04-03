---
name: apply-findings
description: "Apply findings by making the suggested code changes. Handles both evaluated findings (applies accepted verdicts) and unevaluated findings. Use when the user asks to \"apply findings\", \"apply fixes\", \"apply suggestions\", \"apply accepted findings\", \"fix the findings\", or \"apply the review results\"."
---

# Apply Findings

Apply findings from evaluations, reviews, or analysis results. Findings are already present in the conversation context.

## Step 1: Identify Findings

Collect all findings from the conversation context. Determine which to apply:

- **Evaluated findings** (table with Confidence/Verdict columns): Apply findings with **Accept** or **Accept with caveats** verdicts. Skip findings with **Skip** verdict.
- **Unevaluated findings** (raw agent output, lists without verdicts): Apply all of them.

## Step 2: Apply in File Order

Group findings by file path and apply in file order to minimize context switching. For each finding:

1. Read the full function or logical block at the referenced location
2. Verify the finding still applies to the current code
3. Make the fix

If a finding references code that has changed since it was generated (e.g., by a prior fix in this same run), re-assess whether it still applies. Skip if the code has diverged.

## Step 3: Offer Skipped Improvements

Do not note findings skipped as false positives or subjective preferences. For findings skipped solely because they are pre-existing, out of scope, or out of proportion, use `AskUserQuestion` to ask whether the user wants to implement the improvement now or note it for later. Based on the response:

- **Implement now**: Make the change inline.
- **Note for later**: Run the `/note-improvement` skill to capture it.

## Step 4: Report Results

Summarize what was applied and what was skipped.

## Rules

- Only edit files. Do not stage, build, or test.
- If two findings conflict (suggest opposite changes to the same code), skip both and report the conflict.
