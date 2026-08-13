# Global agent preferences

Shared instructions for every AI coding session on this machine. They live in
`~/.config/agent/AGENTS.md`; each tool is pointed at this file (Claude Code via
`~/.claude/CLAUDE.md`). Project-level instructions take precedence over anything here.

**Editing this file or any skill:** these are chezmoi-managed. Edit the source in
`~/.local/share/chezmoi/dot_config/agent/`, then `chezmoi apply`. Editing `~/.config/agent/` directly
changes only this machine and is silently reverted by the next apply — the skills reference their own
deployed paths, so this is an easy mistake to make.

## Git & commits

- Commits: use the `commit` skill — it defines the message convention, how to validate
  against commitlint, and the commit workflow.
- PR/MR descriptions: use the `pr-description` skill.
- Prefer small, focused commits over one large mixed commit.
- Prefer rebasing and a linear branch history over merge commits.
- Never create commits unless I explicitly ask. Stage the changes, then pause so I can review
  what is staged; on my approval, commit and push together without asking again.
- Never `git push --force`. Only `git push --force-with-lease` if I explicitly ask.

## Code review

- For a read-only critique of changes, use the `review` skill.

## Writing

- Any prose I will read later — notes, skills, docs, READMEs, commit bodies, PR descriptions: use
  the `caveman` skill. Tight prose, no filler, full sentences.

## Notes & plans

My personal Obsidian vault is `~/Documents/Notes`.

- Reading or writing any note: use the `obsidian-notes` skill.
- Finding or creating a project, workstream or task, or updating status: use the `obsidian-project`
  skill.
- Planning work: use the `obsidian-plan` skill. Plans live in the vault, not in the repo or
  `~/.claude/plans` — offer to save one whenever a plan is drafted or approved.

## Code style

When writing or editing code, follow the conventions in `STYLE.md` (naming, comments,
simplicity, consistency). The `review` skill critiques against the same file.

@~/.config/agent/STYLE.md

## Working style

- Be concise. Lead with the answer, then the reasoning if needed.
- Ask before doing anything destructive or hard to reverse.
