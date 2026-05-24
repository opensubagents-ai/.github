# Cross-repo change-set + commit conventions for opensubagents-ai

Org-wide source of truth for how commits are formatted across every
repository in `opensubagents-ai`. Read this before contributing to any
repo in the org.

First landed under outcome **OCSC1** — the canonical milestone for the
convention itself.

## TL;DR

Every commit follows Conventional Commits with an outcome-tag suffix:

```
<type>(<scope>): <subject> (<TAG>)
```

Examples in current use across the org:

- `feat(handoff-to-sidecar): codify cross-surface routing as /handoff (OHAND1)`
- `chore(sidecar): operator GO + stage OPLUG1 writeback infrastructure (OPLUG1)`
- `docs(product-engineering): canonical milestone aaa073b5 (OORCH1)`
- `chore(extended-cache-ttl): 1h-TTL helpers and beta plumbing (OCACHE1)`

## The outcome tag (TAG)

### Naming

`O<DOMAIN><NUMBER>` where:

- `O` — opensubagents-ai org prefix (distinguishes from upstream / internal tags)
- `<DOMAIN>` — 3-6 letter mnemonic for the outcome area
- `<NUMBER>` — monotonic integer within that domain, starting at 1

### Outcome tags in use (catalog)

| Tag | Domain | First commit | Description |
|-----|--------|--------------|-------------|
| `OCACHE1` | extended cache TTL | `subagentceo/knowledge-engineering@7be10b7` | 1h-TTL helpers + beta plumbing |
| `OPUSH1` | GitHub App push diagnostics | `opensubagents-ai/mcp-knowledge#1` | OAuth selector validation probe |
| `OPLUG1` | marketplace scaffolds | `opensubagents-ai/subagent-plugins@2b7a588`, `coworker-plugins@4270609` | subagent-plugins + coworker-plugins bootstrap |
| `OSKILL1` | product-engineering skill | `opensubagents-ai/skills@feda952c` | SKILL.md + 2 references + 1 example |
| `OHAND1` | handoff-to-sidecar plugin | `opensubagents-ai/subagent-plugins@f0bd8b47` | /handoff slash command |
| `OHAND2` | handoff-to-sidecar plugin | `opensubagents-ai/subagent-plugins@22ba4c25` | /complete-handoff + /audit-handoff (slash-loop completion) |
| `OORCH1` | orchestration milestone | `opensubagents-ai/skills@568fa76f` | canonical capture of claude.ai/chat/aaa073b5 |
| `OCSC1` | cross-repo change-set conventions | this commit | this file |

Future tags reserved or anticipated: `OWFL1` (shared workflows repo), `OINT1` (integration meta-repo), `OSEC1` (GHAS security campaign), `OTEL1` (Actions telemetry), `ORUL1` (branch rulesets sweep — deferred), `OPLUG2` (canonical-topics registration of new marketplaces).

### Dual role

A TAG serves two purposes simultaneously:

1. **Per-PR / per-commit identifier.** Lets you grep within a single repo to see all commits in that work:

   ```bash
   git log --grep="(OPLUG1)" --oneline
   ```

2. **Cross-repo change-set identifier.** When work spans multiple repos (e.g. OPLUG1 touched `subagent-plugins`, `coworker-plugins`, `claude-chrome-sidecar`, `skills`), the same tag appears in commits on every repo that participated. Letting any operator reconstruct the full change set with:

   ```bash
   gh search commits --owner opensubagents-ai "(OPLUG1)"
   ```

This avoids the need for a separate change-set ID system; **the commit message IS the audit trail.**

### When to allocate a new TAG vs. continue an existing one

- **New TAG:** starting work in a new outcome area (different goal, different artifacts, different success criteria)
- **Same TAG:** continuing or extending work already in flight; tail-end cleanup commits; reverts of work belonging to the original outcome
- **No reuse across distant restarts:** if you abandoned `OWFL1` last month and now want to do it again, allocate `OWFL2`. Tag reuse pollutes the change-set audit.

### Where a TAG appears

- Every commit message in the work: `<type>(<scope>): <subject> (<TAG>)`
- PR titles: should match the lead commit title
- INBOX entries: `**<TAG>:** <title>` in `claude-chrome-sidecar/tasks/INBOX.md`
- Session logs: `sessions/<NNNN>-<slug>.md` references the tag in body (optionally embed in slug)
- Milestone captures: `skills/product-engineering/examples/<slug>.md` lists the tag in its "Outcome tags spawned" section

## Conventional Commits types used in this org

| Type | When to use |
|------|-------------|
| `feat` | New user-facing functionality (plugin, slash command, MCP server, skill, agent) |
| `fix` | Bug fix |
| `chore` | Maintenance, refactor without semantic change, INBOX management, marketplace registration, dependency bump |
| `docs` | Documentation-only changes (READMEs, skills, examples, this file) |
| `refactor` | Code change that doesn't add features or fix bugs |
| `test` | Adding or updating tests |
| `ci` | CI/workflow changes. **Note:** writing to `.github/workflows/*` from the desktop GitHub App connector is currently blocked by missing `Workflows: write` scope. Route via `/handoff` (see `subagent-plugins/plugins/handoff-to-sidecar/`). |

The scope is usually the most specific component touched (plugin name, module name, service name, file name). Use lowercase, hyphenated. No camelCase. No spaces.

## Branch and PR conventions

- **Default branch:** `main`
- **Topic branches:** `<type>/<short-slug>-<TAG>`, e.g. `feat/extended-cache-ttl-OCACHE1`
- **PR titles:** match the lead commit title (including TAG suffix)
- **Merge strategy:** squash-merge into `main`. Delete branch on merge.
- **Direct-to-main allowances:**
  - `claude-chrome-sidecar/tasks/INBOX.md` updates (per sidecar charter)
  - `claude-chrome-sidecar/sessions/NNNN-*.md` writes (per sidecar charter)
  - `claude-chrome-sidecar/memory/*.md` updates (per sidecar charter)
  - `claude-chrome-sidecar/tasks/<TAG>/*` scaffolding files filed via `/handoff` (per sidecar charter)
  - All other repos go through PR.

## Anti-patterns

- ❌ Forgetting the TAG suffix — makes commits orphaned from their change set
- ❌ Using free-form TAG names like `(workflow-update)` — defeats the grep-ability
- ❌ Using the same TAG across unrelated work — pollutes the change-set audit
- ❌ Reusing TAGs across distant restarts — allocate a new number
- ❌ Putting the TAG inside the subject instead of as a suffix — breaks the search pattern (`feat(OPLUG1 plugin)` is wrong; `feat(plugin): ... (OPLUG1)` is right)
- ❌ Allocating TAGs greedily (one per commit) — a TAG is per OUTCOME, not per commit. OPLUG1 has ~15 commits across 5 repos.

## Worked example: tracing OPLUG1 across the polyrepo

Outcome `OPLUG1` ("upload lint-manifests workflow to two new marketplaces") touched these repos:

| Repo | Commits | Purpose |
|------|---------|---------|
| `subagent-plugins` | scaffold + 1 workflow blocked | marketplace #1 |
| `coworker-plugins` | scaffold + 1 workflow blocked | marketplace #2 |
| `claude-chrome-sidecar` | INBOX file + STATUS + session log + memory updates | cross-surface routing |
| `skills` | milestone capture | OORCH1 references OPLUG1 |

Reconstructing the full change set:

```bash
gh search commits --owner opensubagents-ai "(OPLUG1)" --json repository,sha,commit
```

Returns every commit across every repo with the `(OPLUG1)` suffix. Combined with the canonical session URL (`claude.ai/chat/aaa073b5-2fb6-47b4-b40b-00a2e3cfc1d0`) referenced in commit bodies, the full audit trail is reconstructable from the commits alone.

## See also

- `claude-chrome-sidecar/CLAUDE.md` — sidecar charter (direct-to-main rules, operating loop)
- `claude-chrome-sidecar/memory/decisions.md` ADR-0001 — surface-scope split
- `subagent-plugins/CONVENTIONS.md` — plugin-specific commit conventions (subset of this doc)
- `coworker-plugins/CONVENTIONS.md` — coworker plugin conventions (subset)
- `skills/skills/product-engineering/examples/agent-to-agent-orchestration-aaa073b5.md` — canonical milestone using these conventions throughout
- `subagent-plugins/plugins/handoff-to-sidecar/` — slash commands that codify the routing pattern
