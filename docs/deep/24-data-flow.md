# 数据流分析

## 概述

Codex App 的数据流涉及多个进程和传输层之间的消息传递。

## 1. 用户输入流

### 输入路径

```
User Input → Renderer (React) → IPC → Main Process → CLI → OpenAI API
```

### 消息类型

| 类型 | 说明 |
|------|------|
| UserMessageEvent | 用户消息事件 |
| SendUserMessage | 发送用户消息 |
| SendUserTurn | 发送用户回合 |

## 2. AI 响应流

### 响应路径

```
OpenAI API (SSE) → CLI → Main Process → IPC → Renderer → UI Update
```

### 增量事件

| 事件 | 说明 |
|------|------|
| AgentMessageDelta | Agent 消息增量 |
| CommandExecutionOutputDelta | 命令输出增量 |
| FileChangeOutputDelta | 文件变更增量 |
| ReasoningSummaryTextDelta | 推理摘要增量 |

## 3. 传输层

### WebSocket

```javascript
// 实时双向通信
ws.send(JSON.stringify(message));
ws.onmessage = (event) => { ... };
```

### SSE (Server-Sent Events)

```
// AI 响应流
OpenAI API → SSE Stream → CLI
```

### stdio

```
// CLI 进程通信
stdin → Codex CLI → stdout
```

## 4. IPC 消息流

### 通道定义

| 通道 | 方向 | 说明 |
|------|------|------|
| message-from-view | Renderer → Main | 渲染进程到主进程 |
| message-for-view | Main → Renderer | 主进程到渲染进程 |

### Worker 通信

| 消息类型 | 说明 |
|----------|------|
| worker-request | 工作请求 |
| worker-response | 工作响应 |
| worker-request-cancel | 取消请求 |

## 5. 数据流架构图

```
┌─────────────────────────────────────────────────────────────┐
│                        User Interface                        │
│                      (React Renderer)                        │
└─────────────────────────┬───────────────────────────────────┘
                          │ IPC
                          ▼
┌─────────────────────────────────────────────────────────────┐
│                      Main Process                            │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐         │
│  │   SQLite    │  │   Worker    │  │  App Server │         │
│  │     DAL     │  │   Thread    │  │   Client    │         │
│  └─────────────┘  └─────────────┘  └──────┬──────┘         │
└────────────────────────────────────────────┼────────────────┘
                                             │ WebSocket/stdio
                                             ▼
┌─────────────────────────────────────────────────────────────┐
│                      Codex CLI (Rust)                        │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐         │
│  │    Core     │  │     MCP     │  │   Sandbox   │         │
│  └─────────────┘  └─────────────┘  └─────────────┘         │
└─────────────────────────┬───────────────────────────────────┘
                          │ HTTPS/SSE
                          ▼
┌─────────────────────────────────────────────────────────────┐
│                      OpenAI API                              │
└─────────────────────────────────────────────────────────────┘
```

