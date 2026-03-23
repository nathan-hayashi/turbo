---
name: consult-codex
description: "Multi-turn consultation with Codex CLI for second opinions, brainstorming, or collaborative problem-solving. Use when the user asks to \"consult codex\", \"ask codex\", \"get codex's opinion\", \"brainstorm with codex\", \"discuss with codex\", or \"chat with codex\"."
---

# Consult Codex

Multi-turn consultation with Codex CLI. Maintains a conversation across multiple turns using session persistence, unlike single-shot `/codex-exec`.

## Step 1: Gather Context

Identify the 2-5 files most relevant to the problem. Formulate a clear, specific question. Include what has been tried and relevant constraints.

## Step 2: Start Session

Run `codex exec` with `-o` to capture the response cleanly. Default to `-s read-only` for safety. Use `-s workspace-write` when the consultation requires running code or reading files outside the workspace.

```bash
codex exec -s read-only -o "$TMPDIR/codex-consult.txt" "<question with full context>"
```

Parse the `session id:` line from the CLI output. This UUID is needed for follow-up turns.

Use a generous timeout (30 minutes / 1800000ms per turn).

## Step 3: Read and Evaluate Response

Read the response from `$TMPDIR/codex-consult.txt`. Assess whether:
- The answer is sufficient and actionable
- Follow-up questions would improve the answer
- The response contradicts known project facts (verify before accepting)

If no follow-up is needed, skip to Step 5.

## Step 4: Follow Up

Resume the session with the parsed session ID (not `--last`, which is unsafe for parallel use):

```bash
codex exec resume <session-id> -o "$TMPDIR/codex-consult.txt" "<follow-up question>"
```

The `-s` flag is not available for `resume`. It inherits sandbox settings from the original session.

Return to Step 3. Cap at 5 turns to prevent runaway conversations.

## Step 5: Synthesize

Summarize the key insights from the consultation. Cross-reference suggestions with project documentation and conventions before applying. Codex suggestions are starting points, not guaranteed solutions.
