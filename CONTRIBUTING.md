# Contributing to ALIVE

Thanks for your interest in contributing. ALIVE is open source and we welcome contributions — whether it's a bug fix, new skill, or documentation improvement.

## Getting Started

1. Fork the repository
2. Clone your fork locally
3. Install the plugin from your local copy for testing:
   ```bash
   claude plugin install /path/to/your/clone
   ```

## Project Structure

```
plugins/alive/
  skills/         12 skill folders, each with a SKILL.md
  rules/          6 rule files defining caretaker behavior
  hooks/          hooks.json + scripts/ for session lifecycle
  templates/      file templates for walnuts, capsules, companions
  onboarding/     first-run experience
  statusline/     terminal status bar integration
  scripts/        utility scripts (world index, context graph)
  CLAUDE.md       the core runtime definition
```

## Making Changes

### Skills
Each skill lives in `plugins/alive/skills/{name}/SKILL.md`. Skills define what the squirrel can do — they're invoked by the user via `/alive:{name}`.

### Rules
Rules live in `plugins/alive/rules/`. They define how the squirrel behaves — these are always loaded and always followed.

### Hooks
Hook scripts live in `plugins/alive/hooks/scripts/`. They fire on session lifecycle events (start, resume, compact) and tool use events (pre/post). The hook registry is `plugins/alive/hooks/hooks.json`.

### Templates
Templates in `plugins/alive/templates/` define the schema for system files. The squirrel reads these before writing any system file.

## Guidelines

- **Read before writing.** Understand the existing code before modifying it.
- **Frontmatter on everything.** Every `.md` file has YAML frontmatter. No exceptions.
- **Don't break backward compatibility.** Existing walnuts must keep working.
- **Test with a real world.** Create a test walnut and exercise your changes.
- **Keep skills focused.** One skill, one purpose.
- **Respect the stash.** Mid-session writes are limited to capture and capsule work only.

## Pull Requests

- Keep PRs focused on a single change
- Describe what changed and why
- Reference any related issues

## Reporting Issues

Use [GitHub Issues](https://github.com/alivecomputer/context/issues) for bugs and feature requests. Use the provided templates.

## Community

Join [Worldbuilders on Skool](https://skool.com/worldbuilders) for discussion, feedback, and show & tell.

## License

By contributing, you agree that your contributions will be licensed under the MIT License.
