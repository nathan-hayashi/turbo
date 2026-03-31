---
name: peer-review-spec
description: "Run an independent peer review of a specification document. Returns structured findings without applying fixes. Use when the user asks to \"peer review my spec\", \"get a second opinion on my spec\", or \"review my spec independently\"."
---

# Peer Review Spec

AI-powered spec review via codex. Delegates to `/codex-exec` for an independent review of the specification document.

## Step 1: Identify the Spec

Determine the spec to review:

- If **spec text** was provided by the caller, use it
- If a **spec file path** was provided, read the file
- If **neither** was provided, check for a spec at `.turbo/spec.md`, or look in the current conversation context

## Step 2: Run `/codex-exec` Skill

Run the `/codex-exec` skill in read-only mode with the full spec text and this prompt:

```
<task>
Review the following specification document for issues that would cause problems during implementation planning. Challenge the design direction: question whether the proposed system design is the simplest safe option and identify assumptions about users, environment, or dependencies that could break.
</task>

<dig_deeper_nudge>
After surface-level issues, check for failure scenarios the spec does not account for: partial failure, race conditions, rollback safety, stale state, and data loss.
</dig_deeper_nudge>

<structured_output_contract>
For each issue, state: (1) the problem, (2) where in the spec it occurs, (3) impact on planning or implementation, (4) a suggested fix, and (5) priority: P0 (fundamentally flawed), P1 (significant gap), P2 (moderate issue), P3 (minor improvement).
Ignore stylistic preferences and minor wording. If no issues are found, state that the spec looks sound.
</structured_output_contract>
```

## Step 3: Return Findings

Return the codex review output. The caller determines what to do with the findings.
