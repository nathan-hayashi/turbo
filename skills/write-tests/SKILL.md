---
name: write-tests
description: "Analyze test coverage gaps, then write missing tests matching project conventions. Use when the user asks to \"write tests\", \"add tests\", \"write missing tests\", \"add test coverage\", or \"create tests\"."
---

# Write Tests

Analyze test coverage gaps, then write missing tests matching project conventions.

## Step 1: Review Test Coverage

Run the `/review-test-coverage` skill with the appropriate scope. If specific files, a diff command, or a target was provided, pass it through. Otherwise, let `/review-test-coverage` determine the scope from context.

If nothing needs tests, report that and stop.

## Step 2: Write and Run Tests

1. Identify the project's test framework and conventions by reading existing test files
2. Write focused unit or integration tests for each coverage gap identified in Step 1
3. Run the test suite to confirm all tests pass
4. If tests fail, run the `/investigate` skill to diagnose the root cause, then apply the suggested fix and re-run tests. If investigation cannot identify a root cause after its full cycle, stop and report with investigation findings.
