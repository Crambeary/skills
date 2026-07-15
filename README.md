# skills

Personal agent skills, installable via [`npx skills`](https://github.com/vercel-labs/skills).

[![skills.sh](https://skills.sh/b/Crambeary/skills)](https://skills.sh/Crambeary/skills)

## Install

```bash
npx skills@latest add Crambeary/skills
```

Install a specific skill:

```bash
npx skills@latest add Crambeary/skills --skill <skill-name>
```

## Reference

- **[explain-diff-html](skills/explain-diff-html/SKILL.md)** — Turn a diff, branch, or PR into a rich, self-contained HTML explainer: background, intuition, a code walkthrough, and a quiz.

## Layout

Each skill lives in its own directory under `skills/`, with a `SKILL.md`
containing YAML frontmatter (`name`, `description`, optional `metadata`).

```
skills/
  <skill-name>/
    SKILL.md
    (optional supporting files: scripts/, references/, etc.)
```

## Adding a new skill

```bash
npx skills@latest init skills/<skill-name>
```

Then edit the generated `SKILL.md`.

## Local development

To iterate on skills in this repo with live changes, symlink them into your
local agent skill directories instead of reinstalling after every edit:

```bash
scripts/link-skills.sh
```

This links each skill into `~/.claude/skills` and `~/.agents/skills`. Re-run
it after adding, removing, or renaming a skill.
