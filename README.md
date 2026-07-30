# Personal Agent Skills

This repository contains personal agent skills for recurring development tasks. Each skill documents decision criteria, workflow, and safety checks so AI agents can work consistently across supported agent runtimes.

> This is an experimental collection for a personal development workflow. The skills are designed to be agent-neutral, but their exact installation and invocation may need to be adapted to the runtime you use.

## Skills

| Skill                                                                        | Purpose                                                                                              | Recommended activation                                     |
| ---------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- | ---------------------------------------------------------- |
| [`improve-folder-architecture`](skills/improve-folder-architecture/SKILL.md) | Inspects a repository's folder structure and proposes evidence-based improvements in an HTML report. | Explicitly select `improve-folder-architecture`            |
| [`dart-melos-upgrade`](skills/dart-melos-upgrade/SKILL.md)                   | Audits and upgrades dependencies in a Dart/Melos monorepo to the latest resolvable versions.         | Select explicitly or activate for dependency-upgrade tasks |

See each skill's `SKILL.md` for detailed behavior and limitations.

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

MIT License. See [`LICENSE`](LICENSE) for details.
