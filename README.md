# Agent Skills

My personal collection of agent skills for various development tasks and workflows.

## Installation

```bash
claude plugin marketplace add andyw8/agent-skills
claude plugin install setup-biome-jumpstart-pro@andyw8-skills
```

Or with `npx skills`:

```bash
npx skills add andyw8/agent-skills --skill setup-biome-jumpstart-pro -a claude-code -g
```

## Available Skills

| Skill | What it does |
| --- | --- |
| [reactionview](/skills/reactionview/SKILL.md) | Install or configure ReActionView in a Rails app for HTML-aware ERB rendering, validation, and a debug mode |
| [setup-biome-jumpstart-pro](/skills/setup-biome-jumpstart-pro/SKILL.md) | Setup Biome on Jumpstart Pro Rails apps for JavaScript linting, formatting, and import organization |

## Adding a Skill

Add `skills/<slug>/SKILL.md` with `name` (the same as the directory) and `description` in the front matter, then add a matching entry to the [marketplace manifest](/blob/main/.claude-plugin/marketplace.json) and a row in the table above. Whatever the skill needs at runtime goes in that same folder and ships with it.
