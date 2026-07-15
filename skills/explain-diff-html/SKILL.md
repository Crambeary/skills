---
name: explain-diff-html
description: Use when asked for a rich, interactive explanation of a code change, diff, branch, or PR, delivered as a self-contained HTML page. Produces background, intuition, a code walkthrough, and a 5-question quiz. HTML output only (see README for the Notion variant this intentionally leaves out).
metadata:
  author: Crambeary
  version: "0.1.0"
  source: https://gist.github.com/geoffreylitt/a29df1b5f9865506e8952488eac3d524
---

# explain-diff-html

Produce a single self-contained HTML page that teaches the reader how a specified
code change works — what changed, why, and how to check they actually understood it.

## Workflow

1. Identify the change: current diff, branch, PR, or user-supplied files. If ambiguous,
   infer the most likely target and say so in the page.
2. Explore the surrounding system broadly enough to explain old vs. new behavior, not
   just line-by-line edits. Prefer real tests/examples in the repo over speculation.
3. Draft the narrative before writing anything: what motivated the change, how the old
   system behaved, the smallest useful mental model of the new behavior, how the
   implementation realizes it, and any edge cases/trade-offs worth flagging.
4. Write a JSON content spec (schema below) — don't hand-write the CSS/JS/page
   scaffold, that's what `scripts/render.py` (bundled with this skill) is for.
5. Run `python3 scripts/render.py spec.json`. It writes
   `/tmp/YYYY-MM-DD-explanation-<slug>.html` by default (pass `-o` for a custom path).
   Keep the output outside the repo/vault and out of version control.
6. Verify before handing off: confirm the file exists, is a complete HTML document
   with no external asset dependencies, the quiz buttons actually toggle feedback, and
   code blocks preserve whitespace.
7. Return the absolute path to the generated file.

## Section content (the spec's `sections` array)

- **Background** — deep background for beginners (note it's skippable if the reader
  already knows it), then narrow background specific to this change.
- **Intuition** — the core idea, not full implementation detail. Toy examples with
  concrete data. Diagrams liberally.
- **Code** — high-level walkthrough of the changes, grouped/ordered for understanding
  (execution or dependency order), not diff order. Reference files/lines when useful,
  but don't dump the whole diff.

The quiz is a separate top-level `quiz` field in the spec, not a `sections` entry.

## Writing style

Kleppmann-style clarity: engaging, systems-oriented prose, smooth transitions between
sections, jargon explained on first use.

## Diagrams

Reuse a small set of diagram families across the page instead of inventing a new
visual style per section:

- simplified UI mockups for UI-facing changes;
- system/data-flow diagrams between components, with example data included.

Never ASCII art — use the renderer's `.diagram` / `.flow` / `.box` / `.box.fail`
classes (full list in the `scripts/render.py` docstring/CSS). For code, always
`<pre><code>...</code></pre>` — the renderer's CSS already sets `white-space: pre-wrap`
so newlines survive.

## Quiz quality

The quiz is the part most likely to go wrong. Two independent readers of the original
recipe (see README) reported the same failure: the correct option was guessable by
position or by being conspicuously longer, without reading the question at all.

- Ask about behavior, causality, or trade-offs — not something answerable by pattern-
  matching a phrase copied from the page.
- Keep option length/specificity/hedging comparable across choices in a question.
- Make every distractor a plausible real misunderstanding of the change, not a joke or
  an "all/none of the above."
- `render.py` randomizes option order for you at render time — write options in
  whatever order reads naturally when drafting the spec; don't hand-arrange position to
  fake randomness.

## Spec schema (for `scripts/render.py`)

```json
{
  "title": "Rewriting the retry logic: exponential backoff with jitter",
  "subtitle": "Prepared 2026-07-15 · PR #482",
  "slug": "retry-backoff-refactor",
  "sections": [
    {"id": "background", "heading": "Background", "html": "<p>...</p>"},
    {"id": "intuition", "heading": "Intuition", "html": "<div class=\"diagram\">...</div>"},
    {"id": "code", "heading": "Code walkthrough", "html": "<pre><code>...</code></pre>"}
  ],
  "quiz": [
    {
      "question": "Why did the first retry attempt fire immediately instead of waiting?",
      "options": [
        {"text": "The jitter calculation returned a negative delay.", "correct": false,
         "explanation": "Jitter is clamped to zero elsewhere in the diff — it can't go negative."},
        {"text": "The base delay was multiplied after the first attempt, not before it.", "correct": true,
         "explanation": "See the reordered multiply in the code walkthrough — that's exactly the fix."}
      ]
    }
  ]
}
```

Each option's `explanation` is optional but worth writing — it's what turns the quiz
feedback from a bare correct/incorrect into something that actually reinforces the
misconception behind a wrong pick.

`html` fields are raw HTML, not markdown — the script is a pure template renderer, so
all the explanatory judgment (what to cover, which diagrams to draw) stays with you.
Run `python3 scripts/render.py --help` for the full option/flag reference if you
haven't used it in a while.

## Output contract

Single self-contained HTML file — no external fonts, CDNs, images, or network calls.
Filename `YYYY-MM-DD-explanation-<slug>.html`, saved outside the repo (default `/tmp/`)
unless the user explicitly asks for it inside the repo.
