---
name: simplify-code
description: "Review changed code for reuse, quality, efficiency, and clarity issues, then fix them. Use when the user asks to \"simplify code\", \"simplify changes\", \"clean up code\", \"refactor changes\", or \"run simplify\"."
---

# Simplify Code

Review code for reuse, quality, and efficiency issues, then fix them.

## Step 1: Review Quality

Run the `/review-quality` skill with the appropriate scope. If a specific diff command was provided, pass it through. Otherwise, let `/review-quality` determine the scope from context.

## Step 2: Fix Issues

Apply each fix from the review findings directly, skipping false positives. Only edit files — do not stage, build, or test.

When done, briefly summarize what was fixed (or confirm the code was already clean).
