# opensubagents-ai

**An automated business org running on Claude Code, managed agents, and remote MCP.**

Part of the [`subagentmcp`](https://github.com/enterprises/subagentmcp) enterprise. Every repo here is built, reviewed, and maintained by Claude — orchestrated by humans via [Claude Code Channels](https://code.claude.com/docs/en/channels) and [managed agents](https://platform.claude.com/docs/en/managed-agents).

## What lives here

| Layer | Repos |
|---|---|
| **SDK** | [`sdk`](../../sdk) · [`agent-sdk`](../../agent-sdk) · [`cli`](../../cli) |
| **Remote MCP servers** | [`mcp-monitoring`](../../mcp-monitoring) · [`mcp-business-ops`](../../mcp-business-ops) · [`mcp-knowledge`](../../mcp-knowledge) |
| **Channels** | [`channels-webhook-router`](../../channels-webhook-router) · [`channels-monitoring`](../../channels-monitoring) |
| **Agents** | [`agents-orchestrator`](../../agents-orchestrator) · [`agents-rubrics`](../../agents-rubrics) |
| **Knowledge** | [`skills`](../../skills) · [`playbooks`](../../playbooks) |
| **Observability** | [`audit-log`](../../audit-log) · [`trace-collector`](../../trace-collector) |
| **Infrastructure** | [`infra`](../../infra) |

## How to work here

Clone the repo you care about, then start a Claude Code session — `CLAUDE.md` (in this repo's root) is auto-loaded into every session in this org and orients Claude to our conventions.

```bash
git clone git@github.com:opensubagents-ai/<repo>.git
cd <repo>
claude
```

## Governance

- **Owners**: admin-jadecli, alex-jadecli, zhoukalex
- **PR policy**: squash-merge only, delete branch on merge, all required CI green before merge
- **Secrets**: org-level via GitHub Actions secrets; rotated quarterly
- **Incident response**: routes through [`playbooks`](../../playbooks) and Channels
