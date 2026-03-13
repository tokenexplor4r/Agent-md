# Contributing

Agent.md is open source. Add a skill, improve an existing one, fix a bug, or submit an example agent.

## Adding a New Skill

1. Create a directory under `skills/` with your skill name (e.g. `skills/my-skill/`).
2. Add a `SKILL.md` file with YAML frontmatter at the top:

```yaml
---
name: my-skill
description: One sentence describing what this skill does and when to use it.
metadata:
  author: your-name
  version: "1.0"
---
```

3. Write the skill instructions in markdown below the frontmatter.
4. If the skill needs templates, create a `templates/` subdirectory.
5. Update the root `README.md` to list your skill in the skills table.
6. Open a pull request.

## Skill Guidelines

Follow the [Agent Skills](https://agentskills.io) format. YAML frontmatter for metadata, markdown for instructions.

Write for AI agents, not humans. The instructions should be clear enough that an agent reading them can follow them without additional context.

Include code examples in TypeScript. Show complete, working patterns, not fragments.

Define how your skill integrates with other Agent.md skills. Every skill should document which other skills it references and why.

No filler. No corporate language. Be direct and technical.

## Improving Existing Skills

Open a pull request with your changes. Describe what you changed and why. If you are adding a new pattern or section, explain the use case.

## Submitting Example Agents

Add a directory under `examples/` with a `README.md` that covers: which skills the agent uses, setup steps, configuration, and the agent's prompt or loop structure. See existing examples for the format.

## Code of Conduct

Be direct. Be helpful. No spam. No AI slop.
