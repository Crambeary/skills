# skills

Personal agent skills, installable via [`npx skills`](https://github.com/vercel-labs/skills).

## Install

```bash
npx skills add Crambeary/skills
```

Install a specific skill:

```bash
npx skills add Crambeary/skills --skill <skill-name>
```

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
npx skills init skills/<skill-name>
```

Then edit the generated `SKILL.md`.
