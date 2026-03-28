---
name: plan-style
description: "Planning conventions for task tracking, skill loading, and finalization. Use when writing implementation plans, or when the user asks to \"load plan style\", \"plan conventions\", or \"how should I structure a plan\"."
---

# Plan Style

Use `EnterPlanMode` to enter plan mode if not already in it. When writing an implementation plan, include these three elements:

1. **Task tracking** — A section at the top so the implementation session can track progress via `TaskCreate`
2. **Skills line** — An instruction to load relevant skills before making edits
3. **Finalize step** — A final step instructing to run the `/finalize` skill after implementation

For non-trivial plans, a pattern survey grounds the approach in existing code, and a codex consultation refines the plan before presenting it (see Pattern Survey and Plan Review below).

## Task Tracking

Add a "Task Tracking" section near the top of the plan (after the title, before the first step).

If the plan has few steps or each step is small (e.g., one edit per file), use a single implementation task:

```markdown
## Task Tracking

At the start, use `TaskCreate` to create a task for each item:

1. Implement the plan
2. Run `/finalize` skill
```

If the plan has substantial, distinct steps, create a task per step:

```markdown
## Task Tracking

At the start, use `TaskCreate` to create a task for each step:

1. [Step 1 label]
2. [Step 2 label]
3. ...
N. Run `/finalize` skill
```

Always include "Run `/finalize` skill" as the last task.

## Skills Line

Identify currently available skills from the skill list in the system prompt. Determine which skills are relevant for this plan's work by comparing the work type against each skill's trigger description.

Add an instruction to the plan: "After plan approval and before making edits, run `/skill-a`, `/skill-b`."

## Pattern Survey

Before proposing an implementation approach, search the codebase for analogous features. If the plan adds behavior similar to something that already exists (e.g., a new validation, a new endpoint, a new query), find all existing instances and study how they work.

Include a brief "Existing patterns" note in the plan that lists what was found and states whether the proposed approach follows or deviates from them. If it deviates, explain why.

**Skip** when the change has no clear analogue in the codebase (greenfield feature, new integration, unique logic).

## Plan Review

After drafting the plan and before presenting it to the user, when the plan introduces new abstractions, changes interfaces between components, or spans 3+ files:

1. Run the `/consult-codex` skill with the full plan text. Ask for critique of the plan.
2. Run the `/evaluate-findings` skill on the codex response to triage the suggestions.
3. Incorporate accepted findings into the plan.

**Skip** codex consultation for simple plans (single-file changes, straightforward additions). When in doubt, skip.

## Finalize Step

Add a final step to the plan:

```markdown
## Step N: Run `/finalize` Skill

Run the `/finalize` skill to run tests, simplify code, review, and commit.
```
