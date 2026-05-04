<p align="right">
<a href="README.md">English</a> |
<a href="README.zh-CN.md">简体中文</a> |
<a href="README.zh-TW.md">繁體中文</a> |
<a href="README.ja.md">日本語</a>
</p>
# Agent Lab Hermes

> Operational knowledge for AI Agents — workflow architecture designs and reusable skill templates.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

## Overview

This repository documents practical patterns for operating AI agents in production:
how to structure workflows, manage persistent memory, maintain version-controlled
knowledge, and build reusable operational skills.

It is the distilled experience of running a Hermes Agent instance — generalized so
that other agent operators can adapt these patterns to their own systems.

## Architecture

Agent operations follow a four-layer design:

```
Workflows  →  Skills  →  Docs  →  Config
 (design)    (execute)  (explain)  (values)
```

| Layer | What | Audience | Change Rate |
|-------|------|----------|-------------|
| **Workflows** | Architecture designs — what the system does and why | Architects, agents | Rarely |
| **Skills** | Operational procedures — how to do specific tasks | Agents, operators | Occasionally |
| **Docs** | Design explanations — why things work this way | Humans, new agents | On skill change |
| **Config** | Instance configuration — specific values and settings | Agents, deploy scripts | Frequently |

Each workflow and skill in this repo is a self-contained unit carrying its own
documentation, references, and templates.

## Workflows

| Workflow | Description |
|----------|-------------|
| `memory-audit` | Three-tier audit system (daily/weekly/monthly) with feedback loop |
| `agent-operations` | Agent lifecycle: initialization, documentation, self-check |
| `data-sync` | Version-controlled data with security classification |
| `communication` | Matrix bot, email, and publishing workflows |
| `infrastructure` | Server setup, gateway, permissions, monitoring |

## Skills

| Skill | Description |
|-------|-------------|
| `memory-tier-workflow` | Multi-layer memory hierarchy for knowledge persistence |
| `system-self-check` | Full system diagnostic health check |
| `hermes-data-git` | Secure version control for agent data |
| `agent-init-documentation` | Automated documentation generation |
| `container-permission-watcher` | Real-time permission conflict resolution |

## Quick Start

1. Browse the `workflows/` directory for architecture designs relevant to your setup
2. Browse the `skills/` directory for operational procedures you can adapt
3. Each workflow and skill includes its own README with setup instructions

## Documentation

- [Getting Started](docs/getting-started.md) — Setup guide
- [Architecture](docs/architecture.md) — Detailed four-layer architecture explanation

## Contributing

Contributions are welcome. Please open an issue to discuss proposed changes before
submitting a pull request.

## License

[MIT](LICENSE)
