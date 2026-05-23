# Loop 1 Retrospective

Date: 2026-05-23
Loop: 1 (Ring 1)
Outcomes shipped: O1.1 (dq-rubric), O1.2 (dq-outcome), O1.3 (Ring-1 subagent)
Verdict: PASS (35/35 across two rubrics + their meta-rubric)

## What worked

- **3-atomic-outcomes-per-loop cadence held.** Each outcome was independently
  shippable. O1.1 didn't block on O1.2; O1.3 used both as inputs.
- **Ring 1's deliberate constraints helped.** Single-shot, no agentic loop,
  no LLM grading. Forced us to prove the static-grading core works before
  adding cognitive load.
- **Self-application caught a real defect.** When dq-rubric graded itself,
  C5 (citations) flagged that 5/7 criteria failed the regex
  `\*\*Citation:\*\*\s*(https?://|inference)` because URLs were written without
  the `https://` scheme prefix.

## What broke / what we learned

- **Citation regex was too strict.** Real-world citations include
  - URLs with scheme: `https://platform.claude.com/...`
  - URLs without scheme: `platform.claude.com/...`
  - Repo-relative paths: `opensubagents-ai/.github/CLAUDE.md`
  - Explicit inference label: `inference`
  Loop 1 fixed by adding `https://` to all URLs in the rubrics. Loop 2
  widens the regex to accept all four forms.

- **C6 (LLM-graded pass_threshold check) is a stub in Ring 1.** The static
  check (comparator + logic operator + number) accepts most well-formed
  thresholds but can't catch genuinely-ambiguous ones like "mostly passing."
  Loop 2 (Ring 2) hands C6 to an actual LLM call.

- **C7 (self-application) is a tautology in Ring 1.** The static check just
  greps for the string "dq-rubric" or "self-application" in the rubric.
  Loop 2 makes C7 a recursive call to the grader on the meta-rubric itself.

## Decisions

1. Keep the citation regex widening for Loop 2 (`https?://|inference|<repo>/<path>.md`).
2. C6 + C7 upgrade in Ring 2 (LLM-graded + recursive).
3. Add a `## Self-application` footer to every rubric so C7 has a textual
   anchor before the recursive check.

## Loop-2 inputs locked

- O2.1: Ring 2 subagent (agentic loop, LLM C6, recursive C7).
- O2.2: dq-eval skill + rubric.
- O2.3: dq-test skill + rubric.
