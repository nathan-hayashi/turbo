---
name: create-changelog
description: "Create a CHANGELOG.md following keepachangelog.com conventions with version history backfilled from GitHub releases or git tags. Use when the user asks to \"create a changelog\", \"add a changelog\", \"initialize changelog\", \"start a changelog\", \"set up changelog\", \"generate changelog\", or \"backfill changelog\"."
---

# Create Changelog

Create a CHANGELOG.md following [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) conventions, backfilled with version history.

## Step 1: Load Changelog Rules

Run `/changelog-rules` to load shared changelog conventions.

## Step 2: Backfill Version History

Collect release history from the most authoritative source available:

1. **GitHub releases** (preferred): Run `gh release list --limit 100 --json tagName,name,publishedAt,body` to get release notes. For each release, parse the body into changelog entries.
2. **Git tags** (fallback): If no GitHub releases exist, run `git tag --sort=-v:refname` to list tags. For each consecutive tag pair, run `git log <older-tag>..<newer-tag> --oneline` to collect commit summaries.

For each version, classify entries into the standard change types per `/changelog-rules`. Use judgment to skip internal or trivial changes (CI config, formatting, dependency bumps with no behavior change).

## Step 3: Check for Existing CHANGELOG.md

If CHANGELOG.md already exists, warn the user and confirm before overwriting.

## Step 4: Write CHANGELOG.md

Write the file at the project root using this format:

```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [1.2.0] - 2024-03-15

### Added

- New feature description

### Fixed

- Bug fix description

[Unreleased]: https://github.com/<owner>/<repo>/compare/v1.2.0...HEAD
[1.2.0]: https://github.com/<owner>/<repo>/compare/v1.1.0...v1.2.0
[1.1.0]: https://github.com/<owner>/<repo>/releases/tag/v1.1.0
```

### Format Rules

Follow `/changelog-rules` conventions for entry format, change types, and section format. Additionally:

- Unreleased section always present at the top
- Version comparison links at the bottom, derived from the repository's remote URL

## Step 5: Present the Result

Briefly summarize how many versions were backfilled and which source was used (GitHub releases or git tags).
