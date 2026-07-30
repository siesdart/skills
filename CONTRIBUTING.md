# Contributing

The skills in this repository turn recurring development problems into small, reusable workflows.

## Before opening a change

- Confirm that the skill's purpose and scope can be explained in one sentence.
- Check that its name and responsibility do not overlap with an existing skill.
- Preserve repository-local instructions and current user changes.
- Document clear approval conditions for risky changes or external-system operations.

## Skill checklist

For a new skill or modification, verify the following:

- `SKILL.md` has valid YAML frontmatter.
- `name` matches the directory name.
- `description` communicates when the skill applies and what it covers.
- The workflow is reproducible and includes completion and stop conditions.
- Required reference files are linked with relative paths.
- Example paths and commands match the actual repository structure.

## Pull requests

Include the following in the pull request description:

1. The recurring task being solved
2. Key decision criteria and intentionally excluded scope
3. Usage examples
4. Commands or manual checks used for validation
5. Changes that affect existing skill users

Keep small changes focused on one purpose. When changing skill behavior, update the relevant README or skill documentation examples as well.

## Validation

This repository currently has no shared executable test suite for all skills. Instead, simulate a representative task for the changed skill and verify the following:

- The skill is invoked only in its intended situations.
- It stops safely when input is insufficient or out of scope.
- Expected artifacts, file links, and paths exist.
- It does not modify unrelated existing files.
