# Messina Group fork notes

This is Messina Group's fork of [obra/superpowers](https://github.com/obra/superpowers).
It tracks upstream `main` and carries a small, deliberate set of local changes.

## Local divergence from upstream

### 1. `hooks/hooks.json` — SessionStart injection removed

Upstream ships a `SessionStart` hook that reads `skills/using-superpowers/SKILL.md`
and injects the whole thing into every session wrapped in `<EXTREMELY_IMPORTANT>`,
including instructions that Claude "ABSOLUTELY MUST" invoke a skill if there is
"even a 1% chance" one applies.

That makes Claude reach for a superpowers skill on nearly every turn, which is not
the behavior we want org-wide. We want the skills *available*, chosen when they are
actually the right tool.

We set `hooks` to an empty object rather than deleting the file, so upstream edits
to this file produce a small, readable rebase conflict instead of a delete/modify one.

**This does not hide the skills.** Plugin skills are listed to the model with their
frontmatter descriptions regardless of hooks, so Claude still discovers and invokes
`superpowers:brainstorming`, `superpowers:systematic-debugging`, and the rest on its
own when they fit the task.

If you later want a gentle nudge instead of nothing, restore the hook but replace
the injected text with a single neutral line pointing at the Skill tool.

## Staying current with upstream

```bash
./sync-upstream.sh
```

Fetches `upstream/main`, rebases our commits on top, pushes to
`Messina-Group/superpowers`. The repo is configured for a triangular workflow:
`main` tracks `upstream/main`, pushes default to `origin`, and `pull.rebase` is on.
The push URL for `upstream` is disabled so nobody pushes Messina commits to obra.

Expect the occasional conflict in `hooks/hooks.json` when upstream reworks hooks.
Resolve by keeping our empty `hooks` object unless upstream added something we want.

## Distribution

This repo is consumed as a plugin by the marketplace at
`Messina-Group/claude-plugins`, which is synced to the Claude organization via
**claude.ai > Organization settings > Plugins**.
