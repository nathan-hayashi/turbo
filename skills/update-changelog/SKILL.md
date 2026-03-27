---
name: update-changelog
description: "Update the Unreleased section of CHANGELOG.md based on current changes. No-op if CHANGELOG.md does not exist. Use when the user asks to \"update changelog\", \"add to changelog\", \"update the changelog\", \"changelog entry\", \"add changelog entry\", or \"log this change\"."
---

# Update Changelog

Update the Unreleased section of CHANGELOG.md based on the current changes.

## Step 1: Check for CHANGELOG.md

Use `git rev-parse --show-toplevel` to find the repository root. Look for CHANGELOG.md there. If it does not exist, stop silently without output. Do not create it.

## Step 2: Analyze the Changes

Determine what changed:

- Read `git diff --cached` for staged changes
- If nothing is staged, read `git diff` for unstaged changes
- Use the conversation context for the intent behind the changes

## Step 3: Assess Changelog-Worthiness

Not every change belongs in a changelog. Changelogs are for humans, not machines.

**Skip** changes that are purely internal:

- Refactoring with no user-facing impact
- Code formatting, linting, whitespace
- Test additions or modifications (unless they indicate a fixed bug)
- CI/CD configuration
- Developer tooling (linters, editor config)
- Documentation updates (README, comments, docstrings)
- Dependency bumps with no behavior change
- Fixes to code introduced by the same branch/PR — these are refinements of the in-progress feature, not separate changelog events

**Include** changes that affect users:

- New features or capabilities
- Changes to existing behavior
- Deprecated or removed functionality
- Bug fixes
- Security patches

If no changes are changelog-worthy, stop silently.

## Step 4: Check Existing Unreleased Entries

Read the current Unreleased section of CHANGELOG.md. Look for entries that relate to the same feature or fix. This prevents duplicates across multiple commits for the same body of work.

- If an existing entry covers the same change, update its wording only if the current commit meaningfully extends or refines the feature. Do not add a duplicate entry.

## Step 5: Update the Unreleased Section

Add or update entries in the Unreleased section using the standard change types: Added, Changed, Deprecated, Removed, Fixed, Security.

- Create subsection headers as needed (e.g., `### Added`)
- Maintain this subsection order: Added, Changed, Deprecated, Removed, Fixed, Security
- Each entry: concise, human-readable, written from the user's perspective
- Use imperative present tense without trailing periods (e.g., "Add dark mode support" not "Added dark mode support.")
- One bullet point per distinct change

## Rules

- Never modify released version sections. Only the Unreleased section is in scope.
- Entries describe what changed for the user, not implementation details (e.g., "Add dark mode support" not "Implement dark mode toggle component in ThemeProvider").
- Do not stage the modified file. The caller is responsible for staging.
