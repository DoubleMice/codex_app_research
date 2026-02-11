# Codex App 逆向分析研究项目

## 项目概述

本项目对 OpenAI Codex Desktop App 进行了深度逆向分析，涵盖产品功能、技术架构、代码实现等多个维度。

## 研究对象

**OpenAI Codex App** - macOS 桌面应用程序
- **版本**: 260208.1016 (构建号 571)
- **架构**: Electron 40.0.0 + Rust CLI 混合架构
- **模型**: GPT-5.2-Codex

## 核心发现

- **多代理工作流**: Thread 管理器支持会话分叉和回滚
- **Skills 系统**: 支持 SKILL.md + openai.yaml 配置
- **沙箱安全**: macOS Seatbelt + Linux Landlock
- **IPC 通信**: 40+ codex_desktop:* 通道
- **MCP 协议**: 支持外部工具集成

## 研究目标

1. **架构分析**: 研究 Codex App 的整体架构设计
2. **代理系统**: 分析多代理编排和协调机制
3. **技能系统**: 理解 Skills 的设计和扩展模式
4. **集成方式**: 研究与 CLI/IDE 的集成实现
5. **任务调度**: 分析自动化任务的调度机制

## 文档结构

```
docs/
├── codex-app-analysis-report.md  # 完整分析报告 (44章)
├── basic/                         # 产品研究 (15篇)
│   ├── 01-product-overview.md
│   ├── 02-multi-agent-workflow.md
│   └── ...
└── deep/                          # 深度逆向分析 (43篇)
    ├── 01-thread-manager.md
    ├── 25-main-js-reverse.md
    └── ...
```

## 快速导航

### 产品研究 (docs/basic/)

| 文档 | 说明 |
|------|------|
| 01-product-overview | 产品概述 |
| 02-multi-agent-workflow | 多代理工作流 |
| 03-skills-system | Skills 系统 |
| 04-integrations | 集成方式 |
| 05-task-scheduling | 任务调度 |
| 06-comparison-claude-code | 对比 Claude Code |
| 07-comparison-copilot | 对比 GitHub Copilot |
| 08-comparison-cursor | 对比 Cursor |
| 09-feature-matrix | 功能矩阵 |
| 10-electron-architecture | Electron 架构 |
| 11-cli-binary-analysis | CLI 二进制分析 |
| 12-javascript-analysis | JavaScript 分析 |
| 13-data-storage | 数据存储 |
| 14-network-api | 网络 API |
| 15-research-summary | 研究总结 |

### 深度逆向分析 (docs/deep/)

| 文档 | 说明 |
|------|------|
| 01-thread-manager | Thread 管理器 |
| 02-automation-scheduler | 自动化调度器 |
| 03-skills-loader | Skills 加载器 |
| 04-ipc-router | IPC 消息路由 |
| 05-sqlite-dal | SQLite 数据层 |
| 06-websocket-client | WebSocket 客户端 |
| 07-cli-modules | CLI 模块 |
| 08-mcp-protocol | MCP 协议 |
| 09-oauth-flow | OAuth 认证流程 |
| 10-sentry-integration | Sentry 集成 |
| 11-sparkle-updater | Sparkle 更新器 |
| 12-terminal-pty | 终端 PTY |
| 13-worker-threads | Worker 线程 |
| 14-cloud-tasks | Cloud Tasks |
| 15-architecture-summary | 架构总结 |
| 16-architecture-deep | 深度架构分析 |
| 17-security-analysis | 安全机制分析 |
| 18-protocol-analysis | 协议分析 |
| 19-tool-system | 工具系统 |
| 20-config-system | 配置系统 |
| 21-prompt-engineering | Prompt 工程 |
| 22-error-handling | 错误处理 |
| 23-state-machine | 状态机 |
| 24-data-flow | 数据流 |
| 25-main-js-reverse | 主进程 JS 逆向 |
| 26-worker-js-reverse | Worker JS 逆向 |
| 27-preload-reverse | Preload 逆向 |
| 28-cli-binary-reverse | CLI 二进制逆向 |
| 29-sparkle-native-reverse | Sparkle 原生模块 |
| 30-pty-native-reverse | PTY 原生模块 |
| 31-sqlite-native-reverse | SQLite 原生模块 |
| 32-js-obfuscation | JS 混淆分析 |
| 33-protocol-reverse | 协议消息逆向 |
| 34-seatbelt-reverse | Seatbelt 沙箱 |
| 35-dependencies | 依赖分析 |
| 36-renderer-entry | 渲染进程入口 |
| 37-hardcoded-urls | 硬编码 URL |
| 38-electron-config | Electron 配置 |
| 39-webview-html | WebView HTML |
| 40-node-pty-source | node-pty 源码 |
| 41-environment-vars | 环境变量 |
| 42-appcast-analysis | Appcast 分析 |
| 43-cli-commands | CLI 命令 |

## 项目状态

✅ 研究完成 (2026-02-10)
