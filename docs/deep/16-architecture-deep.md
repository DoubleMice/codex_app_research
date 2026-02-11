# Codex App 架构深度分析补充

## 1. 进程通信架构

### 通信拓扑
```
┌─────────────┐     IPC      ┌─────────────┐
│   Main      │◄────────────►│  Renderer   │
│   Process   │              │   Process   │
└──────┬──────┘              └─────────────┘
       │
       │ Worker Threads
       ▼
┌─────────────┐
│   Worker    │
│   (Git)     │
└─────────────┘
       │
       │ Child Process
       ▼
┌─────────────┐
│  Codex CLI  │
│   (Rust)    │
└─────────────┘
```

### 通信协议层次
```
Application Layer:  JSON Messages
Transport Layer:    IPC / WebSocket / stdio
```

## 2. 数据流架构

### 用户输入流
```
User Input
    │
    ▼
Renderer (React)
    │ IPC: message-from-view
    ▼
Main Process
    │ stdio/WebSocket
    ▼
Codex CLI
    │ HTTP/WebSocket
    ▼
OpenAI API
```

### AI 响应流
```
OpenAI API (SSE)
    │
    ▼
Codex CLI (Stream Parser)
    │ stdout events
    ▼
Main Process
    │ IPC: message-for-view
    ▼
Renderer (React State)
    │
    ▼
UI Update
```

## 3. 状态管理架构

### 持久化状态
```
SQLite (Main Process)
├── automations      # 定时任务
├── automation_runs  # 执行记录
└── inbox_items      # 收件箱
```

### 运行时状态
```
Main Process Memory
├── Thread Sessions  # 活跃线程
├── PTY Sessions     # 终端会话
└── Worker Refs      # Worker 引用

Renderer Memory
├── React State      # UI 状态
└── Message Queue    # 消息队列
```

## 4. 扩展架构

### Skills 扩展点
```
~/.codex/skills/
└── <skill-name>/
    ├── SKILL.md       # 元数据
    └── agents/
        └── openai.yaml # Agent 配置
```

### MCP 扩展点
```
MCP Server Registry
├── Built-in Tools
├── Dynamic Tools (runtime)
└── External MCP Servers
```

## 5. 隔离架构

### 进程隔离
```
┌─────────────────────────────────┐
│  Electron Sandbox               │
│  ├── contextIsolation: true     │
│  └── nodeIntegration: false     │
└─────────────────────────────────┘
```

### 文件系统隔离
```
macOS: Seatbelt (sandbox-exec)
Linux: Landlock + seccomp
Windows: Restricted Token
```

### Git Worktree 隔离
```
每个 Thread 独立 Worktree
├── 避免工作目录冲突
├── 支持并行任务
└── 快照和回滚
```

## 6. 架构设计原则

### 关注点分离
| 层 | 职责 |
|---|---|
| UI | React 渲染 |
| IPC | 消息路由 |
| Business | Main Process |
| Compute | Worker/CLI |

### 性能优化策略
- Worker Thread 处理 Git 操作
- Rust CLI 处理 AI 交互
- 动态导入 (node-pty)
- 消息批处理

## 7. 架构总结

### 核心架构特点
1. **混合技术栈** - Electron + Rust
2. **多进程模型** - Main/Renderer/Worker
3. **插件化设计** - Skills + MCP
4. **沙箱隔离** - 多层安全机制

### 可借鉴点
- Worker 分离耗时操作
- Rust 处理性能敏感逻辑
- Git Worktree 实现任务隔离
- 版本化数据库迁移
