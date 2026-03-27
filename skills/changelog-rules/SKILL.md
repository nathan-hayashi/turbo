---
name: changelog-rules
description: "Shared changelog conventions and formatting rules referenced by /create-changelog and /update-changelog. Not typically invoked directly."
---

# Changelog Rules

## Entry Format

- Imperative present tense without trailing periods (e.g., "Add dark mode support")
- Concise, human-readable, written from the user's perspective
- Describe what changed for the user, not implementation details (e.g., "Add dark mode support" not "Implement dark mode toggle component in ThemeProvider")
- One bullet point per distinct change

## PR and Issue References

Reference both the PR and any associated GitHub issue in each entry using inline parenthetical format with linked numbers in ascending order.

```markdown
- Add dark mode support ([#38](https://github.com/owner/repo/issues/38), [#42](https://github.com/owner/repo/pull/42))
```

To discover associated issues for a PR, run:

```bash
gh pr view <number> --json closingIssuesReferences --jq '.closingIssuesReferences[].number'
```

- If there is no associated issue, reference only the PR
- If there is no PR (e.g., backfilling from git tags), omit references

## Change Types

Standard types in this order when present: Added, Changed, Deprecated, Removed, Fixed, Security. Omit empty sections.

## Section Format

- ISO 8601 dates (`YYYY-MM-DD`)
- Reverse chronological order (newest first)
- Blank line between each section header and its content
- Version comparison links at the bottom, derived from the repository's remote URL
- Detect whether the project uses `v`-prefixed tags (e.g., `v1.0.0`) or bare tags (e.g., `1.0.0`) and match that convention in comparison links
