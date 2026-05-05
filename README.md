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

## Quick Start

1. Browse the `workflows/` directory for architecture designs relevant to your setup
2. Each workflow includes its own README, architecture document, and templates

## Contributing

Contributions are welcome. Please open an issue to discuss proposed changes before
submitting a pull request.

## License

[MIT](LICENSE)
=======
