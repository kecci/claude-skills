# claude-skills

Personal Claude Code skills. Live in `.claude/skills/`, so they load automatically when Claude Code runs in this repo.

| Skill                                                        | Does                                                                          |
| ------------------------------------------------------------ | ----------------------------------------------------------------------------- |
| [glab-mr-create](.claude/skills/glab-mr-create/SKILL.md)     | Open an MR for the current branch, title/description written from the commits |
| [glab-mr-comments](.claude/skills/glab-mr-comments/SKILL.md) | Read every comment/discussion on the MR, flag what is unresolved              |
| [glab-mr-resolve](.claude/skills/glab-mr-resolve/SKILL.md)   | Fix review comments, reply in-thread, resolve the discussions                 |
| [glab-mr-review](.claude/skills/glab-mr-review/SKILL.md)     | Review someone else's MR, post findings as inline threads                     |

## Use elsewhere

To get them in every project, not just this one:

```sh
ln -s "$PWD"/.claude/skills/*/ ~/.claude/skills/
```

Each skill is a directory with a `SKILL.md` — the filename is exact, `SKILLS.md` will not load.
