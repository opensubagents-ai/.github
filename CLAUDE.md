# opensubagents-ai · canonical Claude context

This file is auto-loaded into every Claude Code session in this org. Keep it short, factual, and current.

## Org identity

- **Org**: `opensubagents-ai` under the `subagentmcp` GitHub Enterprise
- **Mission**: ship an automated business where Claude does most of the engineering and ops work, supervised through Channels and managed-agent sessions
- **Default model**: `claude-opus-4-7` for code generation and reasoning; `claude-sonnet-4-6` for routine tooling; `claude-haiku-4-5` for cheap classification

## Conventions

### Code
- TypeScript everywhere unless a repo's README says otherwise. Strict mode on.
- Lint via the repo's own config — never globally; install with the dep tree.
- Tests live next to the code they test, named `*.test.ts`.

### Commits + PRs
- Conventional Commits: `feat:`, `fix:`, `chore:`, `docs:`, `refactor:`, `test:`, `perf:`, `ci:`
- One concern per PR. Squash-merge. Delete branch on merge.
- Every PR must reference an issue or a Channel thread.

### Secrets
- Never put real secrets in code or in `.env.example`. Use GitHub Actions secrets at the org level.
- For local dev, use 1Password CLI: `op run -- <cmd>`.

## Repos at a glance

| Repo | Purpose |
|---|---|
| `sdk` | Internal TS SDK wrapping `@anthropic-ai/sdk` |
| `agent-sdk` | Internal wrapper over `@anthropic-ai/claude-agent-sdk` |
| `cli` | Spin up Claude Code sessions with our defaults preloaded |
| `mcp-monitoring` | Remote MCP for CI + observability alerts → Channels |
| `mcp-business-ops` | Remote MCP for invoices, contracts, runbooks |
| `mcp-knowledge` | Remote MCP for the company knowledge graph |
| `channels-webhook-router` | Webhook fan-in to Channels |
| `channels-monitoring` | Sentry / Datadog / Honeycomb publishers |
| `agents-orchestrator` | Managed-agent session spin-up with env, vaults, outcomes |
| `agents-rubrics` | Markdown rubrics consumed by `define_outcome` |
| `skills` | Internal Agent Skills shared across sessions |
| `audit-log` | Append-only audit DB schema + ingestion |
| `trace-collector` | OTel collector + dashboards |
| `infra` | Terraform + GitHub Actions for the org |
| `playbooks` | Runbooks Claude reads when alerted |

## How to ask Claude for something

1. State the outcome, not the steps. Claude turns outcomes into plans.
2. If urgency matters, say so. Otherwise default is "high quality, no rush."
3. Reference the repo by name, not by URL.
4. Push back on Claude's plan if it seems off — don't accept it just to be polite.

## Where to go next

- Architecture decisions: each repo's `docs/decisions/` directory (ADR format)
- Channels routing: `channels-webhook-router/README.md`
- Agent outcomes library: `agents-rubrics/README.md`
- Anything urgent: Channels `#incidents`
