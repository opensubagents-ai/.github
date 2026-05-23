# Loop 2 Retrospective

Date: 2026-05-23
Loop: 2 (Ring 2)
Outcomes shipped: O2.1 (Ring-2 subagent), O2.2 (dq-eval), O2.3 (dq-test)
Verdict: PASS after two in-flight fixes (all 4 rubrics green)

## What worked

- **Loop-1 retro inputs landed cleanly.** Citation regex widened, C6 +
  C7 upgraded, self-application footer added.
- **Ring-2 agentic loop shape matched Anthropic's tutorial.** Tool dispatch
  → run grader → feed gaps → iterate, bounded by max_iterations=3.
- **C6 degraded-mode fallback was the right call.** No ANTHROPIC_API_KEY
  in the sandbox; instead of failing, Ring 2 falls back to a stronger
  code-check (comparator + logic + number) and explicitly marks the score
  with `[code-stub, degraded — no API key]`. Operator can run the system
  today AND upgrade in place when the key is set.
- **Recursive C7 actually catches things.** When dq-rubric grades itself,
  the recursive call uses Ring-2 grading throughout — no static-only
  fallback inside the recursion.

## What broke / what we learned

- **Two defects caught in-flight by the grader on the new rubrics:**

  1. dq-eval and dq-test both failed C7 (self-reference) because they
     didn't contain the strings `dq-rubric` or `self-application`. Fixed
     by adding a `## Self-application` footer to every new rubric:
     "This rubric should pass dq-rubric. Run python3 ...ring2.py <this>
     and confirm verdict=PASS." Loop-3 deliverable: a template that
     pre-fills this footer so future rubrics can't forget.

  2. dq-test C5 (citations) failed because one citation pointed at
     `opensubagents-ai/.github/CLAUDE.md` (a real org-canonical doc) but
     the regex only accepted `https?://` or `inference`. Fixed by
     widening the regex to also accept `<repo>/<path>.md` patterns.

- **The loop bounds matter.** max_iterations=3 means after 3 iterations
  without convergence, the grader reports NEEDS_WORK rather than spiraling.
  This caught my "fix one thing, break another" pattern early — by the
  third iteration the gaps either stabilize (PASS) or oscillate (clear
  signal that the artifact has competing constraints).

## Decisions

1. Add a rubric template in `agents-rubrics/templates/_rubric_template.md`
   pre-filled with the self-application footer (Loop 3 deliverable).
2. Lift the static grader into `lib/static_grader.py` so Rings 3-5 can
   import it cleanly without sys.path tricks.
3. The session-level grader (Ring 3) needs a SKILL.md-specific grader
   because the meta-rubric only covers rubric files, not skills.

## Loop-3 inputs locked

- O3.1: Ring-3 subagent (parallel multi-artifact grading, SKILL.md
  grader S1-S6, lib refactor, rubric template).
- O3.2: dq-prompt skill + rubric.
- O3.3: dq-research-prompt skill + rubric.
