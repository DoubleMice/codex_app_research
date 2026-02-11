# 竞品对比分析 - Claude Code

## 产品概述

| 项目 | Codex App | Claude Code |
|------|-----------|-------------|
| 开发商 | OpenAI | Anthropic |
| 类型 | 桌面应用 | CLI 工具 |
| 平台 | macOS | macOS, Linux, Windows |

## 核心功能对比

| 功能 | Codex App | Claude Code |
|------|-----------|-------------|
| 多代理 | Thread 并行 | Subagent 系统 |
| 自动化 | Automation + RRule | Hooks 系统 |
| 扩展性 | Skills | MCP + 自定义命令 |

## 架构设计对比

### Codex App
- Electron 桌面应用
- 独立 CLI 二进制
- SQLite 本地存储
- Thread 并行执行

### Claude Code
- 原生 CLI 工具
- MCP 协议扩展
- Subagent 分层系统
- CLAUDE.md 配置文件

## 用户体验对比

| 方面 | Codex App | Claude Code |
|------|-----------|-------------|
| 界面 | GUI 图形界面 | 终端命令行 |
| 学习曲线 | 较低 | 中等 |
| 定制化 | Skills 系统 | Hooks + MCP |

## 优劣势分析

### Codex App 优势
- 图形界面更直观
- 内置 Git 工具
- 多任务可视化管理

### Claude Code 优势
- 跨平台支持更广
- MCP 扩展性强
- 终端集成更深

## 参考来源

- [Claude 官网](https://claude.com)
- [Medium](https://medium.com)
- [Dev.to](https://dev.to)
