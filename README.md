# agent-skills

Personal skills for Claude Code and Codex, installed with the [`skills`](https://github.com/vercel-labs/skills) CLI.

## Install on a machine

```bash
npx skills add theskillwithin/agent-skills -g -a claude-code codex -y
```

This puts each skill in `~/.agents/skills` and links it into `~/.claude/skills`. It works the same on macOS and Linux; the machine needs GitHub access to this private repo.

## Update

```bash
npx skills update -g -y
```

## Add a skill

Create `skills/<name>/SKILL.md`, push to `main`, then run the update on each machine.
