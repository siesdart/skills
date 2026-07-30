# Personal Agent Skills

This repository contains personal agent skills for recurring development tasks. Each skill documents decision criteria, workflow, and safety checks so AI agents can work consistently across supported agent runtimes.

> This is an experimental collection for a personal development workflow. The skills are designed to be agent-neutral, but their exact installation and invocation may need to be adapted to the runtime you use.

## Skills

| Skill                                                                        | Purpose                                                                                              | Recommended activation                                     |
| ---------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- | ---------------------------------------------------------- |
| [`improve-folder-architecture`](skills/improve-folder-architecture/SKILL.md) | Inspects a repository's folder structure and proposes evidence-based improvements in an HTML report. | Explicitly select `improve-folder-architecture`            |
| [`dart-melos-upgrade`](skills/dart-melos-upgrade/SKILL.md)                   | Audits and upgrades dependencies in a Dart/Melos monorepo to the latest resolvable versions.         | Select explicitly or activate for dependency-upgrade tasks |

See each skill's `SKILL.md` for detailed behavior and limitations.

### Prerequisites for `improve-folder-architecture`

`improve-folder-architecture` is adapted from Matt Pocock's [`improve-codebase-architecture`](https://github.com/mattpocock/skills/tree/main/skills/engineering/improve-codebase-architecture) skill. To use its complete workflow, install the full [`mattpocock/skills`](https://github.com/mattpocock/skills) collection. The skill references supporting capabilities from that collection, and installing the full upstream set keeps those dependencies aligned as the upstream project evolves.

Install the upstream collection with the `skills` CLI, or use the equivalent installation mechanism provided by your AI agent runtime:

```bash
npx skills@latest add mattpocock/skills
```

The adapted skill can still inspect and propose folder-architecture candidates without the upstream collection, but the post-selection workflow and supporting design guidance will not be available.

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
