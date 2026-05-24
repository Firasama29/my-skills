# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repo Is

A Claude Code plugin (`my-skills`) containing personal workflow skills. Installing the plugin makes all skills available via the `Skill` tool in any Claude Code session.

```
/plugin install https://github.com/Firasama29/my-skills.git
```

## Plugin Structure

```
.claude-plugin/plugin.json   # Plugin metadata (name, version, author)
skills/
  <skill-name>/
    SKILL.md                 # Skill definition — the only required file per skill
```

There are no build steps, tests, or scripts. This is a pure markdown/config repository.

## Adding or Editing Skills

Each skill lives in `skills/<skill-name>/SKILL.md` with required YAML frontmatter:

```markdown
---
name: <skill-name>
description: <trigger rules — include example trigger phrases AND explicit non-triggers>
---
```

The `description` field is the auto-invoke trigger. Write it precisely: include "trigger on phrases like..." and "do NOT trigger when..." — this is what Claude uses to decide whether to invoke the skill automatically.

Skill content changes take effect immediately for local path installs. Remote users need to reinstall or `git pull`.

## Skills in This Plugin

| Skill | Purpose |
|---|---|
| `8020` | 80/20 prioritization for fast learning on any topic |
| `two-levels` | Dual-depth explanations (beginner + expert) for any concept |
| `weekly-review` | Coaching-style weekly review for Java learning, Medium writing, open-source work |
| `medium-article-drafter` | Full article workflow (outline → draft → SEO titles) for Javarevisited and AI publications |
| `article-validator` | Fact-checks technical articles against official docs using web search |
| `second-brain-ingest` | Ingests Obsidian vault files (`raw-sources/`, `my-notes/`) into `wiki/` knowledge base |
| `daily-tasks` | Suggests top 3 priority tasks to work on today |
| `list-of-pending-articles` | Lists pending articles from the cowork project |

## Git Workflow

**Always** push changes to a feature branch and open a PR against `main`. Never push directly to `main`.

1. Create a feature branch: `git checkout -b <descriptive-branch-name>`
2. Commit changes on the branch
3. Push: `git push -u origin <branch-name>`
4. Open a PR: `gh pr create --base main`

## Key Conventions

- `second-brain-ingest` operates on a separate vault at `/home/firas/private-projects/claude/my-second-brain` — never modify `raw-sources/` or `my-notes/` there, only the `wiki/` layer.
- `medium-article-drafter` targets two distinct audiences: Java/backend (Javarevisited, mid-level) and AI/Claude tools (beginner-friendly). The skill handles tone switching automatically.
- `article-validator` always runs `web_search` — it must not rely on memory alone, even for familiar topics.
- `weekly-review` is scoped to learning and side projects only (Java, Medium, open-source, tools/workflows) — not work deliverables.
