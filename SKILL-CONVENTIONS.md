## Skill Conventions

- SKILL.md frontmatter has `name` and `description` — description includes trigger phrases
- Skills should not reference which pipelines call them (stay self-contained)
- Workflow skills should not embed implementation details of delegated skills (downstream CLI commands, tool-specific flags, model coupling in reference materials). The skill interface is the abstraction boundary.
- Workflow skills use `TaskCreate` for phase tracking
- Skills communicate through standard interfaces: git staging area, PR state, file conventions at `.turbo/`
- Skills should be context-agnostic: accept caller-specified context but determine their own when called standalone (from conversation context or git state). See `/simplify-code` as the model.
- Analysis review skills accept a standardized scope interface: a diff command OR a file/directory list. In diff mode, only flag issues introduced by the changeset. In file scope mode, all issues in the reviewed files are in scope.
- Skills should avoid side effects outside their domain. Let the caller or a dedicated skill handle cross-cutting concerns (e.g., staging files).
- Steps that primarily run a skill use "Run `/skill-name` Skill" for headings and "Run `/skill-name` skill" for task tracking items. Steps with their own logic use human-readable names (e.g., "Deterministic Cleanup", "Commit and PR").
- Run `/create-skill` when creating or editing skills
- When adding a new skill, update README.md: add it to the appropriate table in "All Skills" and update any relevant prose sections
