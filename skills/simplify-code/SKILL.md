---
name: simplify-code
description: "Review code for reuse, quality, efficiency, and clarity issues, then fix them. Use when the user asks to \"simplify code\", \"simplify changes\", \"clean up code\", \"refactor code\", \"refactor changes\", or \"run simplify\"."
---

# Simplify Code

Review code for reuse, quality, efficiency, and clarity issues, then fix them.

## Step 1: Determine the Scope

Determine what to review:

- If a specific **diff command** was provided (e.g., `git diff --cached`), use that.
- If a **file list or directory** was provided, review those files directly (read the full files, not a diff).
- If **neither** was provided, determine the appropriate diff command (e.g., `git diff`, `git diff --cached`, `git diff HEAD`) based on the current git state. If there are no git changes, review the most recently modified files mentioned in the conversation.

## Step 2: Review

For diff scope: run the diff command to obtain the changes and only flag issues introduced by the changeset. For file scope: read the specified files. For each file, read enough surrounding context to understand the code. Then check for all of the following:

### Reuse

1. **Search for existing utilities and helpers** that could replace newly written code. Look for similar patterns elsewhere in the codebase — common locations are utility directories, shared modules, and files adjacent to the changed ones.
2. **Flag any new function that duplicates existing functionality.** Suggest the existing function to use instead.
3. **Flag any inline logic that could use an existing utility** — hand-rolled string manipulation, manual path handling, custom environment checks, and similar patterns are common candidates.

### Quality

1. **Redundant state**: state that duplicates existing state, cached values that could be derived, reactive subscriptions that could be direct calls
2. **Parameter sprawl**: adding new parameters to a function instead of generalizing or restructuring existing ones
3. **Copy-paste with slight variation**: near-duplicate code blocks that should be unified with a shared abstraction
4. **Leaky abstractions**: exposing internal details that should be encapsulated, or breaking existing abstraction boundaries
5. **Stringly-typed code**: using raw strings where constants, enums, or dedicated types already exist in the codebase
6. **Unnecessary wrapper nesting**: container elements or wrapper layers that add no structural or layout value

### Efficiency

1. **Unnecessary work**: redundant computations, repeated file reads, duplicate network/API calls, N+1 patterns
2. **Algorithmic complexity**: nested iterations, repeated linear searches replaceable by sets/maps, missing early exits
3. **Missed concurrency**: independent operations run sequentially when they could run in parallel
4. **Hot-path bloat**: new blocking work added to startup or per-request hot paths
5. **Unnecessary existence checks**: pre-checking file/resource existence before operating (TOCTOU anti-pattern) — operate directly and handle the error
6. **Memory**: unbounded data structures, missing cleanup, resource leaks
7. **Overly broad operations**: reading entire files when only a portion is needed, loading all items when filtering for one

### Clarity and Standards

1. **Project standards**: coding conventions from CLAUDE.md not followed — import sorting, naming conventions, component patterns, error handling patterns, module style
2. **Unnecessary complexity**: deep nesting, redundant abstractions, unclear variable or function names, comments that describe obvious code, nested ternary operators (prefer switch/if-else chains)
3. **Unclear code**: choose clarity over brevity — explicit code is better than overly compact code. Consolidate related logic, but not at the cost of readability
4. **Over-simplification**: overly clever solutions that are hard to understand, too many concerns combined into single functions or components, "fewer lines" prioritized over readability (dense one-liners, nested ternaries), helpful abstractions removed that were aiding code organization
5. **Dead weight**: unnecessary comments, redundant code, abstractions that add indirection without value

## Step 3: Fix Issues

Apply each fix directly, skipping false positives. Only edit files — do not stage, build, or test.

When done, briefly summarize what was fixed (or confirm the code was already clean).
