# explain-diff-html

Generates a rich, self-contained HTML explanation — background, intuition, code
walkthrough, and a 5-question quiz — of a code change, diff, branch, or PR.

Thanks to [Geoffrey Litt](https://gist.github.com/geoffreylitt/a29df1b5f9865506e8952488eac3d524)
for the original `explain-diff-html` recipe, and to
[ankitg12](https://gist.github.com/ankitg12/8e808d387799de4e9839bc393f8e6405) for
extracting the repeated CSS/JS page scaffold into a `render.py` content-spec renderer
— which also fixed the quiz answer-position bias that two other commenters (Butanium,
fm1randa) independently flagged in the gist's comment thread.

## Files

- `SKILL.md` — the skill instructions
- `scripts/render.py` — renders a JSON content spec into the final HTML page, so the
  LLM only ever writes content (prose, diagrams, quiz), never the CSS/JS boilerplate

## Scope

HTML output only. The source gist also has a Notion variant
(`explain-diff-notion.md`) that this skill intentionally does not port.
