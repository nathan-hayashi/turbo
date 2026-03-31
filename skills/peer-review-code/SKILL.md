---
name: peer-review-code
description: "Run an independent peer review of code changes. Returns structured findings without applying fixes. Use when the user asks to \"peer review code\", \"peer review my code\", \"get a second opinion on my code\", or \"review against main\"."
---

# Peer Review Code

AI-powered code review of changes. Delegates to `/codex-review` by default.

## Usage

Determine what to review based on context:

### Diff Mode

- **Uncommitted changes**: run `/codex-review` with `--uncommitted`
- **Against a base branch**: run `/codex-review` with `--base <branch>`
- **Specific commit**: run `/codex-review` with `--commit <sha>`

Pass `--title` when reviewing a feature branch or PR to give the reviewer context.

### File Scope Mode

When a file list or directory is provided instead of a diff:

```bash
codex review "Review the following files for bugs, correctness, and security issues: <file list>"
```

Capture and return the full review output.
