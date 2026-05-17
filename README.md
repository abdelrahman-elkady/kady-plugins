# kady-skills

A Claude Code plugin bundling personal skills for planning and engineering workflows.

## Skills

| Skill | Description |
| :---- | :---------- |
| [`/kady-skills:draft-plan`](skills/draft-plan/SKILL.md) | Draft a self-contained implementation plan in the project's plans directory as a handover document another agent can ship independently. |

## Install

### Via `npx skills` (recommended)

[`skills.sh`](https://skills.sh) ships skills into any supported agent, including Claude Code:

```bash
# Browse the plugin's skills and pick interactively
npx skills add abdelrahman-elkady/kady-plugins -a claude-code

# Or install a specific skill directly
npx skills add abdelrahman-elkady/kady-plugins --skill draft-plan -a claude-code
```

### From a local clone (development)

```bash
claude --plugin-dir /path/to/kady-plugins
```

Then invoke a skill, e.g.:

```
/kady-skills:draft-plan
```

After editing a skill, run `/reload-plugins` inside Claude Code to pick up changes without restarting.

## Layout

```
kady-plugins/
├── .claude-plugin/
│   └── plugin.json          # Plugin manifest (name, description, version)
├── skills/
│   └── draft-plan/
│       └── SKILL.md         # Skill entrypoint with YAML frontmatter
└── README.md
```

Skills are auto-discovered from `skills/<skill-name>/SKILL.md`. Add new skills by creating a new folder under `skills/` with its own `SKILL.md` — no manifest update needed.
