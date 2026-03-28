# botir-skills

Small, practical `skills.sh`-compatible skills collected in one repo.

## Skills

- `rust-performance` - measurement-first Rust optimization guidance distilled from The Rust Performance Book

## Install

List everything in the repo:

```bash
npx skills add botirk38/botir-skills --list
```

Install one skill:

```bash
npx skills add botirk38/botir-skills --skill rust-performance
```

Install from a local checkout:

```bash
npx skills add /Users/botirkhaltaev/repos/botir-skills --skill rust-performance
```

## Repo Layout

- `skills/<name>/SKILL.md` - the skill entrypoint required by `skills.sh`
- `skills/<name>/references/` - optional deep reference material
- repo root `README.md` - repo overview and publishing notes
- repo root licenses - carried attribution where a skill is derived from upstream material

## Publishing To skills.sh

There is no separate manual publishing flow.

If the repo is public on GitHub and contains valid `SKILL.md` files, it is installable immediately with `npx skills add owner/repo` or `--skill <name>`.

To make a new skill publishable from this repo:

1. Create `skills/<skill-name>/SKILL.md`
2. Add YAML frontmatter with at least:

```md
---
name: skill-name
description: What the skill does and when to use it
---
```

3. Push the repo to GitHub
4. Test it with:

```bash
npx skills add botirk38/botir-skills --list
npx skills add botirk38/botir-skills --skill <skill-name>
```

Notes:

- `skills.sh` discovers skills from standard folders such as `skills/`
- a skill can be installed before it appears prominently on the public leaderboard
- leaderboard visibility comes from ecosystem discovery and installs, not from a separate submission form

## Authoring Guidelines

- Keep `SKILL.md` action-oriented and short enough to load well in an agent
- Move detail, examples, and long checklists into `references/`
- Prefer distilled guidance over copy-pasting upstream docs
- Preserve attribution and license files when adapting outside material
- Keep names lowercase and hyphenated

## Attribution

The `rust-performance` skill is a distilled derivative of The Rust Performance Book by Nicholas Nethercote and contributors.

- Source: `https://github.com/nnethercote/perf-book`
- License: `MIT OR Apache-2.0`

This repo does not mirror the upstream book verbatim.
