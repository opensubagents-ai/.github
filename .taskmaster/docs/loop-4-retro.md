# Loop 4 Retrospective

Date: 2026-05-23
Loop: 4 (Ring 4)
Outcomes shipped: O4.1 (Ring-4 subagent + error handling), O4.2 (dq-brief),
                  O4.3 (dq-task)
Verdict: PASS (16/16 — 8 rubrics + 8 SKILL.md files)

## What worked

- **Ring 4's error-handling discipline caught a real edge case.**
  Stress-test #1: pass a non-existent path. Result: returns
  `verdict=ERROR`, gap message `file not found: /tmp/nope.md`, exit code
  non-zero, no Python exception thrown. Exactly the shape Anthropic's
  tutorial Ring 4 specifies.
- **Stress-test #2 surfaced a real subtle:** empty rubric at
  /tmp/empty-rubric.md. Returns SKIP (not ERROR) because the dispatcher
  rightly says "this path doesn't match SKILL.md or /rubrics/, I don't
  know what to do with it." That's correct behavior — the dispatcher
  refuses to guess the artifact type from content.
- **All 16 artifacts (8 SKILL.md + 8 rubrics) grade PASS.** Loop-1 and
  Loop-2 fixes propagated cleanly; Loop-3 + Loop-4 introduced zero new
  defects. The template + retro discipline is working.

## What broke / what we learned

- **The dispatch heuristic is path-based, not content-based.** Today:
  `/rubrics/` in path → rubric grader; `SKILL.md` suffix → skill grader.
  A future Loop could add YAML frontmatter detection so an artifact_type
  field on the file decides routing. Not needed now; flag for later.
- **Three loops in a row with zero defects on new artifacts.** Either we
  found the steady state, or the test surface is too narrow. Loop 5's
  end-to-end demo against 3 *real* org artifacts (one rubric, one
  outcome, one task that pre-date this work) will test the latter.
- **Self-grading is now load-bearing.** Every new artifact goes through
  `ring4 <new-artifact> <its-rubric>` before commit. CI lint in Loop 5
  makes this enforced rather than optional.

## Decisions

1. Loop 5 starts the TS port. Ring 1 already exists in TS; Rings 2-5
   port via @anthropic-ai/sdk's Tool Runner API, which collapses the
   manual loop into `tool_runner(...).until_done()`.
2. Plugin assembly: `.claude-plugin/plugin.json` + `.mcp.json` + 10
   slash-command markdown files. CI lint workflow at
   `infra/.github/workflows/dq-lint.yml`.
3. End-to-end demo: pick 3 existing artifacts in the org (one rubric,
   one outcome doc, one task) and run dq-grade on each. Publish at
   `playbooks/demos/dq-demo-2026-05-23.md`.

## Loop-5 inputs locked

- O5.1: Ring-5 TS port (Tool Runner abstraction).
- O5.2: Plugin assembly (.claude-plugin/plugin.json + .mcp.json + 10
  slash-commands + CI lint workflow).
- O5.3: End-to-end demo against 3 existing org artifacts.

## Session-wide retro (across Loops 1-4)

Twelve atomic outcomes shipped. Every artifact graded against its own
rubric. Three real defects caught and fixed in-flight (citation regex,
relative-path citations, self-application footers). Loops 3-4 caught zero
defects, indicating the template-and-retro discipline closed the failure
modes Loops 1-2 surfaced. Total bundle: 8 skills + 8 rubrics + 4 Python
grader rings + 1 TS Ring + 1 PRD + 4 retros.
