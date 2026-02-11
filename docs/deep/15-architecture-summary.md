# Codex App 代码架构总结报告

## 概述

本报告汇总了 Codex App 深度代码分析的所有成果。

## 系统架构图

```
┌─────────────────────────────────────────────────────────┐
│                    Codex Desktop App                     │
├─────────────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐     │
│  │   Main      │  │   Renderer  │  │   Worker    │     │
│  │   Process   │◄─┤   Process   │  │   Thread    │     │
│  │             │  │             │  │             │     │
│  │ - IPC       │  │ - React UI  │  │ - Git Ops   │     │
│  │ - SQLite    │  │ - WebView   │  │ - File Ops  │     │
│  │ - Sparkle   │  │             │  │             │     │
│  └──────┬──────┘  └─────────────┘  └─────────────┘     │
│         │                                               │
│         ▼                                               │
│  ┌─────────────────────────────────────────────────┐   │
│  │              Codex CLI (Rust)                    │   │
│  │  ┌─────────┐ ┌─────────┐ ┌─────────┐           │   │
│  │  │  Core   │ │   MCP   │ │  Cloud  │           │   │
│  │  │         │ │  Server │ │  Tasks  │           │   │
│  │  └─────────┘ └─────────┘ └─────────┘           │   │
│  └─────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────┘
```

## 核心模块依赖关系

### Electron 层
```
Main Process
├── IPC Router (codex_desktop:*)
├── SQLite DAL (better-sqlite3)
├── Sparkle Updater
├── Sentry Integration
└── Worker Thread Manager

Renderer Process
├── React UI
├── electronBridge API
└── WebSocket Client
```

### Rust CLI 层
```
codex_core
├── auth (认证)
├── exec (执行)
├── config (配置)
├── tools (工具)
└── mcp_connection_manager

codex_app_server
├── message_processor
├── dynamic_tools
└── transport
```

## 关键设计模式

### 1. 进程隔离
- Main/Renderer/Worker 三进程架构
- IPC 消息传递
- Context Bridge 安全暴露 API

### 2. 数据持久化
- SQLite 本地存储
- 版本迁移机制
- PRAGMA user_version 管理

### 3. 任务调度
- RRule 标准格式
- Automation 定时任务
- Thread 并行执行

### 4. 扩展机制
- Skills 插件系统
- MCP 协议支持
- 动态工具注册

## 可借鉴的架构设计

### 1. 多进程架构
- Worker Thread 处理耗时操作
- 避免阻塞主进程和 UI

### 2. 混合技术栈
- Electron (UI) + Rust (CLI)
- 性能敏感操作用 Rust

### 3. 协议设计
- JSON-RPC 风格消息
- 版本化 API (v1/v2)

## 文档索引

| 编号 | 文档 | 主题 |
|------|------|------|
| 01 | thread-manager.md | Thread 管理器 |
| 02 | automation-scheduler.md | Automation 调度器 |
| 03 | skills-loader.md | Skills 加载器 |
| 04 | ipc-router.md | IPC 消息路由 |
| 05 | sqlite-dal.md | SQLite 数据访问层 |
| 06 | websocket-client.md | WebSocket 客户端 |
| 07 | cli-modules.md | CLI Rust 模块 |
| 08 | mcp-protocol.md | MCP 协议 |
| 09 | oauth-flow.md | OAuth 认证流程 |
| 10 | sentry-integration.md | Sentry 监控 |
| 11 | sparkle-updater.md | Sparkle 自动更新 |
| 12 | terminal-pty.md | node-pty 终端 |
| 13 | worker-threads.md | Worker 线程 |
| 14 | cloud-tasks.md | Cloud Tasks |
| 15 | architecture-summary.md | 架构总结 |


