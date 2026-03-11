---
name: stage-commit
description: Stage files and create a commit in one step with a message matching existing commit style. Use when the user asks to "stage and commit", "commit these changes", "commit my changes", or "make a commit".
---

# Stage and Commit Changes

Stage and commit changes with a message matching existing commit style.

## Commit Message Rules

- Match the style from `git log -n 10 --oneline`
- Concise and descriptive
- Imperative mood, present tense
- No commit description—summarize everything in the message

## Staging Rules

- Stage only the changes to commit
- Leave other unstaged changes alone

## Technical Note

- Use `git commit -m "message"` directly—do not use heredoc syntax (sandbox blocks temp file creation)
