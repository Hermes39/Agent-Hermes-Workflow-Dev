<p align="right">
<a href="README.md">English</a> |
<a href="README.zh-CN.md">简体中文</a> |
<a href="README.zh-TW.md">繁體中文</a> |
<a href="README.ja.md">日本語</a>
</p>
# Agent Lab Hermes

> AI Agent 运维知识库 — 工作流架构设计与可复用 Skill 模板。

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

## 概述

本仓库记录了 AI Agent 生产环境运维的实践模式：如何组织工作流、管理持久记忆、
维护版本化知识库，以及构建可复用的操作技能。

内容来源于运行 Hermes Agent 实例的实战经验，经过泛化处理，供其他 Agent 运营者
参考和适配。

## 架构

Agent 运维遵循四层设计：

```
工作流  →  技能  →  文档  →  配置
 (设计)    (执行)    (说明)    (参数)
```

| 层级 | 内容 | 受众 | 变更频率 |
|------|------|------|----------|
| **工作流** | 架构设计 — 系统做什么、为什么这样做 | 架构师、Agent | 极少 |
| **技能** | 操作流程 — 具体任务如何执行 | Agent、运维人员 | 偶尔 |
| **文档** | 设计说明 — 为什么这样设计 | 人类、新 Agent | 随技能变更 |
| **配置** | 实例配置 — 具体的参数和设置 | Agent、部署脚本 | 频繁 |

每个工作流和技能都是自包含单元，携带自己的文档、参考资料和模板。

## 工作流

| 工作流 | 说明 |
|--------|------|
| `memory-audit` | 三级审计系统（日/周/月），带反馈闭环 |

## 快速开始

1. 浏览 `workflows/` 目录，找到与你场景相关的架构设计
2. 每个工作流包含独立的 README、架构文档和模板

## 贡献

欢迎贡献。提交 Pull Request 前请先开 Issue 讨论。

## 许可证

[MIT](LICENSE)
