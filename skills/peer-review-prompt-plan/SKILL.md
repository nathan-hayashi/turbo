---
name: peer-review-prompt-plan
description: "Run prompt plan review using the codex CLI as an independent reviewer. Returns structured findings without applying fixes. Use when the user asks to \"peer review my prompt plan\", \"get a second opinion on my prompts\", \"codex review my prompt plan\", or \"run codex on my prompts\"."
---

# Peer Review Prompt Plan

AI-powered prompt plan review via codex. Delegates to `/codex-exec` for an independent review of the prompt plan against its source spec.

## Step 1: Identify the Prompt Plan

Determine the prompt plan to review:

- If **prompt plan text** was provided by the caller, use it
- If a **file path** was provided, read the file
- If **neither** was provided, check for a prompt plan at `.turbo/prompts.md`

Also read the source spec (path listed in the prompt plan's `Source:` field).

## Step 2: Run `/codex-exec` Skill

Run the `/codex-exec` skill in read-only mode with the full prompt plan text, the source spec, and this prompt:

```
<task>
Review the following prompt plan against its source spec. Check for: spec requirements not covered by any prompt, dead ends where a prompt creates something no later prompt consumes, missing prerequisites where a prompt assumes something no prior prompt creates, duplicated requirements across prompts, and incorrect dependency ordering.
</task>

<structured_output_contract>
For each issue, state: the problem, which prompt(s) are affected, the impact, a suggested fix, and priority: P0 (spec requirement missing or system broken), P1 (significant gap), P2 (moderate issue), P3 (minor improvement).
Ignore stylistic preferences. If no issues are found, state that the prompt plan looks sound.
</structured_output_contract>
```

## Step 3: Return Findings

Return the codex review output. The caller determines what to do with the findings.
