---
name: resolve-pr-comments
description: "Evaluate, fix, and reply to GitHub pull request review comments. Use when the user asks to \"resolve PR comments\", \"fix review comments\", \"address PR feedback\", \"handle review comments\", \"address review feedback\", \"respond to PR comments\", or \"address code review\"."
---

# Resolve PR Review Comments

Fetch unresolved review comments from a GitHub PR, critically evaluate each one, fix or skip based on confidence, and reply to each thread.

## Step 1: Fetch Comments

Fetch review threads and top-level review body comments from the PR:

```bash
gh api graphql -f query='
query($owner: String!, $repo: String!, $pr: Int!) {
  repository(owner: $owner, name: $repo) {
    pullRequest(number: $pr) {
      title url
      reviewThreads(first: 100) {
        nodes {
          id isResolved isOutdated
          comments(first: 50) {
            nodes { author { login } body path line }
          }
        }
      }
      reviews(first: 50) {
        nodes {
          author { login }
          body state
        }
      }
    }
  }
}' -f owner='{owner}' -f repo='{repo}' -F pr={pr_number}
```

Auto-detect owner, repo, and PR number from current branch if not provided. Filter review threads to unresolved only. Filter reviews to those with a non-empty body, excluding `PENDING` state (unsubmitted drafts).

## Step 2: Evaluate

Run the `/evaluate-findings` skill on the unresolved inline threads to assess each comment. Review body comments are not evaluated here — they are surfaced in Step 6 for manual attention.

## Step 3: Apply Findings

Run the `/apply-findings` skill on the evaluated results.

## Step 4: Finalize

If any fixes were applied, run the `/finalize` skill to polish, test, review, and commit the changes. The commit SHA from finalize is needed for reply messages.

If no fixes were applied, skip to Step 5.

## Step 5: Reply to Each Thread

Run `/github-voice` to load writing style rules before composing replies. Keep replies to one or two sentences. Avoid bullet-point reasoning or bolded labels.

**Review body comments** (top-level review comments with non-empty body) cannot be replied to via thread replies — they are not threads. Do not attempt to reply to them. Instead, list them in the summary (Step 6) as requiring manual attention.

For each processed **inline thread**, check whether it was resolved between fetching and replying (e.g., CodeRabbit auto-resolves its own threads after a push). Skip resolved threads. Reply to every remaining thread using:

```bash
gh api graphql -f query='
mutation($threadId: ID!, $body: String!) {
  addPullRequestReviewThreadReply(input: {pullRequestReviewThreadId: $threadId, body: $body}) {
    comment { id }
  }
}' -f threadId="THREAD_ID" -f body="REPLY_BODY"
```

**Reply format for fixes:**
```
Fixed in <commit-sha>.
```

Only add a brief description after the SHA if the fix meaningfully diverges from what the reviewer suggested. Otherwise, the commit SHA alone is enough.

**Reply format for skips:** Just state the reasoning for not changing it.

## Step 6: Summary

After processing all threads, present a summary table:

- Total unresolved inline threads found
- Number fixed (high + medium confidence)
- Number skipped (low confidence)
- Review body comments requiring manual attention (list author, state, and a one-line summary of each)
- List of files modified

## Rules

- Never resolve or dismiss a review thread — only reply. Let the reviewer resolve.
- Process comments in file order to minimize context switching.
- Stale references and default-to-skip policy are handled by the `/evaluate-findings` skill.
- When a thread has multiple comments (discussion), read the full thread before deciding.
- The first comment in each thread is the original review comment; subsequent comments are replies.
