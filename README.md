# journalSkills

A collection of agent skills for Claude Code, Codex, and other AI coding assistants.

## Skills

| Skill | Description |
|-------|-------------|
| [agent-md-refactor](skills/agent-md-refactor) | Refactor bloated AGENTS.md/CLAUDE.md files using progressive disclosure |
| [claude-md-improver](skills/claude-md-improver) | Audit and improve CLAUDE.md files against actual codebase |
| [claude-md-management](skills/claude-md-management) | Plugin for managing CLAUDE.md lifecycle |
| [command-creator](skills/command-creator) | Create agent commands with best practices |
| [create-agent](skills/create-agent) | Custom agent creation protocol |
| [database-schema-designer](skills/database-schema-designer) | Database schema design with SQL migration templates |
| [domain-name-brainstormer](skills/domain-name-brainstormer) | Brainstorm domain names |
| [humanizer](skills/humanizer) | Remove AI writing patterns from text |
| [lesson-learned](skills/lesson-learned) | Capture and apply software engineering lessons |
| [naming-analyzer](skills/naming-analyzer) | Suggest better variable, function, and class names |
| [perplexity](skills/perplexity) | Perplexity integration skill |
| [professional-communication](skills/professional-communication) | Professional communication templates and guides |
| [session-handoff](skills/session-handoff) | Create and resume AI session handoff documents |
| [skill-creator](skills/skill-creator) | Full skill creation and iteration framework |
| [skill-judge](skills/skill-judge) | 120-point evaluation rubric for skill quality |
| [update-docs](skills/update-docs) | Multi-agent documentation update workflow |
| [web-to-markdown](skills/web-to-markdown) | Convert web content to markdown |
| [write-concisely](skills/write-concisely) | Writing rules from Elements of Style |
| [writing-clearly-and-concisely](skills/writing-clearly-and-concisely) | Full Elements of Style with AI writing detection |

## Installation

```bash
# Clone the repo
git clone https://github.com/<username>/journalSkills.git

# Copy a skill to your agent's skills directory
cp -r journalSkills/skills/naming-analyzer ~/.claude/skills/
```

## Structure

Each skill follows a consistent format:

```
skills/
└── skill-name/
    ├── SKILL.md          # Required: skill definition
    ├── README.md         # Optional: human-readable overview
    ├── references/       # Optional: supporting docs
    ├── scripts/          # Optional: executable scripts
    └── assets/           # Optional: templates, images
```

## Creating New Skills

See [templates/minimal/](templates/minimal/) for a starter template.

## License

MIT
