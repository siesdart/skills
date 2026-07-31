# Personal Agent Skills

This repository contains personal agent skills for recurring development tasks. Each skill documents decision criteria, workflow, and safety checks so AI agents can work consistently across supported agent runtimes.

> This is an experimental collection for a personal development workflow. The skills are designed to be agent-neutral, but their exact installation and invocation may need to be adapted to the runtime you use.

## Skills

| Skill                                                                                                    | Purpose                                                                                              | Recommended activation                                     |
| -------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- | ---------------------------------------------------------- |
| [`dart-melos-upgrade`](skills/dart-melos-upgrade/SKILL.md)                                               | Audits and upgrades dependencies in a Dart/Melos monorepo to the latest resolvable versions.         | Select explicitly or activate for dependency-upgrade tasks |
| [`flutter-apply-architecture-best-practices`](skills/flutter-apply-architecture-best-practices/SKILL.md) | Designs Flutter/Dart monorepos with Melos, Pub Workspaces, and get_it.                               | Select for monorepo architecture tasks                     |
| [`improve-folder-architecture`](skills/improve-folder-architecture/SKILL.md)                             | Inspects a repository's folder structure and proposes evidence-based improvements in an HTML report. | Explicitly select `improve-folder-architecture`            |

## Before using a skill

### `flutter-apply-architecture-best-practices`

`flutter-apply-architecture-best-practices` is a monorepo-focused variant of the same-named skill from Flutter's official [`flutter/skills`](https://github.com/flutter/skills) repository.

- The rest of the official `flutter/skills` collection can be installed and used alongside this skill.
- The official and local versions use the same directory name, so they cannot be installed together in one `skills` directory.
- When installing or updating the official collection, omit its `flutter-apply-architecture-best-practices` skill and install this repository's version in its place.
- This replacement applies only to the same-named skill; this version adds the repository's Melos, Dart Pub Workspaces, get_it, package-boundary, and composition-root policies.

### `improve-folder-architecture`

`improve-folder-architecture` is adapted from Matt Pocock's [`improve-codebase-architecture`](https://github.com/mattpocock/skills/tree/main/skills/engineering/improve-codebase-architecture) skill.

- For the complete workflow, install the full [`mattpocock/skills`](https://github.com/mattpocock/skills) collection so its supporting capabilities remain available and aligned.
- Install the upstream collection with the `skills` CLI, or use the equivalent mechanism provided by your agent runtime:

```bash
npx skills@latest add mattpocock/skills
```

The adapted skill can still inspect and propose folder-architecture candidates without the upstream collection, but its post-selection workflow and supporting design guidance will not be available.

## Installation

Install the skills with the `skills` CLI:

```bash
npx skills@latest add siesdart/skills
```

The CLI installs or registers the skills for the supported agent runtimes in your environment. If a skill with the same name already exists, back it up or compare the changes before installing it.

## Adding a skill

Add each new skill around a `skills/<skill-name>/SKILL.md` file. The core instructions should remain usable without relying on a specific vendor, model, or agent product.

- Use lowercase kebab-case for the directory name and the frontmatter `name`.
- Make the `description` specific about when the skill should and should not be used.
- Separate observation, modification, validation, and stop conditions in the instructions.
- Keep any required templates or visual assets inside the skill directory.
- For skills that modify repositories, preserve existing user changes and document pre- and post-run validation.

See [`CONTRIBUTING.md`](CONTRIBUTING.md) for proposal and change procedures.

## License

This repository is distributed under the MIT License. See [`LICENSE`](LICENSE) for the license covering this repository and [`THIRD-PARTY-NOTICES.md`](THIRD-PARTY-NOTICES.md) for attribution and license information for adapted upstream material.
