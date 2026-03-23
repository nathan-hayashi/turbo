---
name: review-quality
description: "Multi-agent review for code reuse, quality, efficiency, and clarity issues. Returns structured findings without applying fixes. Use when the user asks to \"review code quality\", \"check for code reuse\", \"review efficiency\", \"check clarity\", or \"review quality\"."
---

# Review Quality

Launch four parallel review agents to analyze code for reuse, quality, efficiency, and clarity issues. Return structured findings.

## Step 1: Determine the Scope

Determine what to review:

- If a specific **diff command** was provided (e.g., `git diff --cached`), each review agent will run it independently to obtain the changes. Do NOT run the diff in the main agent's context.
- If a **file list or directory** was provided, each review agent will read and review those files directly.
- If **neither** was provided, determine the appropriate diff command (e.g., `git diff`, `git diff --cached`, `git diff HEAD`) based on the current git state. If there are no git changes, review the most recently modified files mentioned in the conversation.

## Step 2: Launch Four Review Agents in Parallel

Launch all four agents in a single message (`model: "opus"`, do not set `run_in_background`). Pass the scope from Step 1 to each agent.

### Agent 1: Code Reuse Review

For each change:

1. **Search for existing utilities and helpers** that could replace newly written code. Look for similar patterns elsewhere in the codebase — common locations are utility directories, shared modules, and files adjacent to the changed ones.
2. **Flag any new function that duplicates existing functionality.** Suggest the existing function to use instead.
3. **Flag any inline logic that could use an existing utility** — hand-rolled string manipulation, manual path handling, custom environment checks, and similar patterns are common candidates.

### Agent 2: Code Quality Review

Review for hacky patterns:

1. **Redundant state**: state that duplicates existing state, cached values that could be derived, reactive subscriptions that could be direct calls
2. **Parameter sprawl**: adding new parameters to a function instead of generalizing or restructuring existing ones
3. **Copy-paste with slight variation**: near-duplicate code blocks that should be unified with a shared abstraction
4. **Leaky abstractions**: exposing internal details that should be encapsulated, or breaking existing abstraction boundaries
5. **Stringly-typed code**: using raw strings where constants, enums, or dedicated types already exist in the codebase
6. **Unnecessary wrapper nesting**: container elements or wrapper layers that add no structural or layout value

### Agent 3: Efficiency Review

Review for efficiency:

1. **Unnecessary work**: redundant computations, repeated file reads, duplicate network/API calls, N+1 patterns
2. **Algorithmic complexity**: nested iterations, repeated linear searches replaceable by sets/maps, missing early exits
3. **Missed concurrency**: independent operations run sequentially when they could run in parallel
4. **Hot-path bloat**: new blocking work added to startup or per-request hot paths
5. **Unnecessary existence checks**: pre-checking file/resource existence before operating (TOCTOU anti-pattern) — operate directly and handle the error
6. **Memory**: unbounded data structures, missing cleanup, resource leaks
7. **Overly broad operations**: reading entire files when only a portion is needed, loading all items when filtering for one

### Agent 4: Clarity and Standards Review

Review for clarity, standards, and balance:

1. **Project standards**: coding conventions from CLAUDE.md not followed — import sorting, naming conventions, component patterns, error handling patterns, module style
2. **Unnecessary complexity**: deep nesting, redundant abstractions, unclear variable or function names, comments that describe obvious code, nested ternary operators (prefer switch/if-else chains)
3. **Unclear code**: choose clarity over brevity — explicit code is better than overly compact code. Consolidate related logic, but not at the cost of readability
4. **Over-simplification**: overly clever solutions that are hard to understand, too many concerns combined into single functions or components, "fewer lines" prioritized over readability (dense one-liners, nested ternaries), helpful abstractions removed that were aiding code organization
5. **Dead weight**: unnecessary comments, redundant code, abstractions that add indirection without value

## Step 3: Aggregate and Return Findings

Wait for all four agents to complete. Aggregate their findings into the output format below. Do not apply fixes.

## Output Format

Return findings as a numbered list. For each finding:

```
### [P<N>] <title (imperative, <=80 chars)>

**File:** `<file path>` (lines <start>-<end>)
**Category:** <reuse | quality | efficiency | clarity>

<one paragraph explaining the issue and what should change>
```

After all findings, add:

```
## Overall Verdict

**Code Quality:** <clean | issues found>

<1-3 sentence summary>
```

If no issues are found, state that the code is clean and explain briefly.
