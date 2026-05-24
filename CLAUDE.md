# my-skills

A personal Claude Code plugin containing skills for my day-to-day workflows.

## Installation

```
/plugin install https://github.com/Firasama29/my-skills.git
```

Once installed, skills are available via the `Skill` tool and listed at session start.

## Structure

```
.claude-plugin/plugin.json   # Plugin metadata
skills/                      # One subdirectory per skill
  <skill-name>/
    SKILL.md                 # Skill definition (required)
```

## Adding a New Skill

1. Create `skills/<skill-name>/SKILL.md` with this frontmatter:

```markdown
---
name: <skill-name>
description: <when to trigger this skill — be specific about trigger phrases and when NOT to trigger>
---

# Skill content here
```

2. The `description` field is critical — it controls when Claude auto-invokes the skill. Write it the same way you'd write a trigger rule: include example phrases that should trigger it and explicitly state when it should NOT trigger.

3. Commit and push. The plugin does not need to be reinstalled for skill content changes if already installed locally via a path — but a git pull or reinstall is needed for remote users.

## Editing a Skill

Edit the relevant `skills/<skill-name>/SKILL.md` directly. Keep the frontmatter intact.

## Skills in This Plugin

| Skill | Purpose |
|---|---|
| `8020` | Fast learning — focus on what matters most, cut information overload |
| `two-levels` | Explain concepts with both a simple and a deep answer |
| `second-brain-ingest` | Ingest new files from Obsidian vault into the wiki knowledge base |
| `weekly-review` | Structured weekly review session covering learning and side projects |
| `article-validator` | Fact-check technical articles against official documentation |
| `medium-article-drafter` | Draft Medium articles on Java/backend and AI/Claude topics |
| `daily-tasks` | Suggest priority tasks for the day |
| `list-of-pending-articles` | List pending articles in the cowork project |
